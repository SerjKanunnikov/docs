# Подключиться к виртуальной машине Linux по SSH

Рекомендуемый способ подключения к виртуальной машине по SSH основан на использовании пары ключей: открытый ключ размещается на виртуальной машине, а закрытый ключ хранится у пользователя. Подключение с помощью пары ключей более безопасно, чем подключение по логину и паролю.

[!INCLUDE [vm-connect-ssh-linux-note](../../../_includes/vm-connect-ssh-linux-note.md)]

[!INCLUDE [vm-ssh-prepare-key](../../../_includes/vm-ssh-prepare-key.md)]

## Подключение к виртуальной машине {#vm-connect}

Вы можете подключиться к виртуальной машине по протоколу SSH, когда она будет запущена (в статусе `RUNNING`). Для этого можно использовать утилиту `ssh` в Linux и macOS и программу [PuTTy](https://www.chiark.greenend.org.uk/~sgtatham/putty/) для Windows. 

Для подключения необходимо указать публичный адрес виртуальной машины. Публичный IP-адрес можно узнать в консоли управления в поле **Публичный IPv4** блока **Сеть** на странице виртуальной машины. Если вы создали машину только с внутренним адресом, для доступа через интернет нужно создать новую машину с публичным адресом.

Также можно использовать внутренние IP-адреса и FQDN для установки SSH-соединения между виртуальными машинами внутри одной облачной сети Яндекс.Облака.

---

**[!TAB Linux/macOS/Windows 10]**

В терминале выполните следующую команду:

```
$ ssh <имя_пользователя>@<публичный_IP-адрес_виртуальной машины>
```

Если вы подключаетесь к машине в первый раз, может появиться предупреждение о неизвестном хосте: 

```
The authenticity of host '130.193.40.101 (130.193.40.101)' can't be established.
ECDSA key fingerprint is SHA256:PoaSwqxRc8g6iOXtiH7ayGHpSN0MXwUfWHkGgpLELJ8.
Are you sure you want to continue connecting (yes/no)?
```

Введите в терминале слово `yes` и нажмите `Enter`.

**[!TAB Windows 7/8]**

В Windows соединение устанавливается с помощью приложения PuTTY.

1. Запустите приложение Pageant.
   1. Нажмите правой кнопкой мыши на значок Pageant на панели задач.
   1. В контекстном меню выберите пункт **Add key**.
   1. Выберите сгенерированный PuTTY приватный ключ в формате `.ppk`. Если для ключа задан пароль, введите его.
1. Запустите приложение PuTTY.
   1. В поле **Host Name (or IP address)** введите публичный IP-адрес виртуальной машины, к которой вы хотите подключиться. Укажите порт `22` и тип соединения **SSH**.

      ![ssh_add_ip](../../_assets/ssh-putty/ssh_add_ip.png)

   1. Откройте в дереве слева пункт **Connection** - **SSH** - **Auth**.
   1. Установите флаг **Allow agent forwarding**.
   1. В поле **Private key file for authentication** выберите файл с приватным ключом.

      ![ssh_choose_private_key](../../_assets/ssh-putty/ssh_choose_private_key.png)

   1. Вернитесь в меню **Sessions**. В поле **Saved sessions** введите любое название для сессии и нажмите кнопку **Save**. Настройки сессии сохранятся под указанным именем. Вы сможете использовать этот профиль сессии для подключения с помощью Pageant.

      ![ssh_save_session](../../_assets/ssh-putty/ssh_save_session.png)

   1. Нажмите кнопку **Open**. Если вы подключаетесь к машине в первый раз, может появиться предупреждение о неизвестном хосте: 

      ![ssh_unknown_host_warning](../../_assets/ssh-putty/ssh_unknown_host_warning.png)

      Нажмите кнопку **Да**. Откроется окно терминала с предложением ввести логин пользователя, от имени которого устанавливается соединение. Введите имя пользователя, которое вы указали при создании виртуальной машины и нажмите `Enter`. Если все настроено верно, будет установлено соединение с сервером.

      ![ssh_login](../../_assets/ssh-putty/ssh_login.png)

Если вы сохранили профиль сессии в PuTTY, в дальнейшем для установки соединения можно использовать Pageant:

1. Нажмите правой кнопкой мыши на значок pageant на панели задач.
1. Выберите пункт меню **Saved sessions**.
1. В списке сохраненных сессий выберите нужную сессию. 

---

## Добавление SSH-ключей для других пользователей {#vm-authorized-keys}

Можно добавить SSH-ключи для другого пользователя виртуальной машины. Для этого нужно создать нового пользователя и добавить для него файл с авторизованными ключами.

1. Зайдите на виртуальную машину под именем пользователя, которого вы указали при создании виртуальной машины в консоли управления.
1. Создайте нового пользователя и укажите для него оболочку `bash` для использования по умолчанию:

   ```
   $ sudo useradd -m -d /home/testuser -s /bin/bash testuser
   ```

1. Переключитесь на созданного пользователя:

   ```
   $ sudo su - testuser
   ```

1. Создайте папку `.ssh` в домашней директории нового пользователя:

   ```
   $ mkdir .ssh
   ```

1. Создайте в папке `.ssh` файл `authorized_keys`:

   ```
   $ touch .ssh/authorized_keys
   ```

1. Добавьте в файл `authorized_keys` публичный ключ нового пользователя:

   ```
   $ echo "ssh-rsa <публичный_ключ>" > /home/testuser/.ssh/authorized_keys
   ```

1. Отключитесь от виртуальной машины.
1. Убедитесь, что есть доступ на виртуальную машину, подключившись к ней под именем нового пользователя:

   ```
   $ ssh testuser@<публичный-IP-адрес-ВМ>
   ```