# Zabbix-Notification-Telegram

Sending notification from Zabbix server to the Telegram.

[Plans](#Plans)

[Installation](#Installation)

[Configuration](#Configuration)

### Key Features
- [x] Sending graphs and latest values **In same message**
- [x] Flexible template configuration in message body.
- [x] Обработка быстрых команд ботом <img alt="AppVeyor" src="https://img.shields.io/static/v1?label=status&message=beta&color=yellow?logo=appveyor">
- [x] Отправка показателей по запросу от бота <img alt="AppVeyor" src="https://img.shields.io/static/v1?label=status&message=beta&color=yellow?logo=appveyor">
- [x] Передача данных из экшена XML разметкой
- [x] Формирование списка линков в теле сообщения
- [x] Формирование списка тэгов в теле сообщения
- [x] Emoji мапинг статуса и важности события
- [x] Adding watermark on images.
- [x] Формирование и обновление кэшфайла (privat, group -> supergroup)


<a name="Plans"><h3>Plans</h2></a>
- Gitlab CI\CD

<a name="Installation"><h3>Installation</h2></a>

* Login as user who will be run script. By default, Zabbix server run all scripts under user 'zabbix'. You can check what user is using now by running 'ps axu|grep zabbix' 
```bash
sudo su - zabbix
```

* Chage directory to default alertscript directory.
```bash
cd /usr/lib/zabbix/alertscripts/
```

* Get latest version of the bot from GitHub.
```bash
git clone https://github.com/xxsokolov/Zabbix-Notification-Telegram.git .
```

* Create virtual environment
```bash
virtualenv venv --python=python3
``` 
or 
```bash
python3 -m venv venv
``` 

* Activate virtual environment that was created
```bash
source venv/bin/activate
```

* Install all required library 
```bash
pip install -r .requirements
deactivate
```

* Creating own configuration from example configuration file:
```bash
cp zbxTelegram_config.example.py zbxTelegram_config.py
```

* Set right ownership for files to avoid any permission issue.
```bash
chown -R zabbix:zabbix zbxTelegram.py zbxTelegram_config.py zbxTelegram_files/ 
```

* Set execution permission
```bash
chmod +x zbxTelegram.py
```

* Open configuration file and adjust all settings that you need.
```bash
vim zbxTelegram_config.py
```
 
 
<a name="Configuration"><h3>Configuration</h2></a>


**Settings that can be changed in zbxTelegram_config.py**


`tg_proxy` = Sending notification thru proxy True/False; By default 'True'

`tg_proxy_server`  = Proxy server address.

`tg_token` = Telegram API  token.

`zabbix_api_url` = Zabbix server URL. '/' in the end is mandatory. For example if you are using default Zabbix installation you URL will be like this 'http://zabbix-server-name.example/zabbix/'

`zabbix_api_login` = User that will be used for integration. User should have at least read permission on group/object to get notification from it.

`zabbix_api_pass` = Password for user


**Zabbix Server Media types configuration**
To make notification work you need crate your own 'Media type'. Go to Zabbix Server web page, then navigate to Administration -> Media type. 
And create new 'Media type' with same settings as described below:
_Name_: ZNT

_Type_: Script

_Script name_: zbxTelegram.py

_Script parameters_:

`{ALERT.SENDTO}`

`{ALERT.SUBJECT}`

`{ALERT.MESSAGE}`


**Настройка Actions**


* Default subject

`{Problem} {TRIGGER.SEVERITY} {{TRIGGER.SEVERITY}}: {EVENT.NAME}`

`{Resolved} {TRIGGER.SEVERITY} {{TRIGGER.SEVERITY}} {EVENT.NAME}`

`{Update} {TRIGGER.SEVERITY} {{TRIGGER.SEVERITY}} {EVENT.NAME}`

`{Problem}` - мапинг значений Problem\Resolved\Update в emoji (config: zabbix_status_emoji_map)

`{{TRIGGER.SEVERITY}}` - мапинг значений Severity в emoji (config: zabbix_status_emoji_map)

* Default message

For notification configuration XML is used to configure alerts _(Examplse can be find in file actions.example)_
For start you can just copy content of the _actions.example_ file directly to the `Default message` field

Default message filed contain next section:

```xml
<body>
   <messages>
      Message text
   </messages>
</body>
``` 

[Settings description](#Settings description) 
```xml
<settings> 
       Settings description.
</settings>
``` 

<img src="https://imgur.com/m6DosDL.png">

**Test your setup**

* From server console
```bash
./zbxTelegram.py @username test test

[2019-11-26 11:48:37,723] - PID:73794 - main() - zbxTelegram.py:311 - INFO: Send to @username action: test
[2019-11-26 11:48:38,653] - PID:73794 - send_messages() - zbxTelegram.py:290 - INFO: Send photo to @username (00000000)
```
* FRom Zabbix Web interface (Media type)
Use test in subject and in message field.

<img src="https://imgur.com/6ej0d40.png">

<a name="Settings description"><h3>Settings description</h2></a>

`<graphs>True</graphs>` - Add graph to the message. Value type (True|False)

`<graphlinks>True</graphlinks>` - URL link to history page. Value type (True|False)

`<triggerlinks>True</triggerlinks>` - URL link to the triger page. Value type (True|False)

`<tag>True</tag>` - Add tag to the message. Value type (True|False)

`<graphs_period></graphs_period>` - Graph time frame. (in seconds)

`<itemid></itemid>` - Add item ID to the message. {ITEM.ID1}

`<triggerid></triggerid>` - Add trigger ID to the message. {TRIGGER.ID}

`<eventid></eventid>`- Add event ID to the message. {EVENT.ID}

`<title></title>` - Graph name. {HOST.HOST} - {EVENT.NAME}

`<triggerurl></triggerurl>` - Send URL from trigger {TRIGGER.URL}

`<tags></tags>` - Send tags list from trigger {EVENT.TAGS}


#### Sample notification:

<img src="https://imgur.com/ayKo62v.png">

###### Official Telegram channel where you can get support **Telegram**: https://t.me/ZbxNTg
