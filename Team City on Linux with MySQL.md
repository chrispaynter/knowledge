# Installing Team City on Linux with MySQL
You'll need a machine with at least 2GB of ram to run Team City without crashing the server, so if you're running Linux in Amazon go with a _t2.micro_ sized instance at the very least.

## Step 1: Install Team City
I installed mine at this path, but you can put it anywhere you want.
`mkdir /var/www/apps/teamcity`
`cd /var/www/apps/teamcity`

Download team city from the Jet Brains server. You'll need to update the version to the latest.
`wget http://download.jetbrains.com/teamcity/TeamCity-8.1.5.tar.gz`

Then extract the files and we're done (for now)
`tar xpf TeamCity-8.1.5.tar.gz`

## Step 2: Install MySQL
I'm using MySQL to store TeamCity data, but you can install any of the [supported databases](https://confluence.jetbrains.com/display/TCD10/Setting+up+an+External+Database).

`sudo yum install mysql56-server`
`sudo service mysqld start`

## Step 3: Create the Team City database
Type `mysql` into the command line to open up a mysql terminal, then follow these commands. I've taken the liberty of naming the database `TeamCity` and the user `teamcityuser`, but you can change it to what you like. Also make sure you include a password to replace `<password>`

1) Create the database
`create database TeamCity collate utf8_bin;`

2) Create a new user with password
`create user 'teamcityuser'@'localhost' identified by '<password>';`

3) Grant privledges and permissions
`grant all privileges on TeamCity.* to 'teamcityuser'@'localhost';`
`grant process on *.* to 'teamcityuser'@'localhost';`

Type `exit` and then you can test your connection works with this command
`mysql --user=teamcityuser --password=<password> TeamCity`

## Step 4: Install the Java MySQL Connector
Team City is written in java and in order to speak with MySQL you'll need to install the MySQL java connector.

`yum install mysql-connector-java`

We'll download it to the tmp directory first, so first move there
`cd /tmp`

Then grab it from the MySQL server (be sure to get the latest version number)
`wget http://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java-5.1.39.tar.gz`

Then extract the files
`tar xpf mysql-connector-java-5.1.39.tar.gz`

And move them to the Team City directory. Mine installed to `/root` but may change in different installations.
`cp  mysql-connector-java-5.1.39/mysql-connector-java-5.1.39-bin.jar /root/.BuildServer/lib/jdbc`

## Step 5: Start Team City
You can start Team City with the following command:
`/var/www/apps/teamcity/TeamCity/bin/runAll.sh start`

Once Team City is running, you should now be able to go to a browser and visit `http://yourdomain.com:8111` and be greeted with the installation screen.

You'll have to make sure that you have open up port 8111 on your server. If using AWS, this means adding that port to the Security Group that your instance is using.

Note at this point, you may also get a message saying your java version is incompatible. See below _A Note on Java_

Once you have the installation running, when setting up the database, follow this screenshot:
![Team City Database Setup](/img/teamcity-db-setup.png?raw=true)

You can also stop it by replacing start with stop:
`/var/www/apps/teamcity/TeamCity/bin/runAll.sh stop`

## A Note on Java
There's a good chance that you'll have to update Java if you are using the latest version of Team City. You'll know, because Team City will present a message to you in the browser, and not let you proceed any further with the installation. Here's how to update Java thanks to this [Stack Overflow post](http://serverfault.com/questions/664643/how-can-i-upgrade-to-java-1-8-on-an-amazon-linux-server).

Note that the versions may be different depending on your server and the version of Team City you are using.

1) Install Java Runtime 1.8
`sudo yum install java-1.8.0`

2) Then use the alternatives command to make Java 1.8 the default.
`sudo /usr/sbin/alternatives --config java`

3) If you prefer you can remove Java 1.7 with this command but remove it after you installed Java 1.8 or the aws-apitools will also be removed as they depend on Java on being installed.
`sudo yum remove java-1.7.0-openjdk`



