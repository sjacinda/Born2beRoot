# Born2beRoot


## Основные команды:
	$ lsblk // показать информацию о дисках и разделах
	$ nano /etc/ssh/sshd_config // открыть конфиг ssh
	$ nano /etc/sudoers // открыть конфиг sudo
	$ nano /Users/sjacinda/.ssh/known_hosts // если есть проблема с доступом по ssh
	$ nano /var/log/sudo/sudo.log // открыть файл истории использования команды sudo

	$ adduser <user_name> // создать пользователя
	$ userdel <user_name> // удалить пользователя и файлы
	$ lastlog // показать пользователей и даты последнего входа в систему
	
	$ groupadd <group_name> // создать группу
	$ groupdel <name_group> // удалить группу
	$ getent group // показать группы и связанных с ними пользователей
	
	$ usermod -aG <group_name> <user_name> // добавить пользователя в группу
	$ gpasswd -d <user_name> <group_name> // удалить пользователя из группы
	
	$ passwd <user_name> // сменить пароль пользователя
	$ passwd -S <user_name> // посмотреть информацию о пароле пользователя
	$ chage -l <user_name> // посмотреть расширенную информацию о пароле пользователя
	
	$ ufw allow <new_port> // создать новый порт
	$ ufw status numbered // показать все порты
	$ ufw delete <port_number> // удалить порт по порядковому номеру

	$ hostname <new_hostname> // сменить имя хоста
	$ service --status-all // просмотреть все сервисы

## Скрипт для файла monitoring.sh:
##### $ nano /usr/local/bin/monitoring.sh
	#!/bin/bash
	wall $'#Architecture:' `uname -a` \
	$'\n#CPU physical : '`nproc` \
	$'\n#vCPU :' `cat /proc/cpuinfo | grep processor | wc -l` \
	$'\n#Memory Usage:' `free -m | grep Mem | awk '{printf "%d/%d (%.1f%%)", $3, $2, $3*100/$2}'` \
	$'\n#Disk Usage: '`df -h | grep root | awk '{print $3"/"$2" ("$5")"}'` \
	$'\n#CPU load: '`cat /proc/loadavg | awk '{printf "%.1f%%", $1}'` \
	$'\n#Last boot:' `who -b | awk '{print $3" "$4}'` \
	$'\n#LVM use:' `lsblk |grep lvm | awk '{if ($1) {print "yes";exit;} else {print "no"} }'` \
	$'\n#Connection TCP:' `netstat -an | grep ESTABLISHED |  wc -l` "ESTABLISHED" \
	$'\n#User log:' `who | wc -l` \
	$'\nNetwork: IP' `hostname -I`"("`ip a | grep link/ether | awk '{print $2}'`")" \
	$'\n#Sudo :' `cat /var/log/sudo/sudo.log | grep COMMAND | wc -l` "cmd"


## Скрипт для сервиса Cron:
##### $ crontab -e
	*/10 * * * * bash /usr/local/bin/monitoring.sh // запускает скрипт каждые 10 мин.
	* * * * * /usr/local/bin/monitoring.sh // запускает скрипт кадую минуту
	* * * * * ( sleep 15 ; /usr/local/bin/monitoring.sh ) // запускает скрипт каждую минуту с задержкой на 15 сек.
	* * * * * ( sleep 30 ; /usr/local/bin/monitoring.sh ) // запускает скрипт каждую минуту с задержкой на 30 сек.
	* * * * * ( sleep 45 ; /usr/local/bin/monitoring.sh ) // запускает скрипт каждую минуту с задержкой на 45 сек.


## Политика паролей:
##### $ nano /etc/pam.d/common-password
	password requisite pam_pwquality.so minlen=10 ucredit=-1 lcredit=-1 \
	dcredit=-1 maxrepeat=3 retry=3 usercheck=1 enforce_for_root // правила для root
	
	password requisite pam_pwquality.so minlen=10 ucredit=-1 lcredit=-1 \
	dcredit=-1 maxrepeat=3 retry=3 usercheck=1 difok=7 // правила для остальных пользователей
