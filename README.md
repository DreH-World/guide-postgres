# Postgresql on Ubuntu Server

## Install

In this step, you will be installing Postgres on your server. The first thing to do is SSH into your server by running:

```commandline
ssh server_user@server_ip
```
Then input your relevant user password or SSH key password if any. Next, update your server packages and dependencies by running:

```commandline
sudo apt-get update
```

When that is done, install Postgres by running:

```commandline
sudo apt-get install postgresql postgresql-contrib
```

This will install Postgres along with its associated dependencies. When the process is complete, switch the user to postgres to be able to execute Postgres commands with Postgres default user by running:

```commandline
su - postgres
```

The server user will be switched from root to postgres. You can access the Postgres shell by running:

```commandline
psql
```

You will be shown something similar to this:

```commandline
postgres@logrocket:~$ psql
psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1))
Type "help" for help
postgres=#
```

## Settings

### Create User

In this step, you will be creating a new user that will be used to access your Postgres database remotely. To create a new user, exit the Postgres shell by executing:

```commandline
\q
``` 

While still being logged in as postgres run the following command to create a new user:

```commandline
createuser --interactive --pwprompt
```

A prompt will be shown to you asking you to input your desired user role, name, password, and if you want the user to be a superuser. Here is an example:

```commandline
Enter name of role to add: cleopatra
Enter password for new role:
Enter it again:
Shall the new role be a superuser? (y/n) y
```

I named my user role cleopatra and I made my user a superuser. A superuser is a user that has all the privileges available on a Postgres instance. Next, we will be assigning cleopatra to a database. To do this, run the following command:

```commandline
createdb -O cleopatra egypt
```

This command above will create a new database named egypt and assign cleopatra to be the database user.

### Allow remote access

In this step, we will look at how to configure Postgres to accept external connections. To begin, open the configuration file with your preferred editor:

```commandline
nano /etc/postgresql/10/main/postgresql.conf
```

Look for this line in the file:

```commandline
#listen_addresses = 'localhost'
```

Uncomment, and change the value to '*', this will allow Postgres connections from anyone.

```commandline
listen_addresses = '*'
```

Save and exit the file. Next, modify pg_hba.conf to also allow connections from everyone. Open the file with your preferred editor:

```commandline
nano /etc/postgresql/10/main/pg_hba.conf
```

Modify this section:

```commandline
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

To this:

```commandline
# IPv4 local connections:
host    all             all             0.0.0.0/0            md5
```

This file stores the client authentication, each record specifies an IP address range, database name, username, and authentication method. In our case, we are granting all database users access to all databases with any IP address range, thus, letting any IP address connect. Save and exit the file. Next, allow port 5432 through the firewall by executing:

```commandline
sudo ufw allow 5432/tcp
```

Finally, restart Postgres to apply all the changes you have made to its configuration by running:

```commandline
sudo systemctl restart postgresql
```
