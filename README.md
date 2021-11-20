# Born2beRoot


## Основные команды:
	$ lsblk // показать информацию о дисках и разделах
	$ nano /etc/ssh/sshd_config // открыть конфиг ssh
	$ nano /etc/sudoers // открыть конфиг sudo
	$ /Users/sjacinda/.ssh/known_hosts // если есть проблема с доступом по ssh
	$ nano /var/log/sudo/sudo/sudo.log // открыть файл в котором есть все команды с судо
	$ nano /etc/pam.d/common-password // открыть файл политик паролей
	$ nano /usr/local/bin/monitoring.sh // открыт файл со скриптом
 	
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
	$ ufw allow ststus numbered // показать все порты
	$ ufw delete <number_port> // удалить номер порта


## Как разбить диск с помощью LVM для бонусной части?
<img width="516" alt="Screen Shot 2021-11-20 at 3 11 58 PM" src="https://user-images.githubusercontent.com/90088815/142725850-577d1bf9-2348-46f1-b8f3-8cbdf7303f34.png">

	Раздел		нужный объём памяти в MB
	sda1		525
	sda5-root	10 740
	sda5-swap	2 469
	sda5-home	5 327
	sda5-var	3 225
	sda5-srv	3 225
	sda5-tmp	3 225
	sda5-var-log	4 280
	
	
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
	
	
## Политика паролей:
#####  Открой файл common.password в папке /etc/pam.d
	
	password requisite pam_pwquality.so minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 retry=3 usercheck=1 difok=7
	password requisite pam_pwquality.so minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 retry=3 usercheck=1 enforce_for_root
	
	Действие пароля должно истекать каждые 30 дней
	Минимальное кол-во дней до смены пороля 2 дня
	Предупреждениие о истечении срока пороля должно приходить за 7 дней
	Пароль должен состоять минимум из 10 символов
	Пароль должден содержать не менее 1 заглавной буквы и не менее 1 цифры
	Пароль не должен содержать более 3 одинаковых символов подряд
	Пороль не должен содерать имя пользователя
	
##### Это правило для всех пользователей, кроме root:
	пороль должен содержать не менее 7 символов, которые не являются частью предыдущего пороля
