# LOGBOOK5 - Buffer-Overflow Attack Lab - Set-UID Version
# LOGBOOK5 - CTF
## Desaio 1
>Existe algum ficheiro que é aberto e lido pelo programa?
<p> Sim, o ficheiro mem.txt é aberto e lido pelo programa. </p>

>Existe alguma forma de controlar o ficheiro que é aberto?
<div align="justify">
<p> Sim, é possível controlar o ficheiro que é aberto, pois o programa lê o nome do ficheiro a abrir apartir do que se encontra no buffer. Se conseguirmos alterar esse conteúdo conseguimos alterar o ficheiro aberto. </p>
</div>

>Existe algum buffer-overflow? Se sim, o que é que podes fazer?
<div align="justify">
<p> Sim, existe um buffer-overflow. Podemos alterar o conteúdo do buffer para o nome do ficheiro que queremos abrir. Considerando que a função 'scanf("%40s", &buffer)' está a ler os primeiros 40 caracteres para o buffer e este tem tamanho 32, é possível criar um 'input' que dê overflow ao buffer de forma a que o programa abra o ficheiro desejado.</p></div>

>Obtenção da flag
<div align="justify">
<p>De forma a conseguirmos obter a flag, sabiamos que tinhamos de dar overflow ao buffer de forma a que o programa abrisse o ficheiro flag.txt. Para isso, criamos um input de tamanho 40, sendo os primeiros 32 caracteres um 'dummy input' como por exemplo 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa' e os restantes 8 caracteres o nome do ficheiro desejado que neste caso é 'flag.txt. Assim, inserindo o input 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaflag.txt', occore um buffer overflow e o ficheiro lido passa a ser o 'flag.txt' o que consequentemente mostra que a flag é: flag{66fe1e5da54e206be20c0439e961a513}</p>
</p>
</div>

## Desaio 2
>Que alterações foram feitas?
Foram feitas as seguintes alterações:
- Um novo array 'val' de tamanho 4 caracteres foi adicionado com o valor 0xdeadbeef;
- O array 'meme_file' foi alterado para um comprimento de 9 caracteres e foi inicializado com mais um 'null terminator';
- A função 'scanf("%40s", &buffer)' foi alterada para 'scanf("%45s", &buffer)';
- Antes de abrir o ficheiro, o program verifica se o valor de 'val' é igual a 0xfefc2324. Se for, o ficheiro é aberto, caso contrário, o programa termina.

>Mitigam na totalidade o problema?
<div align="justify">
<p> Não, as alterações não mitigam na totalidade o problema. Apesar de o programa verificar se o valor de 'val' é igual a 0xfefc2324, o que impede que o ficheiro seja aberto, o programa continua a ler o nome do ficheiro a abrir do buffer, o que permite que o buffer seja overflowed e que o programa abra um ficheiro diferente do que é suposto. </p>
</div>

>É possível ultrapassar a mitigação usando uma técnica similar à que foi utilizada anteriormente?
<div align="justify">
<p> Sim, é possível ultrapassar a mitigação usando uma técnica similar à que foi utilizada anteriormente, tal como foi explicado na questão anterior. </p>
</div>

>Obtenção da flag
<div align="justify">
<p>Após termos analisado o script python, percebemos que a função 'sendline' enviava o input que desejavamos. Assim, tal como foi feito no desafio 1, tivemos que construir um input da seguinte forma: 'dummy input' de tamanho 32 + 0xfefc2324 em código ASCII e invertido pois o sistema é 'little-endian' + 'flag.txt'. Com isto, o input que foi colocado na função 'sendline' foi o seguinte: "abcabcabcabcabcabcabcabcabcabcab$#üþflag.txt". Após executar o script, fomos apresentado com a flag: flag{7d91f1369e6f3161e44445e6207f2290}</p>

```Python
#!/usr/bin/python3
from pwn import *

DEBUG = False

if DEBUG:
    r = process('./program')
else:
    r = remote('ctf-fsi.fe.up.pt', 4000)

r.recvuntil(b":")
r.sendline("abcabcabcabcabcabcabcabcabcabcab$#üþflag.txt")
r.interactive()
```
</div>