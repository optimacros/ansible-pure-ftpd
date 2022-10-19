# Ansible Pure-ftpd

## Подготовка

1. Клонируем репозиторий на докальную машину.
2. Создать файл хранилище пароля. 
```
echo “MySuperPass” > ~/.ansible_pass.txt
```
3. Запустить выполнение `ftp-playbook.yml`

```
ansible-playbook ftp-playbook.yml --vault-password-file .ansible_pass.txt
```


## Переменные роли

### Переменные относящиеся к демону pureftpd
Данные переменные относятся к работе демона ftp. Приведенных значений по умолчанию достаточно для функционирования. Имеем два кейса при работе с этими двумя переменными.
* при вводе в эксплуатацию нового ftp сервера данные переменные в файле описания хоста можно не указывать, и будут использованны значения по умолчанию.
* для имеющихся ftp серверов если значения в настроенной системе отличаются от дефолтных их требуется переопределить. 

```
pure_ftpd_user: "ftp"
```
Системный пользователь. Не имеет право входа в систему. Является владельцем загружаемых файлов.
```
pure_ftpd_group: "ftp"
```
Группа системного уровня. Группа назначается всем файлам загруженным через FTP-сервер
```
pure_ftpd_root: "/var/ftp"
```
Системный каталог ftp сервера


### Переменная список виртуальных пользователей
```
# virtual users for ftp server
pure_ftpd_vusers:
  - name: ftp
    dir: "{{ pure_ftpd_root }}"
    password: "FTPisSoC00l?"
```
Список пользователей ftp сервера. Имя и пароль обязательные поля. Каталог пользователя можно не указывать. По умолчанию равен `pure_ftpd_root`

Поскольку массив virtual_users должен содержать секретные учетные данные, необходимо шифровать файлы содержащие переменные хоста. 

### Параметры TLS
```
pure_ftpd_tls: true 
```
Включает или выключает поддержку шифрования сервером. По умолчанию включенно. Если выключенно остальные переменные не требуются. 
```
pure_ftpd_allow_insecure: false
```
Когда шифрование TLS включено, по умолчанию незашифрованные небезопасные соединения запрещены. Установка этого значения в true разрешает как безопасные, так и небезопасные соединения.
```
pure_ftpd_pem : |
-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQCmKFFE1LBM73EY
fwm07ozcWBcFSjJxCa8T+jZ5tRswxLaAjZYjWE44+DDn1inP8DzaXldeYQqwGQoA
k8z59gFKsbQv7n6vtlYPaN5cGB5DcXsguxJVhIA+tIKmuLhw+yALK/rRmwjk9Qmo
...
3wNKz3J+1X5fQOhkOChq54stGpUP4ej1Gqh7UG18kP0UHdJchgIAPXU3eF7bK8Cx
RcVFUA0jCEcSShGNCWXZ3Bjz8mJJRWiTF73Z7gBEr2zoS83mveNarnS7ZAwykxTt
Fk7nz2lbC6NHNUp4fU4OPGM=
-----END PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
MIIDazCCAlOgAwIBAgIUL+TxVvif2HLdtgMgoNHrrk/GKrgwDQYJKoZIhvcNAQEL
BQAwRTELMAkGA1UEBhMCUlUxEzARBgNVBAgMClNvbWUtU3RhdGUxITAfBgNVBAoM
GEludGVybmV0IFdpZGdpdHMgUHR5IEx0ZDAeFw0yMjEwMTkwNjM5NThaFw0yMzEw
...
yNLB/EWRKS/yATPH9QQO872RPtxEUQ7bMoIbOvGPMZcLPpIpm27VXEsHnPdDyWxY
VY24MgO1HqW23PdXcWCI
-----END CERTIFICATE-----
```

Файл сертификата ftp сервера. Если заданно, будет скопирован и установлен на сервер. 

```
pure_ftpd_openssl_config:
  days: "365"
  size: "2048"
  country: "RU"
  state: ""
  locality: ""
  org: ""
  unit: ""
  common: ""
  email: ""
```
Параметры выпуска сертификата. Используется если значение `pure_ftpd_pem` не заданно. 



## Работа с файлами переменных хостов
### Шифрование файлов переменных

```
ansible-vault encrypt ./host_vars/ftp-001.yml --vault-password-file .ansible_pass.txt
```

### Расшифровка 
```
ansible-vault decrypt ./host_vars/ftp-001.yml --vault-password-file .ansible_pass.txt
```

### Просмотр содержимого
```
ansible-vault view ./host_vars/ftp-001.yml --vault-password-file .ansible_pass.txt
```

### Редактирование
```
ansible-vault edit ./host_vars/ftp-001.yml --vault-password-file .ansible_pass.txt
```

