# LOGBOOK11 - PKI
## Task 1 - Becoming a certificate authority
<div <div align="justify">
<p>
Para esta tarefa é nos pedido para criarmos um self-signed certificate para nos podermos tornar numa CA:
</p>

<p>
Para isso começámos por copiar o openssl.conf que estava em /usr/lib/ssl/openssl.cnf. Alterando-o para permitir a criação de certificados com o mesmo tema, adicionalmente também criamos o serial com unicamente o número 1000 escrito e o index.txt que é deixado vazio
</p>

```bash
touch index.txt
echo "1000" >> serial
```

<p>
Criamos o nosso CA usando este comando:
</p>

```bash
openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 \
-keyout ca.key -out ca.crt
```

<p>
e foram dados estes com parametros comforme prompted:
    - passphrase (dees)
    - nome do país (PT)
    - região (Porto)
    - cidade (Porto)
    - organização (UP)
    - secção (FEUP)
    - nome (L11G06)
    - email (blank)
</p>

<p>
Por fim, usamos estes comandos para conseguirmos decriptar e ler o certificado e a key:
</p>

```bash
openssl x509 -in ca.crt -text -noout
openssl rsa -in ca.key -text -noout
```

<p>
Usando a informação presente nos ficheiros respondemos às seguintes perguntas:
</p>

<p>
• What part of the certificate indicates this is a CA’s certificate?
O valor de CA está a TRUE logo podemos assegurarnos que o ficheiro trata se de um CA's certificate.
</p>

![](uploads/logbook11P1.png)

<p>
• What part of the certificate indicates this is a self-signed certificate?
O campo issuer e o campo subject são exatamente iguais.
</p>

![](uploads/logbook11P2.png)

<p>
• In the RSA algorithm, we have a public exponent e, a private exponent d, a modulus n, and two secret
numbers p and q, such that n = pq. Please identify the values for these elements in your certificate
and key files.
</p>

<p>
public exponent:
</p>

![](uploads/logbook11P3.png)

<p>
private exponent:
</p>

![](uploads/logbook11P4.png)

<p>
modulus:
</p>

![](uploads/logbook11P5.png)

<p>
secret number p:
</p>

![](uploads/logbook11P6.png)

<p>
secret number q::
</p>

![](uploads/logbook11P7.png)

<p>
n (p*q):
</p>

![](uploads/logbook11P8.png)

## Task 2: Generating a Certificate Request for Your Web Server

Usando o seguinte comando criamos ocertificado para o nosso site ficticio, neste caso, `www.bank32.com`

```bash
openssl req -newkey rsa:2048 -sha256 \
-keyout server.key -out server.csr \
-subj "/CN=www.bank32.com/O=Bank32 Inc./C=US" \
-passout pass:dees \
-addext "subjectAltName = DNS:www.bank32.com,DNS:www.bank32A.com,DNS:www.bank32B.com"
```
<p>
Obtendo assim o ficheiro RSA e o certificado do nosso site.
</p>

## Task 3: Generating a Certificate for your server

<p>
Usando o comando nano descomentamos a linha "# Extension copying option: use with caution.
copy_extensions = copy"
do ficheiro openssl.cnf que nós copiamos.
</p>

<p>
De seguida, geramos um certificado para o nosso servidor e todos os seus nomes alternativos:
</p>

```bash
openssl ca -config myCA_openssl.cnf -policy policy_anything \
-md sha256 -days 3650 \
-in server.csr -out server.crt -batch \
-cert ca.crt -keyfile ca.key
```
![](uploads/logbook11P9.png)
<p>
Ao correr o seguinte comando são gerados 2 ficheiros o server.crt e server.csr.
</p>

## Task 4 - Deploying Certificate in an Apache-Based HTTPS Website

for this task we started by setting up the `bank32_apache_ssl.conf` with the following parameters, changing the certificate and key file to the ones we have created:

![](uploads/logbook11P10.png)

<p>
Iniciamos então o servidor:
</p>

```bash
service apache2 start
```

<p>
e abrimos o site:
</p>

![](uploads/logbook11P11.png)

<p>
como podemos ver o site é considerado inseguro, adicionamos então o nosso certificado CA à lista de autoridades do Firefox, em about:preferences#privacy -> Certificates -> View Certificates -> Authorities -> Import, depois de fazermos isso podemos ver que o nosso site passou a ser assinado pelo nosso self-signed certificate:
</p>

![](uploads/logbook11P12.png)

## Task 5 - Task 5: Launching a Man-In-The-Middle Attack
### Step 1/2 - Setting up the malicious website and becoming the man in the middle

Começámos por alterar o `bank32_apache_ssl.conf` para ele passar a incluir o `example.com` e adicionamos a linha `10.9.0.80 www.example.com`
ao nosso ficheiro `etc/hosts` para emularmos o resultado de um cache poisoning attack.

![](uploads/logbook11P14.png)

### Step 3 - Browse the target website

Voltamos a iniciar o nosso servidor e podemos de seguida ver o site `example.com`:

![](uploads/logbook11P15.png)

Como o site foi criado sem nenhuma alteração ao certificado de server que tinhamos usado anteriormente, o site `example.com` é considerado inseguro pois ele não faz parte do certificado.
