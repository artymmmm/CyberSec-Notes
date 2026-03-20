## Источники информации
- [SPbCTF A&D Кароч: первым делом на вулнбоксе](https://www.youtube.com/watch?v=hxEQdgabKUQ&t=901s&pp=ugUEEgJydQ%3D%3D)
## Подключение к VPN
- Linux: `apt install openvpn`, `openvpn --config config.ovpn`
- Windows: [Openvpn](https://openvpn.net/community/)
- MacOS: Openvpn](https://openvpn.net/community/), [Tunnelblick](https://tunnelblick.net/downloads.html)
## Информация о сети
- `ifconfig` - посмотреть, какой IP в VPN  
- `route -n` - посмотреть, докуда можно достучаться через VPN
## Подключение к образу
- Windows: [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)  
- Linux, MacOS: `ssh team18@7.1.1.1`
## Поиск запущенных сервисов
- `ps auxf` — процессы в виде дерева, обратить внимание на пользователя (в A/D часто сервисы под своими пользователями)
- `netstat -tunlp` - слушающие порты, обратить внимание на PID процессов; необходмо различать то, что торчит наружу (сервисы), от того, что доступно локально (вспомогательные демоны). 127.0.0.1/ : : 1 : .... - слушает локально, 0.0.0.0/ : : : .... - слушает со всех IP.
### Файлы процесса  
В папке `/proc` хранится информация о запущенных процессах. Если у нас есть процесс с PID 1, в папке `/proc/1` будут храниться его файлы. Если процесс запущен не под пользователем, под которым сейчас произведен вход, можно зайти под `root`, чтобы посмотреть содержимое папки. Полезные файлы:
- `/proc/.../exe` - бинарный файл, который запущен
- `/proc/.../cwd` - папка, в которой работает процесс
- `/proc/.../fd/**` - открытые файлы сервиса
Есть два варианта, какой процесс будет слушать на порту:  
1. У сервиса есть самописный процесс, он и слушает. Тогда в `/proc/.../exe` будет файл, который нужно изучать.
2. Сервис пользуется сторонним демоном, который слушает за него ([xinetd](http://vault.centos.org/3.7/docs/html/rhel-rg-en-3/s1-tcpwrappers-xinetd-config.html), [apache](https://www.digitalocean.com/community/tutorials/how-to-configure-the-apache-web-server-on-an-ubuntu-or-debian-vps)/[nginx](https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-server-blocks-virtual-hosts-on-ubuntu-16-04), [systemd](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/using_systemd_unit_files_to_customize_and_optimize_your_system/assembly_working-with-systemd-unit-files_working-with-systemd)). Тогда нужно изучать конфиги этого демона. Конфиги обычно лежат в `/etc`.
Запасной вариант как найти сервисы - места автозагрузки в Linux:  
- `/etc/rc.local` - скрипт, который выполняется один раз при загрузке  
- `/etc/init.d/` - каталог с описаниями сервисов для SysV Init (старый стандарт)  
- `/etc/systemd/system/` (вероятней), `/lib/systemd/system/` (менее) - каталоги с сервисами для SystemD (новый стандарт)
## Взаимодействие с сервисами
- `nc -nv 7.1.1.1 16404` - TCP
- `nc -nvu 7.1.1.1 5417` - UDP (по UDP сервис не знает, что соединение "открыто", надо ему что-нибудь послать)
- `openssl s_client -connect 7.1.1.1:8443` - SSL. Если вывело инфу о сертификате, значит успешно установило с сервисом SSL-сессию
 - `curl -v http://7.1.1.1:8100` - HTTP, `-v` нужен, чтобы видеть заголовки  (`curl -vk https://7.1.1.1:8443` - HTTPS)
Стоит пробовать посылать сервисам `help`, `?` и т.д., смотреть, что они отвечают, гуглить ответы (вдруг известный протокол).
## Передача файла с образа
- `scp -r team18@7.1.1.1:/home /tmp/` - копирование по пути
- `sftp team18@7.1.1.1` - открывается шелл с возможностью делать `cd`, `ls`
- [FileZilla](https://filezilla-project.org/download.php?type=client) (GUI)