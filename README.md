# Born2beRoot


## Основные команды:
	*для пользователя*
	$ adduser <user_name> // создать пользователя
	$ useradd <user_name> // низкоуровневое создание пользователя, *не рекомендуется использовать*
	$ userdel <user_name> // удалить учётную запись и файлы пользователя 
	
	
	$ groupadd <group_name> // содать группу
	$ groupdel <name_group> // удалить группу
	$ getent group // показать группы и пользователей, которые в них есть
	$ lastlog // проверить пользователей и их последний вход в систему
	$ usermod -aG <group_name> <user_name> // добавить пользователя в группу
	$ gpasswd -d <group_name> <user_name> // удалить пользователя из группы
	$ deluser <user_name> <group_name> // удалить пользователя и группы
	$ usermod -G"" <user_name> // удалить пользователя из всех групп
	$ chage -l <user_name> // проверить кофигурации пароля пользователя
	$ passwd <user_name> // сменить пароль пользователя
	$ passwd -S <user_name> // посмотреть последнее изменение пароля и его секретнцю информацию
	
	$ service --status-all // просмотреть все запущенные сервисы

	
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
	*/10 * * * * /usr/local/bin/monitoring.sh // для реализации скрипта каждые 10 мин.
	* * * * *              /usr/local/bin/monitoring.sh // для реализаии скрипта кадую минуту
	* * * * * ( sleep 15 ; /usr/local/bin/monitoring.sh ) // для реализции скрипта каждую минуту с задержкой в 15 сек.
	* * * * * ( sleep 30 ; /usr/local/bin/monitoring.sh ) // для реализции скрипта каждую минуту с задержкой в 30 сек.
	* * * * * ( sleep 45 ; /usr/local/bin/monitoring.sh ) // для реализции скрипта каждую минуту с задержкой в 45 сек.
	
	
## Политика паролей - в разработке !!!
	Срок действия пароля должен истекать каждые 30 дней
	Минимальное кол-во дней до смены пороля 2 дня
	Предупреждениие о истечении срока пороля 7 дней
	
	Пароль должен состоять из 10 символов
	Долен содержать заглавную букву и цифру
	Не должен содержать более 3 одинаковых символов подряд
	Пороль не должен содерать имя пользователя
	
	retry 3 // 3 попытки для ввода пороля
	
	не для root
	пороль должен содержать не менее 7 символов, которые не являются частью предыдущего пороля
	
