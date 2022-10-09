
# Мониторниг Cosmos-ноды? Легко!

### **HAQQ + TenderDuty**
Самый легкий инструмент мониторинга в настройке, который я нашел!  
Данная инструкция без воды и сахара, для моментальной активации мониторинга своего валидатора в сети HAQQ.  
Естественно данный инструмент обладает куда большим функционалом и вы можете настроить его под свои нужды.  
[Читать документацию](https://github.com/blockpane/tenderduty/tree/main/docs) .

Приступим!

#### Компилируем бинарник:
```shell
cd
git clone https://github.com/blockpane/tenderduty
cd tenderduty
go install
cd
```

#### Создаем файл конфигурации. 
##### не забудьте подставить свои значения:
```shell
mkdir $HOME/.tenderduty &&\

tee $HOME/.tenderduty/config.yml << EOF
enable_dashboard: yes
listen_port: 8888
hide_logs: yes
node_down_alert_minutes: 1
prometheus_enabled: yes
prometheus_listen_port: 28686

discord:
  enabled: yes
  # в discord создать свой сервер => настройки сервера => интеграция => посмотреть вебхуки => скопировать УРЛ и вставить ниже
  webhook: https://discordapp.com/api/webhooks/1028019418904670248/MPaZwN5TE2xyTcSazVetcyHvPeFmLfvLYPSmYWb6Ak4ARl0yu7htJm

chains:

  "haqq":
    chain_id: haqq_54211-2
    # ваш валидатор
    valoper_address: haqqvaloper1ylj36axpu99p9aanetr4c9ltfhd97yym8xzdq5
    public_fallback: yes

    alerts:
      stalled_enabled: yes
      stalled_minutes: 10
      consecutive_enabled: yes
      consecutive_missed: 5
      consecutive_priority: critical
      percentage_enabled: yes
      percentage_missed: 50
      percentage_priority: warning
      alert_if_inactive: yes
      alert_if_no_servers: yes

    nodes:
      - url: http://95.216.155.75:26657
        alert_if_down: no
      - url: https://rpc.tm.testedge2.haqq.network:443
        alert_if_down: no
      - url: https://rpc-haqq.palamar.io:443
        alert_if_down: no
      - url: http://142.132.202.50:11601
        alert_if_down: no   
EOF
```

#### Запускаем как сервис:
```shell
tee /etc/systemd/system/tenderduty.service << EOF
[Unit]
Description=Tenderduty
After=network.target

[Service]
Type=simple
Restart=always
RestartSec=5
TimeoutSec=180

User=root
WorkingDirectory=$HOME/.tenderduty
ExecStart=$(which tenderduty)
LimitNOFILE=infinity

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable tenderduty
sudo systemctl start tenderduty

sudo journalctl -fu tenderduty
```

Введите в браузере  `http://<IP сервера>:8888/`  
[Живой пример](http://45.88.106.186:8888/)  
Так же вы будете получать оповещения на свой сервер в discord.
