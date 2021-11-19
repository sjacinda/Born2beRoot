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
	$ deluser <name_user> <name_group> // так же удаляет пользователя и группы
	$ usermod -G"" <name_user> // удалbnm пользователя из всех групп
	$ chage -l <name_user> // проверить кофигурации пароля пользователя
