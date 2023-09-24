
# Trabalho realizado nas Semanas #2 e #3

## Identificação

- Esta vulnerabilidade é usualmente denominada de “Authentication Bypass Using an Alternate Path or Channel”.
- A autenticação através das redes sociais na aplicação do WordPress até a versão 7.6.4, permitia aos utilizadores contornar a mesma.
- Esta falha existia devido à falha na encriptação das credenciais no momento do registo.
-  Isto permitia aos atacantes entrarem na conta de outro utilizador sabendo unicamente o email do mesmo podendo se apoderar de uma conta de um administrador .

## Catalogação

- Esta vulnerabilidade foi descoberta a 
2023/05/27 por Lana Codes-Wordfence, mais especificamente István Márton.
('https://lana.codes/lanavdb/2326f41f-a39f-4fde-8627-9d29fff91443/')
- Tem um nível de gravidade de 9.8. 
('https://www.wordfence.com/threat-intel/vulnerabilities/wordpress-plugins/miniorange-login-openid/wordpress-social-login-and-register-discord-google-twitter-linkedin-764-authentication-bypass')

## Exploit

- O exploit presente toma vantagem da vulnerabilidade authentication bypass, desencriptografando o email com uma chave estática, permitindo o login.
- O plugin utiliza uma chave de desencriptação estática e fácil de ser descoberta ('jMj7MEdu4wkHObiD').
- Torna-se possível obter os cookies de resposta do servidor (Cookie Theft), que têm a informação da autenticação e da sessão.
- Desta forma, consegue-se acesso ao website com os privilégios do utilizador.

## Ataques

- Como outro exemplo desta vulnerabilidade temos a CVE-1999-1454.
- Nesta, caso o atacante ao clicar na tecla ESC durante a autenticação, conseguia entrar numa conta sem saber a respetiva password.
- Um ataque a este tipo de vulnerabilidade tem elevado potencial de risco para os utilizadores já que consegue se apoderar de qualquer conta, inclusive de administradores.
