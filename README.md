# Master-Master Database Replication With Odoo
(Tested with postgresql version 14)
**Prerequisite**
Please ensure the following:
    1. Psycopg2 or psycopg2-binary should be present in the system
    2. Postgres and pglogical should be installed in the system
    3. After installation of pglogical in the system, configuration is required before running the script
    4. The database which is going to be used for replication should be present in the system.
    5. The details of the database should be correctly entered in the details file.
**Installation of the required package:**
1. Run the following command if you are using a linux terminal for installing psycopg2 and psycopg2-binary:
        i. sudo pip3 install psycopg2
        ii. sudo pip3 install psycopg2-binary
2. Run the following command in docker bash if you are using docker:
        i. pip3 install psycopg2
        ii. pip3 install psycopg2-binary
3. Installation of postgres and pglogical in the system:
    i. curl https://dl.2ndquadrant.com/default/release/get/deb | sudo bash
    ii. sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
    iii.  sudo apt-get install wget ca-certificates
    iv. wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
    v. sudo apt-get update
    vi. sudo apt install postgresql-14 postgresql-contrib-14 
    vii.  sudo apt-get install postgresql-14-pglogical

**Replication Configuration:**
1. Server Configuration
    i. First create a user for replication (here we’ve used ubuntu user for replication)
    **createuser -s --replication ubuntu**
    ii. Modify the pg_hba.conf file of the postgres
	**cd /etc/postgresql/14/main**
    **nano pg_hba.conf**
		_Update the file as per following_
        **local   all         	postgres                            	peer**
        **# TYPE  DATABASE    	USER        	ADDRESS             	METHOD**
        **# "local" is for Unix domain socket connections only**
        **#local   all         	all                                 	peer**
        **local	all         	all                                 	trust**
        **# IPv4 local connections:**
        **host	all         	all          	127.0.0.1/32        	trust**
        **host	replication 	ubuntu         	127.0.0.1/32        	trust**
        **# IPv6 local connections:**
        **host	all         	all         	::1/128             	trust**
        **# Allow replication connections from localhost, by a user with the**
        **# replication privilege.**
        **#local   replication 	postgres                            		peer**
        **#host	replication 	postgres    	127.0.0.1/32        	md5**
        **#host	replication 	postgres    	::1/128             	md5**
    iii. Modify the Postgresql.conf file:
       **cd /etc/postgresql/14/main**
        **nano postgresql.conf**
        _Make the changes as per following:_
         listen_addresses = 'localhost'
         wal_level = 'logical' 
        max_worker_processes = 10 
        max_replication_slots = 10 
        max_wal_senders = 10
        shared_preload_libraries = 'pglogical'
2. Restart postgres server to implement your changes:
        **Service postgresql restart**
3. Database Creation:
    **createdb -p &lt;postgres port> -O &lt;user for replication> &lt;database name>**

**Creating replication for the database**
    1. Enter the details of the two databases in the details.txt file 
    2. Run the database replication script
    3. When a new module or table is added to the database then run the database table and sequence update script
     4. In case the replication goes down then run the database table and sequence update script again.
