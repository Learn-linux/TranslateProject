Translating by ZTinoZ
How to Install Bugzilla 4.4 on Ubuntu / CentOS 6.x
================================================================================
Here, we are gonna show you how we can install Bugzilla in an Ubuntu 14.04 or CentOS 6.5/7. Bugzilla is a Free and Open Source Software(FOSS) which is web based bug tracking tool used to log and track defect database, its Bug-tracking systems allow individual or groups of developers effectively to keep track of outstanding problems with their product. Despite being "free", Bugzilla has many features its expensive counterparts lack. Consequently, Bugzilla has quickly become a favorite of thousands of organizations across the globe.

Bugzilla is very adaptable to various situations. They are used now a days in different IT support queues, Systems Administration deployment management, chip design and development problem tracking (both pre-and-post fabrication), and software and hardware bug tracking for luminaries such as Redhat, NASA, Linux-Mandrake, and VA Systems.

### 1. Installing dependencies ###

Setting up Bugzilla is fairly **easy**. This blog is specific to Ubuntu 14.04   and CentOS 6.5 ( though it might work with older versions too )

In order to get Bugzilla up and running in Ubuntu or CentOS, we are going to install Apache webserver ( SSL enabled ) , MySQL database server and also some tools that are required to  install and configure Bugzilla.

To install Bugzilla in your server, you'll need to have the following components installed:

- Per l(5.8.1 or above)
- MySQL
- Apache2
- Bugzilla
- Perl modules
- Bugzilla using apache

As we have mentioned that this article explains installation of both Ubuntu 14.04 and CentOS 6.5/7, we will have 2 different sections for them.

Here are the steps you need to follow to setup Bugzilla in your Ubuntu 14.04 LTS and CentOS 7:

**Preparing the required dependency packages:**

You need to install the essential packages by running the following command:

**For Ubuntu:**

    $ sudo apt-get install apache2 mysql-server libapache2-mod-perl2
    libapache2-mod-perl2-dev libapache2-mod-perl2-doc perl postfix make gcc g++

**For CentOS:**

    $ sudo yum install httpd mod_ssl mysql-server mysql php-mysql gcc perl* mod_perl-devel

**Note: Please run all the commands in a shell or terminal and make sure you have root access (sudo) on the machine.**
 
### 2. Running Apache server ###

As you have already installed the apache server from the above step, we need to now configure apache server and run it. We'll need to go for sudo or root mode to get all the commands working so, we'll gonna switch to root access.

    $ sudo -s

Now, we need to open port 80 in the firewall and need to save the changes.

    # iptables -I INPUT -p tcp --dport 80 -j ACCEPT
    # service iptables save

Now, we need to run the service:

For CentOS:

    # service httpd start

Lets make sure that Apache will restart every time you restart the machine:

    # /sbin/chkconfig httpd on

For Ubuntu:

    # service apache2 start

Now, as we have started our apache http server, we will be able to open apache server at IP address of 127.0.0.1 by default.

### 3. Configuring MySQL Server ###

Now, we need to start our MySQL server:

For CentOS:

    # chkconfig mysqld on
    # service start mysqld

For Ubuntu:

    # service mysql-server start

![mysql](http://blog.linoxide.com/wp-content/uploads/2014/12/mysql.png)

Login with root access to MySQL and create a DB for Bugzilla. Change “mypassword” to anything you want for your mysql password. You will need it later when configuring Bugzilla too.

For Both CentOS 6.5 and Ubuntu 14.04 Trusty

    # mysql -u root -p

    # password: (You'll need to enter your password)

    # mysql > create database bugs;

    # mysql > grant all on bugs.* to root@localhost identified by "mypassword";

    #mysql > quit

**Note: Please remember the DB name, passwords for mysql , we'll need it later.**

### 4. Installing and configuring Bugzilla ###

Now, as we have all the required packages set and running, we'll want to configure our Bugzilla.

So, first we'll want to download the latest Bugzilla package, here I am downloading version 4.5.2 .

To download using wget in a shell or terminal:

    wget http://ftp.mozilla.org/pub/mozilla.org/webtools/bugzilla-4.5.2.tar.gz

You can also download from their official site ie. [http://www.bugzilla.org/download/][1]

**Extracting and renaming the downloaded bugzilla tarball:**

    # tar zxvf bugzilla-4.5.2.tar.gz -C /var/www/html/

    # cd /var/www/html/

    # mv -v bugzilla-4.5.2 bugzilla

 

**Note**: Here, **/var/www/html/bugzilla/** is the directory where we're gonna **host Bugzilla**.

Now, we'll configure buzilla:

    # cd /var/www/html/bugzilla/

# ./checksetup.pl --check-modules

![bugzilla-check-module](http://blog.linoxide.com/wp-content/uploads/2014/12/bugzilla2-300x198.png)

After the check is done, we will see some missing modules that needs to be installed And that can be installed by the command below:

    # cd /var/www/html/bugzilla
    # perl install-module.pl --all

This will take a bit time to download and install all dependencies. Run the **checksetup.pl –check-modules** command again to verify there are nothing left to install.

Now we'll need to run the below command which will automatically generate a file called “localconfig” in the /var/www/html/bugzilla directory.

    # ./checksetup.pl

Make sure you input the correct database name, user, and password we created earlier in the localconfig file

    # nano ./localconfig

    # checksetup.pl

![bugzilla-success](http://blog.linoxide.com/wp-content/uploads/2014/12/bugzilla-success.png)

If all is well, checksetup.pl should now successfully configure Bugzilla.

Now we need to add Bugzilla to our Apache config file. so, we'll need to open /etc/httpd/conf/httpd.conf (For CentOS) or etc/apache2/apache2.conf (For Ubuntu) with a text editor:

For CentOS:

    # nano /etc/httpd/conf/httpd.conf

For Ubuntu:

    # nano etc/apache2/apache2.conf

Now, we'll need to configure Apache server we'll need to add the below configuration in the config file:

    <VirtualHost *:80>
         DocumentRoot /var/www/html/bugzilla/
    </VirtualHost>

    <Directory /var/www/html/bugzilla>
         AddHandler cgi-script .cgi
         Options +Indexes +ExecCGI
         DirectoryIndex index.cgi
         AllowOverride Limit FileInfo Indexes
    </Directory>

Lastly, we need to edit .htaccess file and comment out “Options -Indexes” line at the top by adding “#”

Lets restart our apache server and test our installation.

For CentOS:

    # service httpd restart

For Ubuntu:

    # service apache2 restart

![bugzilla-install-success](http://blog.linoxide.com/wp-content/uploads/2014/12/bugzilla_apache.png)

Finally, our Bugzilla is ready to get bug reports now in our Ubuntu 14.04 LTS and CentOS 6.5 and you can browse to bugzilla by going to the localhost page ie 127.0.0.1 or to your IP address in your web browser .

--------------------------------------------------------------------------------

via: http://linoxide.com/tools/install-bugzilla-ubuntu-centos/

作者：[Arun Pyasi][a]
译者：[译者ID](https://github.com/译者ID)
校对：[校对者ID](https://github.com/校对者ID)

本文由 [LCTT](https://github.com/LCTT/TranslateProject) 原创翻译，[Linux中国](http://linux.cn/) 荣誉推出

[a]:http://linoxide.com/author/arunp/
[1]:http://www.bugzilla.org/download/
