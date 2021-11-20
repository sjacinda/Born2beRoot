# Born2beRoot


## __Основные команды:__
	$ lsblk // показать информацию о дисках и разделах
	$ nano /etc/ssh/sshd_config // открыть конфиг ssh
	$ nano /etc/sudoers // открыть конфиг sudo
	$ /Users/sjacinda/.ssh/known_hosts // если есть проблема с доступом по ssh
	$ nano /var/log/sudo/sudo.log // открыть файл в котором есть все команды с судо
 	
	$ adduser <user_name> // создать пользователя
	$ userdel <user_name> // удалить пользователя и файлы
	$ lastlog // показать пользователей и даты последнего входа в систему
	
	$ groupadd <group_name> // создать группу
	$ groupdel <name_group> // удалить группу
	$ getent group // показать группы и связанных с ними пользователей
	
	$ usermod -aG <group_name> <user_name> // добавить пользователя в группу
	$ gpasswd -d <group_name> <user_name> // удалить пользователя из группы
	$ usermod -G"" <user_name> // удалить пользователя из всех групп
	
	$ passwd <user_name> // сменить пароль пользователя
	$ passwd -S <user_name> // посмотреть информацию о пароле пользователя
	$ chage -l <user_name> // посмотреть расширенную информацию о пароле пользователя
		
	$ service --status-all // просмотреть все запущенные сервисы
	$ hostname <new_hostname> // поменять имя хоста
	
	$ ufw allow <new_port> // создать новый порт
	$ ufw ststus numbered // показать все порты с порядковым номером
	$ ufw delete <port_number> // удалить порт по порядковому номеру
	
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
	*/10 * * * * /usr/local/bin/monitoring.sh // запускает скрипт каждые 10 мин.
	* * * * *              /usr/local/bin/monitoring.sh // запускает скрипт кадую минуту
	* * * * * ( sleep 15 ; /usr/local/bin/monitoring.sh ) // запускает скрипт каждую минуту с задержкой в 15 сек.
	* * * * * ( sleep 30 ; /usr/local/bin/monitoring.sh ) // запускает скрипт каждую минуту с задержкой в 30 сек.
	* * * * * ( sleep 45 ; /usr/local/bin/monitoring.sh ) // запускает скрипт каждую минуту с задержкой в 45 сек.
	
	
## Политика паролей:
##### $ nano /etc/pam.d/common-password
	password requisite pam_pwquality.so minlen=10 ucredit=-1 lcredit=-1 \
	dcredit=-1 maxrepeat=3 retry=3 usercheck=1 enforce_for_root // правила для root
	
	password requisite pam_pwquality.so minlen=10 ucredit=-1 lcredit=-1 \
	dcredit=-1 maxrepeat=3 retry=3 usercheck=1 difok=7 // правила для остальных пользователей
