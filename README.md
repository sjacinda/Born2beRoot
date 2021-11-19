# Born2beRoot


## Основные команды:
	$ groupadd <name_group> // содать группу
	$ groupdel <name_group> // удалить группу
	$ getent group // показать группы и пользователей, которые в них есть
	$ useradd <name_user> // создать пользователя
	$ userdel <name_user> // удалить пользователя
	$ lastlog // проверить пользователей и их последний вход в систему
	$ usermod -aG <name_grop> <name_user> // добавить пользователя в группу
	$ gpasswd -d <name_grop> <name_user> // удалить пользователя из группы
	$ deluser <name_user> <name_group> // удалить пользователя и группы
	$ usermod -G"" <name_user> // удалить пользователя из всех групп
	$ chage -l <name_user> // проверить кофигурации пароля пользователя

	
## Скрипт для файла monitoring.sh:
##### Этот скрипт должен лежать по адрусу /usr/local/bin
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
##### Используй комнаду crontab -e для пользователя root
	*/10 * * * * /usr/local/bin/monitoring.sh // команда для реализации скрипта каждые 10 мин.
	* * * * *              /usr/local/bin/monitoring.sh // команда для реализаии скрипта кадую минуту
	* * * * * ( sleep 15 ; /usr/local/bin/monitoring.sh ) // команда для реализции скрипта каждую минуту с задержкой в 15 сек.
	* * * * * ( sleep 30 ; /usr/local/bin/monitoring.sh ) // команда для реализции скрипта каждую минуту с задержкой в 30 сек.
	* * * * * ( sleep 45 ; /usr/local/bin/monitoring.sh ) // команда для реализции скрипта каждую минуту с задержкой в 45 сек.
