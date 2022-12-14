

# π Scenario-Based-Virtual-Infrastructure
## ποΈ Scenario

π¬
>A η€Ύλ νμ¬μ ννμ΄μ§λ₯Ό λ§λ€μ΄ κ³ κ°λ€μ΄ μ νμ λν΄ λΉ λ₯Έ μ κ·Όμ ν  μ μλλ‘ μΈνλΌλ₯Ό κ΅¬μ±νμ¬ μΉ μλΉμ€λ₯Ό μ κ³΅νκ³ μ νλ€. μνν μλΉμ€ μ΄μκ³Ό λλΆμ΄ λ°μ΄ν° λ°±μμ μν λ°±μ μλ² λ° μ±μμΆμ μ±μ νλ³΄νκΈ° μν΄ λ‘κ·Έ μλ²λ₯Ό λ³λ κ΅¬μ±νκ³ , κ°λ°μλ€μ΄ μ€ μλΉμ€λ₯Ό νλ μλ²μ μλ‘λνκΈ° μ μ νμ€ν λ° κ°λ° μμ μ¬μ©νλ κ°λ° μλ²λ νμλ‘ νλ€.


## π κ°μ μΈνλΌ κ΅¬μ± λ° μκ΅¬ μ¬ν­
**1. Web Service Server** 
```
- μΉ νμ΄μ§λ₯Ό μΈλΆμμ μ μ ν  λ μλΉμ€λ₯Ό μ κ³΅νλ μλ²
- μλ²κ΅¬μ±: μΉ(Apache) λ° WAS(Tomcat)
- DB μλ²μ μΉ μμΌ μ°κ²°
```
**2. DB Server**
```
- ν΄λΉ μΉ μλΉμ€μ νμ μ λ³΄λ₯Ό μ μ₯νλ μλ²
- MariaDB
```
**3. Development Server**
```
- μΉ μλΉμ€ μλ²μ μμ€ μ½λλ₯Ό μλ‘λ νκΈ° μ  νμ€ν λ° κ°λ° μ μ¬μ©νλ μλ²
- FTPλ₯Ό μ΄μ©νμ¬ μΉ μλΉμ€ μλ²μ ν΅μ  κ΅¬κ° μ°κ²°
- κ°λ°μ PC(νΈμ€νΈ)μ SSH ν°λ―Έλ μ°κ²°
```

**4. Log Server**
```
- DB μλ²μ λ°μ΄ν°λ₯Ό λ€ν μ μ₯
```

**5. DB Backup Server**
```
- μΉ μλΉμ€, DB μλ²μ λ‘κ·Έ μ μ₯
- μ£ΌκΈ°μ μΌλ‘ μΉ μλΉμ€ μλ² λ° DB μλ²μμ λ‘κ·Έ μ μ‘
```

