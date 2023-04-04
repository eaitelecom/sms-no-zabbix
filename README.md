Integração de SMS no Zabbix
===========================

Este é um guia passo a passo para a integração de SMS no Zabbix, utilizando um script de configuração e um arquivo de log.

Arquivo de Log
--------------

Antes de configurar o script de envio de SMS, é necessário criar um arquivo de log. Para isso, execute os seguintes comandos no terminal:

```
sudo mkdir -p /var/run/zabbix/log/ 
sudo touch /var/run/zabbix/log/log.out 
sudo chmod +x /var/run/zabbix/log/log.out 
sudo chown zabbix:zabbix /var/run/zabbix/log/log.out
```

Script de Configuração
----------------------

Agora, é necessário criar um script de configuração para envio de SMS. Para isso, execute os seguintes comandos no terminal:

```
sudo touch /usr/lib/zabbix/alertscripts/send_sms.sh 
sudo chmod +x /usr/lib/zabbix/alertscripts/send_sms.sh 
sudo chown zabbix:zabbix /usr/lib/zabbix/alertscripts/send_sms.sh
```

Copie o código abaixo e cole no arquivo criado `/usr/lib/zabbix/alertscripts/send_sms.sh`.

```
#!/bin/sh
#
# Script to send SMS alert through Zabbix
#
exec 3>&1 4>&2
trap 'exec 2>&4 1>&3' 0 1 2 3
exec 1>>/var/run/zabbix/log/log.out 2>&1

# Credentials
APIKEY=ENTER_YOUR_KEY_HERE
PHONE=${1}
MESSAGE=${2}

# Execution date
date

# GET with encoded URL
curl -G "http://api.sms.eai.net.br/v1/message?recipient=$PHONE&key=$APIKEY" --data-urlencode "text=$MESSAGE" -s

# Output
echo " SMS sent to $1"
echo " MESSAGE: $2"

```

Neste script, é necessário substituir o valor `ENTER_YOUR_KEY_HERE` pelo token de autenticação da API de SMS que você utilizará.

Agora, basta configurar o Zabbix para utilizar o script de envio de SMS em suas ações de alerta.

Utilização
----------

Para utilizar o script de envio de SMS, basta executá-lo no terminal, informando o número do celular e a mensagem que deve ser enviada. Por exemplo:

```
./send_sms.sh 555199999999 "Teste de envio de SMS pelo Zabbix"
```

O comando acima enviará uma mensagem para o número `555199999999` com o texto `Teste de envio de SMS pelo Zabbix`.

É possível deixar o código de país estático na URL, em vez de precisar passar todos os números com o código de país. Por exemplo, se você está enviando mensagens apenas para números de telefone brasileiros, pode deixar o código do país como 55 na URL.

Para fazer isso, substitua a linha `curl` no script de configuração por esta:

```
curl -G "http://api.sms.eai.net.br/v1/message?recipient=55${PHONE}&key=$APIKEY" --data-urlencode "text=$MESSAGE" -s
```

Dessa forma, basta passar o número de telefone sem o código do país na chamada do script, por exemplo:

```
./send_sms.sh 1999999999 "Teste de envio de SMS pelo Zabbix"
```

O número `1999999999` será adicionado automaticamente com o código de país `55` na URL do envio de SMS.

Contribuição
------------

Contribuições são bem-vindas! Se você encontrar algum problema ou tiver sugestões para melhorar a aplicação, sinta-se à vontade para abrir uma issue ou enviar um pull request.

Licença
-------

Este projeto está licenciado sob a Licença MIT. Consulte o arquivo LICENSE para obter mais informações.