## πΌοΈ μΈνλΌ κ΅¬μ±λ
![κ΅¬μ±λμ](https://user-images.githubusercontent.com/118449010/206063891-fc3b8616-46ee-4f35-88ad-4a1c36855543.png)

#
### π κ° κ΅¬μ± μμ λ²μ  λ° μ λ³΄
<img src="https://img.shields.io/badge/Apache-D22128?style=for-the-badge&logo=Apache&logoColor=white"> <img src="https://img.shields.io/badge/Tomcat-F8DC75?style=for-the-badge&logo=Apache Tomcat&logoColor=black"> <img src="https://img.shields.io/badge/MariaDB-1F305F?style=for-the-badge&logo=MariaDB&logoColor=white"> <img src="https://img.shields.io/badge/Ubuntu-FF455F?style=for-the-badge&logo=Ubuntu&logoColor=white">

- **OS**: Ubuntu 18.04.6-live-server-amd64
- **Apache**: Apache/2.4.29
- **Tomcat**: Apache Tomcat/9.0.16
- **MariaDB**: 10.1.48-MariaDB

| μλΉμ€ λͺ | κ³ μ μμ΄νΌ | Hard Disk | RAM |
| --- | --- | --- | --- |
| Web Service Server | 192.168.0.151 | 20GB | 2GB |
| DB Server | 192.168.0.152 | 20GB | 2GB |
| Development Server | 192.168.0.153 | 20GB | 2GB |
| Log Server | 192.168.0.154 | 20GB | 1GB |
| DB Backup Server | 192.168.0.155 | 20GB | 1GB |


## π» Web Service Server
### π κ³ μ  μμ΄νΌ μ€μ 

- /etc/netplan/00-installer-config.yaml μμ 
```
sudo vim /etc/netplan/00-installer-config.yaml
```
```
network:
  ethernets:
        ens33:
                dhcp4: no
                addresses: [192.168.0.151/24]
                gateway4: 192.168.0.2
                nameservers:
                        addresses: [8.8.8.8]

  version: 2
```

- λ³κ²½ μ¬ν­ μ μ₯
```
sudo netplan apply
```

### π μνμΉ μ€μ 
- Apache2 libapache2-mod-jk μ€μΉ
```
sudo apt-get install -y apache2 libapache2-mod-jk
```
- /etc/apache2/sites-available/000-default.conf μμ νμ¬ `JkMount /* tomcat` μΆκ°
```
sudo vim /etc/apache2/sites-available/000-default.conf 
```
```
DocumentRoot /var/www/html
JkMount /* tomcat #μΆκ° λ¬Έκ΅¬
```

- /etc/apache2/workers.properties νμΌ μμ± ν `λ¬Έκ΅¬ μμ±`
```
	worker.list=tomcat
	workers.tomcat_home=/usr/share/tomcat9
	workers.java_home=/usr/lib/jvm/java-11-openjdk-amd64
	worker.tomcat.port=8009
	worker.tomcat.host=localhost
	worker.tomcat.type=ajp13
	worker.tomcat.lbfactor=1
```

- /etc/apache2/mods-available/jk.conf μμ 
```
#JkWorkersFile /etc/libapache2-mod-jk/workers.properties 		#κΈ°μ‘΄λ¬Έκ΅¬
JkWorkersFile /etc/apache2/workers.properties 				#μμ λ¬Έκ΅¬
```

- μνμΉ μ¬μμ
```
sudo systemctl restart apache2
```


### π WAS κ΅¬μΆ
- default-jdk tomcat9 libmysql-java μ€μΉ
```
sudo apt-get install -y default-jdk tomcat9 libmysql-java
```

- /etc/tomcat9/server.xml μμ 
```
sudo vim /etc/tomcat9/server.xml
```
```
<!-- Define an AJP 1.3 Connector on port 8009 -->
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443"/>		#ν΄λΉ λ΄μ© μ£Όμ ν΄μ 
```

- jar νμΌ Copy
```
sudo cp /usr/share/java/mysql-connector-java-5.1.45.jar /usr/share/tomcat9/lib/
```

- Tomcat μ¬μμ
```
sudo systemctl restart tomcat9
```


### π vsFTP μ€μ 
- vsFTP μ€μΉ
```
sudo apt-get install -y vsftpd
```

- /etc/vsftpd.conf μμ  `μλ 3κ° μ€μ  μ£Όμ μ κ±°`
```
sudo vim /etc/vsftpd.conf
```
```
write_enable=YES
local_umask=022
chroot_local_user=NO ##YESμΈμ§ νμΈ
```

- vsFTP μ¬μμ
```
sudo systemctl restart vsftpd
```

### π rsyslog μ€μ 
- /etc/rsyslog.conf μμ  `Log Server μμ΄νΌ μΆκ°`
```
sudo vim /etc/rsyslog.conf
```
```
*.* @@192.168.0.154:514
```

- rsyslog μ¬μμ
```
sudo systemctl restart rsyslog
```

### π μμ€μ½λ μ΄λ
- νΈμ€νΈ PC μμ ROOT.war νμΌμ΄ μλ νμΌνμκΈ° μμΉμμ Shift + μ°ν΄λ¦­μ ν ν PowerShell μ°½ μ΄κΈ° ν΄λ¦­
```
-> scp ROOT.war KJOONHWAN@192.168.0.153:~/ 
-> yes
```

- DB μλ²μμ νμΌμ΄ μ μμ μΌλ‘ μ΄λ λμλμ§ νμΈ
```
sudo cp ~/ROOT.war /var/lib/tomcat9/webapps/ 
```

- ROOT νμΌμ `ROOT_BAK`μΌλ‘ νμΌλͺ λ³κ²½
```
cd /var/lib/tomcat9/webapps
```
```
sudo mv ROOT/ ROOT_BAK/
```

- Tomcat μ¬μμ `λλ μΌμ  μκ°μ΄ μ§λλ©΄ tomcatμ΄ ROOT.WAR νμΌμ ν΄μ νμ¬ ROOT λλ ν°λ¦¬ μμ±`
```
sudo systemctl restart tomcat9
```

- μ€μ  μ°ΎκΈ°
```
sudo -s
```
```
cd ROOT
```
```
grep -rn "192.168."
```

- μ€μ  λ³κ²½ `ν΄λΉ νμΌλ€ μμ μλ λ΄μ©μ DB Server IPλ‘ μ€μ `
```
joinAction.jsp
success.jsp
loginAction.jsp
```
```
joinAction.jsp:10:    String jdbcurl = "jdbc:mysql://192.168.0.152:3306/KJOONHWAN";
success.jsp:8:    String jdbcurl = "jdbc:mysql://192.168.0.152:3306/KJOONHWAN";
loginAction.jsp:12:     String jdbcurl = "jdbc:mysql://192.168.0.152:3306/KJOONHWAN";
```

- Tomcat μ¬μμ
```
systemctl restart tomcat9
```


## π» DB Server
### π mariaDB μ€μΉ
- mariadb-server μ€μΉ
```
sudo apt-get -y install mariadb-server
```

- mariaDB μ΄κΈ° μ€μ 
```
sudo mysql_secure_installation
```
```
Enter current password for root (enter for none):  # νμ¬ MariaDBμ root ν¨μ€μλκ° μμΌλ―λ‘ μν°
OK, successfully used password, moving on...
```
```
Set root password? [Y/n]  		        y 	# MariaDB root ν¨μ€μλ μ€μ  μ§μ
```
```
New password: 	# μ€μ ν  root ν¨μ€μλ μλ ₯
Re-enter new password: 	# μ€μ ν root ν¨μ€μλ νμΈ μ¬μλ ₯
```
```
Remove anonymous users? [Y/n] 		        y # μ΅λͺμ μ κ·Όμ λν μ§μμ΄λ©°, λ³΄μμ μν΄ μ°¨λ¨

Disallow root login remotely? [Y/n] 		n # μΈλΆλ‘μ μ°κ²° νμ©

Remove test database and access to it? [Y/n] 	y # νμ€νΈμ©μΌλ‘ μμ±λ λ°μ΄ν°λ² μ΄μ€ μ­μ  μ¬λΆ μ§μ

Reload privilege tables now? [Y/n] 		y # νμ¬ μ€μ λ κ°μ λν μ μ© μ¬λΆ μ§μ
```


### π mariaDB λ΄λΆμμ
- mySQL μ μ
```
sudo mysql -u root -p
```

- Database μμ±
```
create database KJOONHWAN;
```

- μμ±ν `KJOONHWAN` μ¬μ©
```
use KJOONHWAN;
```

- `login` Table μμ±
```
create table login (id varchar(20) primary key, pw varchar(20));
```

- μμ±ν Table νμΈ
```
MariaDB [KJOONHWAN]> show tables;
```
```
+---------------------+
| Tables_in_KJOONHWAN |
+---------------------+
| login               |
+---------------------+
```

- `login` νμ΄λΈ κ΅¬μ‘° μ΄λ
```
MariaDB [KJOONHWAN]> desc login;
```
```
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | varchar(20) | NO   | PRI | NULL    |       |
| pw    | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
```

- λ°μ΄ν°λ² μ΄μ€ μ ν
```
use mysql
```

- user νμ΄λΈμμ `host, user, password` κ²μ
```
MariaDB [mysql]> select host, user, password from user;
```

- μμ±λ κΆν λΆμ¬ `KJOONHWAN μ¬μ©μμκ² λͺ¨λ  κΆνμ ν λΉ`
```
GRANT ALL PRIVILEGES ON KJOONHWAN.* TO root@'192.168.0.151' identified by '!kjoonhwan!123@';
```

- localhostμ λΉλ°λ²νΈκ° κ°μμ§ νμΈ
```
MariaDB [mysql]> select host, user, password from user;
```
```
+---------------+------+-------------------------------------------+
| host          | user | password                                  |
+---------------+------+-------------------------------------------+
| localhost     | root | *cda82de8117bc8eda72a04864ecece43040fb5da |
| 192.168.0.151 | root | *cda82de8117bc8eda72a04864ecece43040fb5da |
+---------------+------+-------------------------------------------+
```

- νμ¬ μ¬μ© μ€μΈ mySQLμ μΊμλ₯Ό μ­μ νκ³  μλ‘μ΄ μ€μ μ μ μ©
```
flush privileges;
```

- λ°μ΄ν°λ² μ΄μ€ λκ°κΈ°
```
exit
```

### π 50-server μ€μ 
- /etc/mysql/mariadb.conf.d/50-server.cnf μμ 
```
sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf
```
```
#bind-address = 127.0.0.1 #κΈ°μ‘΄ μ€μ 
bind-address = 0.0.0.0    #μμ  μ€μ 
```

- mySQL μ¬μμ
```
service mysqld restart
```

### π rsyslog μ€μ 
- /etc/rsyslog.conf μμ 
```
sudo vim /etc/rsyslog.conf
```
```
*.* @@192.168.0.154:514 #νμΌμ λ§¨ νλ¨μ ν΄λΉ λ΄μ© μΆκ°
```

- rsyslog μ¬μμ
```
sudo systemctl restart rsyslog
```

### π DB Backup μ€μ  1
- xinetd rsync μ€μΉ
```
sudo apt-get install xinetd rsync
```

- /etc/rsyncd.conf μμ± ν μλ λ΄μ© μμ±
```
sudo vim /etc/rsyncd.conf
```
```
	[DBS]
	path = /backup
	comment = DB server
	uid = 65534
	gid = 65534
	use chroot = yes
	read only = yes
	hosts allow = [192.168.0.155]
	max connections = 3
	timeout 600
```

- /etc/xinetd.d/rsync μμ± ν μλ λ΄μ© μμ±
```
sudo vim /etc/xinetd.d/rsync
```
```
	service rsync
	{
		disable = no
		socket_type = stream
		wait = no
		user = root
		server = /usr/bin/rsync
		server_args = --daemon
		log_on_failure += USERID
	}
```

- xinetd μ¬μμ
```
sudo systemctl restart xinetd
```

### π DB Backup μ€μ  2 `DB λ€ν λ° Cron λ±λ‘`
- backup λλ ν°λ¦¬ μμ±
```
sudo mkdir /backup
```

- `root` κΆνμΌλ‘ λ°μ΄ν°λ² μ΄μ€ λ°±μ λ€ν
```
sudo -s
```
```
mysqldump -uroot -p!joonhwan!123! KJOONHWAN > /backup/mysql_dump_$(date +%Y%m%d).sql
```

- Cron λ±λ‘μ μν μ μ€ν¬λ¦½νΈ νμΌ μμ±
```
vim /backup/backup.sh
```
```
#!/bin/bash
mysqldump -uroot -p!joonhwan!123! KJOONHWAN > /backup/mysql_dump_$(date +%Y%m%d).sql
```

- μ¬μ©μμκ² μ€ν κΆν λΆμ¬
```
chmod u+x /backup/backup.sh
```

- crontab λ±λ‘
```
crontab -e
```
```
* * * * * /backup/backup.sh > /backup/backup.log 2>&1
```

- λ°±μ νμΌμ΄ λ§€ λΆ μμ λλμ§ νμΈ
```
ls -al /backup/
```



## π» Development Server
### π μνμΉ μ€μ 
- Apache2 libapache2-mod-jk μ€μΉ
```
sudo apt-get install -y apache2 libapache2-mod-jk
```
- /etc/apache2/sites-available/000-default.conf μμ νμ¬ `JkMount /* tomcat` μΆκ°
```
sudo vim /etc/apache2/sites-available/000-default.conf 
```
```
DocumentRoot /var/www/html
JkMount /* tomcat #μΆκ° λ¬Έκ΅¬
```

- /etc/apache2/workers.properties νμΌ μμ± ν `λ¬Έκ΅¬ μμ±`
```
	worker.list=tomcat
	workers.tomcat_home=/usr/share/tomcat9
	workers.java_home=/usr/lib/jvm/java-11-openjdk-amd64
	worker.tomcat.port=8009
	worker.tomcat.host=localhost
	worker.tomcat.type=ajp13
	worker.tomcat.lbfactor=1
```

- /etc/apache2/mods-available/jk.conf μμ 
```
#JkWorkersFile /etc/libapache2-mod-jk/workers.properties 		#κΈ°μ‘΄λ¬Έκ΅¬
JkWorkersFile /etc/apache2/workers.properties 				#μμ λ¬Έκ΅¬
```

- μνμΉ μ¬μμ
```
sudo systemctl restart apache2
```


### π WAS κ΅¬μΆ
- default-jdk tomcat9 libmysql-java μ€μΉ
```
sudo apt-get install -y default-jdk tomcat9 libmysql-java
```

- /etc/tomcat9/server.xml μμ 
```
sudo vim /etc/tomcat9/server.xml
```
```
<!-- Define an AJP 1.3 Connector on port 8009 -->
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443"/>		#ν΄λΉ λ΄μ© μ£Όμ ν΄μ 
```

- jar νμΌ Copy
```
sudo cp /usr/share/java/mysql-connector-java-5.1.45.jar /usr/share/tomcat9/lib/
```

- Tomcat μ¬μμ
```
sudo systemctl restart tomcat9
```


### π vsFTP μ€μ 
- vsFTP μ€μΉ
```
sudo apt-get install -y vsftpd
```

- /etc/vsftpd.conf μμ  `μλ 3κ° μ€μ  μ£Όμ μ κ±°`
```
sudo vim /etc/vsftpd.conf
```
```
write_enable=YES
local_umask=022
chroot_local_user=NO ##YESμΈμ§ νμΈ
```

- vsFTP μ¬μμ
```
sudo systemctl restart vsftpd
```

### π mariaDB μ€μΉ
- mariadb-server μ€μΉ
```
sudo apt-get -y install mariadb-server
```

- mariaDB μ΄κΈ° μ€μ 
```
sudo mysql_secure_installation
```
```
Enter current password for root (enter for none):  # νμ¬ MariaDBμ root ν¨μ€μλκ° μμΌλ―λ‘ μν°
OK, successfully used password, moving on...
```
```
Set root password? [Y/n]  		        y 	# MariaDB root ν¨μ€μλ μ€μ  μ§μ
```
```
New password: 	# μ€μ ν  root ν¨μ€μλ μλ ₯
Re-enter new password: 	# μ€μ ν root ν¨μ€μλ νμΈ μ¬μλ ₯
```
```
Remove anonymous users? [Y/n] 		        y # μ΅λͺμ μ κ·Όμ λν μ§μμ΄λ©°, λ³΄μμ μν΄ μ°¨λ¨

Disallow root login remotely? [Y/n] 		n # μΈλΆλ‘μ μ°κ²° νμ©

Remove test database and access to it? [Y/n] 	y # νμ€νΈμ©μΌλ‘ μμ±λ λ°μ΄ν°λ² μ΄μ€ μ­μ  μ¬λΆ μ§μ

Reload privilege tables now? [Y/n] 		y # νμ¬ μ€μ λ κ°μ λν μ μ© μ¬λΆ μ§μ
```


### π mariaDB λ΄λΆμμ
- mySQL μ μ
```
sudo mysql -u root -p
```

- Database μμ±
```
create database KJOONHWAN;
```

- μμ±ν `KJOONHWAN` μ¬μ©
```
use KJOONHWAN;
```

- `login` Table μμ±
```
create table login (id varchar(20) primary key, pw varchar(20));
```

- μμ±ν Table νμΈ
```
MariaDB [KJOONHWAN]> show tables;
```
```
+---------------------+
| Tables_in_KJOONHWAN |
+---------------------+
| login               |
+---------------------+
```

- `login` νμ΄λΈ κ΅¬μ‘° μ΄λ
```
MariaDB [KJOONHWAN]> desc login;
```
```
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | varchar(20) | NO   | PRI | NULL    |       |
| pw    | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
```

- λ°μ΄ν°λ² μ΄μ€ μ ν
```
use mysql
```

- user νμ΄λΈμμ `host, user, password` κ²μ
```
MariaDB [mysql]> select host, user, password from user;
```

- μμ±λ κΆν λΆμ¬ `KJOONHWAN μ¬μ©μμκ² λͺ¨λ  κΆνμ ν λΉ`
```
GRANT ALL PRIVILEGES ON KJOONHWAN.* TO root@'192.168.0.153' identified by '!kjoonhwan!123@';
```

- localhostμ λΉλ°λ²νΈκ° κ°μμ§ νμΈ
```
MariaDB [mysql]> select host, user, password from user;
```
```
+---------------+------+-------------------------------------------+
| host          | user | password                                  |
+---------------+------+-------------------------------------------+
| localhost     | root | *cda82de8117bc8eda72a04864ecece43040fb5da |
| 192.168.0.171 | root | *cda82de8117bc8eda72a04864ecece43040fb5da |
+---------------+------+-------------------------------------------+
```

- νμ¬ μ¬μ© μ€μΈ mySQLμ μΊμλ₯Ό μ­μ νκ³  μλ‘μ΄ μ€μ μ μ μ©
```
flush privileges;
```

- λ°μ΄ν°λ² μ΄μ€ λκ°κΈ°
```
exit
```

### π 50-server μ€μ 
- /etc/mysql/mariadb.conf.d/50-server.cnf μμ 
```
sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf
```
```
#bind-address = 127.0.0.1 #κΈ°μ‘΄ μ€μ 
bind-address = 0.0.0.0    #μμ  μ€μ 
```

- mySQL μ¬μμ
```
service mysqld restart
```

### π μμ€μ½λ μ΄λ
- νΈμ€νΈ PC μμ ROOT.war νμΌμ΄ μλ νμΌνμκΈ° μμΉμμ Shift + μ°ν΄λ¦­μ ν ν PowerShell μ°½ μ΄κΈ° ν΄λ¦­
```
-> scp ROOT.war KJOONHWAN@192.168.0.153:~/ 
-> yes
```

- DB μλ²μμ νμΌμ΄ μ μμ μΌλ‘ μ΄λ λμλμ§ νμΈ
```
sudo cp ~/ROOT.war /var/lib/tomcat9/webapps/ 
```

- ROOT νμΌμ `ROOT_BAK`μΌλ‘ νμΌλͺ λ³κ²½
```
cd /var/lib/tomcat9/webapps
```
```
sudo mv ROOT/ ROOT_BAK/
```

- Tomcat μ¬μμ `λλ μΌμ  μκ°μ΄ μ§λλ©΄ tomcatμ΄ ROOT.WAR νμΌμ ν΄μ νμ¬ ROOT λλ ν°λ¦¬ μμ±`
```
sudo systemctl restart tomcat9
```

- μ€μ  μ°ΎκΈ°
```
sudo -s
```
```
cd ROOT
```
```
grep -rn "192.168."
```

- μ€μ  λ³κ²½ `ν΄λΉ νμΌλ€ μμ μλ λ΄μ©μ Development Server IPλ‘ μ€μ `
```
joinAction.jsp
success.jsp
loginAction.jsp
```
```
joinAction.jsp:10:    String jdbcurl = "jdbc:mysql://192.168.0.153:3306/KJOONHWAN";
success.jsp:8:    String jdbcurl = "jdbc:mysql://192.168.0.153:3306/KJOONHWAN";
loginAction.jsp:12:     String jdbcurl = "jdbc:mysql://192.168.0.153:3306/KJOONHWAN";
```

- Tomcat μ¬μμ
```
sudo systemctl restart tomcat9
```

- λμ νμΈ
```
1. νΈμ€νΈ PC μΉ λΈλΌμ°μ μμ Development Server IPλ‘ μ μ
2. λ‘κ·ΈμΈ μ°½ νμΈ λ° νμκ°μ
3. λ‘κ·ΈμΈ

β» adminμΌλ‘ νμκ°μ ν  κ²½μ° κ΄λ¦¬μ νμ΄μ§κ° λνλ¨
β» κ·Έ μΈ κ³μ μ μΌλ° νμ΄μ§
```

- ROOT.war νμΌ μμ±
```
cd /var/lib/tomcat9/webapps/ROOT/
```
```
jar xvf ROOT.war *
```

### π WASλ‘ ROOT.war νμΌ μ μ‘
- /var/lib/tomcat9/webapps/ λ‘ μ΄λ
```
sudo -s
```
```
cd /var/lib/tomcat9/webapps/
```

- FTP νμΌ μ μ‘
```
ftp [192.168.0.151] #WAS μλ² IP
ftp> cd /var/lib/tomcat9/webapps/
ftp> mput ROOT.war
ftp> exit
```


## π» Log Server
### π λ‘κ·Έ μλ² μ€μ 
- /etc/rsyslog.conf μμ 
```
sudo vim /etc/rsyslog.conf
```
```
module(load="imtcp")             #μ£Όμ μ κ±°
input(type="imtcp" port="514")   #μ£Όμ μ κ±°

$template LogName, "/var/log/rsyslog/%fromhost-ip%/%PROGRAMNAME%.log"         #λ΄μ© μΆκ°
*.* ?LogName                                                                  #λ΄μ© μΆκ°
```

- rsyslog μ¬μμ
```
sudo systemctl restart rsyslog
```

- κ°μ μΈνλΌ μλ² λ³ λλ ν°λ¦¬κ° μμ±μ΄ λμλμ§ νμΈ
```
sudo ls -al /var/log/rsyslog/
```

## π» DB Backup Server
### π DB Backup μλ² μ€μ 
- xinetd rsync μ€μΉ
```
sudo apt-get install -y xinetd rsync
```

- /etc/xinetd.d/rsync μμ± ν λ΄μ© μμ±
```
sudo vim /etc/xinetd.d/rsync
```
```
	service rsync
	{
		disable = no
		socket_type = stream
		wait = no
		user = root
		server = /usr/bin/rsync
		server_args = --daemon
		log_on_failure += USERID
	}
```

- xinetd μ¬μμ
```
sudo systemctl restart xinetd
```

- rsyncλ‘ λ°±μ νμΌμ μ κ°μ Έμ€λμ§ νμΈ
```
sudo -s
```
```
rsync -avz [192.168.0.152]::DBS/mysql_dump_$(date +%Y%m%d).sql /backup/
```

- Cron λ±λ‘μ μν μ μ€ν¬λ¦½νΈ νμΌ μμ±
```
sudo vim /backup/backup.sh
```
```
 #!/bin/bash
 rsync -avz [192.168.0.152]::DBS/mysql_dump_$(date +%Y%m%d).sql /backup/
 ```

- μ¬μ©μμκ² μ€ν κΆν ν λΉ
```
chmod u+x /backup/backup.sh
```

- Crontab λ±λ‘
```
crontab -e
```
```
* * * * * /backup/backup.sh > /backup/backup.log 2>&1
```

- λ°±μ νμΌμ΄ λ§€ λΆ μμ λλμ§ νμΈ
```
ls -al /backup/
```
