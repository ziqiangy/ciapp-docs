# 关于raspberrypi的开始教程-如何使用raspberrypi搭建一个自己的服务器和服务

作者Peter Ziqiang Yuan

https://ziqiangy.github.io/

> 我写这个教程主要是帮助自己记住搭建时候的全过程。并且用简单直接的方式告诉其他人如何快速实现的。

### 全部流程一览

Raspberry pi is Debian Linux system. When you purchase a Pi, you can install Raspberry Pi imager, install linux system into the micro SD card. The system is not readable with pc windows system.

1. install raspberry pi imager, with ssh config, wifi name and password, and name of the pi and so on

2. set hostname: raspberrypi.local

3. enable ssh use password

4. username: ziqiangy

5. password: 1212

6. config wireless LAN

7. set locale settings time zone: America/Chicago

8. start raspberrypi and be able to use ssh to connect from your pc
   install apache, php, mysql

9. use no-ip to get a free ddns domain name and point to your public IP. 

10. In the router, set 80 port forwarding to the static IP you set for you raspberrypi. Use DHCP to assign a static ip to your raspberrypi.

11. Install apache and config for the website.

12. Deploy your web app in /var/www/html, where your apache config point to
    
    1. create customized config.php and database.php file in config folder. change base_url, index_page

13. use let's encrypt certbot to auto assign SSL for your website

14. Now you should be able to access your website.

### ddns以及网站入口等信息(not for publish):

- noip.com(renew ddns with noip every 30 days)

- ziqiangy.ddns.net->75.73.211.115->(my TPlink router) forwarding to 192.168.0.119:80(raspberryPI static IP)

- login to my project:
  username:ziqiangy
  login:PeterBond40

- local dev env: 
  http://codeigniter/ (localhost192.168.0.1)

- local wifi access: 
  http://raspi/ (http://192.168.0.119/)

- remote production env:
  http://ziqiangy.ddns.net/ (http://75.73.211.115/)

### My github token(not for publish)

- username: 
  
  ziqiangy

- token: 

- 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

# Let's get start with the real practice

### 1. prepare micro SD card

Plug in your micro sd card into pc, open miniTool partition wizard, this is easy to clean linux system installed hard disk.

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-11-27-14-09-01-image.png)

Find the disk and select and click delete all partition. The disk will now in unallocated status

Now open Raspberry Pi Imager, I can find my storage disk. I can erase the disk into FAT32 format, after finished, in your computer, you can see this formated disk next to your C drive.

Now, use Raspberrypi imager, choose raspberry pi os lite(32-bit), choose storage card, in settings, use my default setting

```
- set hostname: raspi.local
- enable ssh use password
- username: ziqiangy
- password: 1212
- config wireless LAN
- set locale settings time zone: America/Chicago
```

and write into this micro SD card. After successfully installed, plug this card into the raspberrpi, it should automatically connect to my wifi, and I should be able to use ssh to access the pi.

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-11-27-14-20-25-image.png)

### 2. tplink router setting

In my router setting, I have bind this raspberry pi with a static ip address (192.168.0.119), here is how I set this up:

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-14-43-23-image.png)

In my router's DHCP address reserved, I have reserved the ip address for the raspberrypi MAC address.

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-14-41-13-image.png)

I have also changed the start and end IP address from 115-199 to avoid any IP assigning problems. The bind address need to be in between 115 and 199 to be able to let router assign a valid IP for you. If now you bind with 107 you are not able to be access the internet automaticlly. 

And the Primary DNS should set to 8.8.8.8, and secondary DNS should be 8.8.4.4 according to tplink service desk respond for dynamic dns set up.

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-14-41-48-image.png)

### 3. ddns set up and config

as we are in the router setting, we also set the forwarding port and ddns setting with noip. please check the following screenshots for the set up

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-14-51-33-image.png)

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-14-51-51-image.png)

I got a free hostname from noip.com, I can use this for free but I need to confirm every 30 days on their website.

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-14-52-55-image.png)

### 4. SSH access

now before we do the ssh access, go to the directory: C:\Users\peter\.ssh, and remove the files in there to avoid previous access problems.

Go to the windows host file (`C:\Windows\System32\drivers\etc`) and add 192.168.0.119 to raspi

now open a new terminal and ssh username@hostname, for me it's:

```
ssh ziqiangy@raspi
```

> If you are running into problems with ssh access, ping the ip, and check router settings to see if your device is in the list. checkdhcp reserve ip address, check mac bind ip setting, to manually connect using linux commandline, use commands: `sudo ifconfig wlan0`, and `sudo raspi-config`.

Now I should be able to access using ssh successfully. Use either putty or termianl to do the ssh connect.

### 5. install and config UFW

```bash
sudo apt update
sudo apt upgrade


sudo apt install ufw
sudo ufw app list

#opening the followings:
OpenSSH
SSH (22)
WWW (80)
ftp (21)
ftps (990)


sudo ufw allow SSH ...
...
sudo ufw enable  
```

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-15-56-19-image.png)

### 6. Install and config apache

```
sudo apt update
sudo apt install apache2

sudo systemctl status apache2
```

now if you browse ip address with default 80, you can see the apache default webpage.

Now it's time to config webpage. create a proj.conf, disable 000-default and enable proj, reload apache, create according dir in the proj dir, and check the new site by visiting browser.

use the router forwarding www request to the raspberrypi server and set up the no-ip correct for the ddns. For me if I set everything correct, I type ziqiangy.ddns.net, it should show my proj page correctly.

> For the SSL certificate, after adding it, I am not able to visit the website using the same domain name, I am still troubleshooting this. So for now to be able to access, just use insecure http instead of https for practice.

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-16-07-53-image.png)

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-16-08-05-image.png)

where is my apache config files?

- | ServerRoot           | /etc/apache2                                                                                            |
  | -------------------- | ------------------------------------------------------------------------------------------------------- |
  | DocumentRoot         | /var/www                                                                                                |
  | Apache Config Files  | /etc/apache2/apache2.conf<br/>/etc/apache2/ports.conf                                                   |
  | Default VHost Config | /etc/apache2/sites-available/default, /etc/apache2/sites-enabled/000-default                            |
  | Module Locations     | /etc/apache2/mods-available, /etc/apache2/mods-enabled                                                  |
  | ErrorLog             | /var/log/apache2/error.log                                                                              |
  | AccessLog            | /var/log/apache2/access.log                                                                             |
  | cgi-bin              | /usr/lib/cgi-bin                                                                                        |
  | binaries (apachectl) | /usr/sbin                                                                                               |
  | start/stop           | /etc/init.d/apache2 (start\|stop\|restart\|reload\|force-reload\|start-htcacheclean\|stop-htcacheclean) |

### 7. install php

```bash
sudo apt install php
```

### 8. clone project and test running

```bash
sudo apt install git
cd /var/www/html
mkdir myproj
cd myproj
sudo git clone https://github.com/ziqiangy/CodeIgniter.git codei
```

my project and initiate file location is `/var/www/html/myproj/codei/index.php`

```bash
sudo apt install vim
```

select everyting in 000-default and paste to myproj.conf

1. To select all in Vim, use ggVG.
2. To select and copy all, use $yy.

disable 000 default and enable myproj and test.

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-16-30-38-image.png)

```bash
sudo a2ensite myproj
sudo a2dissite 000-default
sudo systemctl reload apache2
```

now visit the site and something show up, php working and site working.

for reference, a standard vhost config file

```apacheconf
<virtualhost *:80="">  
ServerAdmin webmaster@localhost  
ServerName yourdomain.com  
ServerAlias www.yourdomain.com  
DocumentRoot /var/www/yourdomain.com  
ErrorLog ${APACHE_LOG_DIR}/error.log  
CustomLog ${APACHE_LOG_DIR}/access.log combined  
</virtualhost>
```

### 9. config the project and install mysql

change all the files to 755 to update:

```bash
sudo chmod -R 755 codei
```

change user and group to ziqiangy

```bash
sudo chown -R ziqiangy:ziqiangy myproj
```

add config and database php files into the codeigniter config folder and config them

install mysql and connect to tht database

```bash
sudo apt install mariadb-server
```

```bash
sudo mysql_secure_installation
```

I have set root password to 1212, I allow test db, I allow root user login remotely, I allow to have anonymous user to login to the db without username and password for testing. All these should be removed when moving to production service.

login to mysql:

```bash
sudo mysql -uroot -p
```

add a new user with:

username:peter
password:1212

```bash
create user peter@'%' identified by '1212'
GRANT ALL ON *.* TO peter@'%'
```

in /etc/mysql/my.cnf file, add `bind-address = 0.0.0.0`

```bash
sudo ufw allow mysql
sudo systemctl restart mariadb
```

to check mariadb and the correct result for opening to listening all ip

```bash
netstat -ant | grep 3306
>>>tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN
```

Now I can connect mariadb remotely:

![](C:\Users\peter\AppData\Roaming\marktext\images\2023-12-01-23-56-44-image.png)

now copy and paste the entire database script from local to remote mysql and the database is installed.

Then the config and database files should be configed correctly to run.

Use this link to install correct php7.4 in raspberrypi

[PHP 7.4 on raspbian - Raspberry Pi Stack Exchange](https://raspberrypi.stackexchange.com/questions/108149/php-7-4-on-raspbian)

If facing problems with the mysql, check if mysql driver is installed and the mode is enabled in php.ini

now, let's remove the index.php from app's url.

first in config.php file check `$config["index_page"]=""`;

second, enable apache2 rewrite engine: `sudo a2enmod rewrite`

third, add .htaccess file

```bash
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php/$1 [L]
```

> This seems still not working for my raspi server, I will keep using index.php now for raspi server to make it work correct. Will come back to this problem later.

### 10. about using my project in raspi server

Actually I don't have to use 755 for my server, and I don't need to change anything but add config.php and database.php file to it and it should work.

keep using git clone and git pull.

Just make sure to install composer and node npm to get all the vendors file and all the npm modules.

```bash
sudo apt install nodejs
sudo apt install npm

#check
node -v
npm -v

#go to project directory and do npm install
sudo npm install
```

install composer

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'e21205b207c3ff031906575712edab6f13eb0b361f2085f1f1237b7126d785e826a450292b6cfd1d64d92e6563bbde02') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
php -r "unlink('composer-setup.php');"
```

This installer script will simply check some `php.ini` settings, warn you if they are set incorrectly, and then download the latest `composer.phar` in the current directory. The 4 lines above will, in order:

- Download the installer to the current directory
- Verify the installer SHA-384, which you can also [cross-check here](https://composer.github.io/pubkeys.html "Get the SHA-384 key on GitHub (external link)")
- Run the installer
- Remove the installer

```bash
#maybe missing ext-dom then run
sudo apt install php7.4-xml
#install packages in working directory
composer install
```

now all packages should be installed



### 12.一个模块的开发和持续部署



how I name them:

| 类别         | 命名方式和习惯               | 记忆方法 |
| ---------- | --------------------- | ---- |
| DB         | recipes_list          | 复数   |
| Model      | RecipesList           | 复数连字 |
| Controller | Recipelist->url可以引用小写 | 单数连字 |
| views      | recipelist/           | 单数小写 |



##### 12.1. create table `receipes_list` in database

first, create db migrate

```bash
./vendor/bin/doctrine-migrations generate
```

create db table, use `receipe list`  as example

```php
public function up(Schema $schema): void
    {
        // this up() migration is auto-generated, please modify it to your needs

        $this->addSql(
            "CREATE TABLE `recipes_list` (
                `id` int(11) NOT NULL AUTO_INCREMENT,
                `name` varchar(500) NOT NUll,
                `detail` varchar(2000) NULL DEFAULT NULL,
                `user_id` int(11) DEFAULT NULL,
                `insert_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
                `update_time` timestamp NULL DEFAULT NULL,
                PRIMARY KEY (`id`),
                KEY `user_id` (`user_id`)
              ) ENGINE=InnoDB CHARSET=utf8 COLLATE=utf8_unicode_ci"
       );
    } 

public function down(Schema $schema): void
    {
        // this down() migration is auto-generated, please modify it to your needs

        $this->addSql(
            "drop table recipes_list"
        );
    }
```





migrate to create table `receipes_list` in the database

```bash
$ ./vendor/bin/doctrine-migrations migrate
```

##### 

##### 12.2 create model `ReceipeList.php`

```php
<?php
class RecipesList extends CI_Model{
    public function insert($data){
        $this->db->insert("recipes_list",$data);
    }
    public function update($id,$data){
        $this->db->update("recipes_list",$data,array('id'=>$id));
    }
    public function displayWithId($id){
        $res = $this->db->get_where("recipes_list",array('id'=>$id),1,0)->result_array();
        return $res;
    }
    public function list($user_id){
        $res = $this->db->get_where("recipes_list",array('user_id'=>$user_id))->result_array();
        return $res;
    }
    public function delete($id){
        $this->db->delete("recipes_list",array("id"=>$id));
    }
}
```



##### 12.3 create controller `Recipelist.php`

```php
<?php
class Recipelist extends CI_Controller{
    public $user_id;
    function __construct(){
        parent::__construct();
        if(!isset($_SESSION['user_id'])) {
            $this->session->set_flashdata("auth",'Not authorized user, login first');
            redirect('user/login');
            exit;
        };
        $this->user_id = $_SESSION['user_id'];
        $this->load->model("RecipesList");
    }

    public function insert(){
        if($this->input->server("REQUEST_METHOD")=="GET"){
            $this->load->view("templates/header");
            $this->load->view("recipelist/insert");
        }elseif($this->input->server("REQUEST_METHOD")=="POST"){
            $form_data = $this->input->post();
            $data = array(
                "user_id" => $this->user_id,
                "name" => $form_data["name"],
                "detail" => $form_data["detail"]
            );
            
            $this->RecipesList->insert($data);
            redirect("recipelist/list");
        }
    }

    public function update($id = NULL){
        if($this->input->server("REQUEST_METHOD")=="GET"){
            
            [$data] = $this->RecipesList->displayWithId($id);
            $this->load->view("templates/header");
            $this->load->view("recipelist/update",$data);
        }elseif($this->input->server("REQUEST_METHOD")=="POST"){
            $form_data = $this->input->post();
            $data = array(
                "name" => $form_data["name"],
                "detail" => $form_data["detail"]
            );
            $this->RecipesList->update($form_data["id"],$data);
            redirect("recipelist/list");
        }
    }
    public function list(){
        $res = $this->RecipesList->list($this->user_id);
        $this->load->view("templates/header");
        $this->load->view("recipelist/list",array("data"=>$res));
    }
    public function delete($id){
        $this->RecipesList->delete($id);
        redirect("recipelist/list");
    }

}
```

##### 12.4 create view folder `recipelist/` with `insert.php`, `list.php`, `update.php`

```php
#insert.php
<style>
    .box-father{
        display:flex;
        justify-content:center;
    }
    .box-child{   
        width:400px;
        height:400px;
        border:3px solid black;
        background-color:#fff7d1;
        border-radius:10px;
        padding:15px;
        
    }

    .h-title{
        text-align:center;
    }
    .text-father{
        display:flex;
        justify-content:center;
    }
</style>


<div class="box-father">
    <div class="box-child">
        <div class="h-title">
            <h1>Insert a Recipe</h1>
        </div>
        <div class="text-father">
            <div class="text-child">            
                <?php echo form_open("Recipelist/insert") ?>
                    <label for="text">Recipe title:</label><br>
                    <input type="text" name="name" value=""><br>
                    <label for="detail">Recipe Detail:</label><br>
                    <textarea id="detail" name="detail" rows="10" cols="35"></textarea><br>
                    <input type="submit" value="submit">
                </form>
            </div>
        </div>
    </div>
</div>




```

```php
#list.php
<style>
    .box-father{
        display:flex;
        justify-content:center;
    }
    .box-child{   
        width:400px;
        height:300px;
        border:3px solid black;
        background-color:#fff7d1;
        border-radius:10px;
        padding:15px;
        
    }

    .h-title{
        text-align:center;
    }
    .text-father{
        display:flex;
        justify-content:center;
    }
</style>


<div class="box-father">
    <div class="box-child">
        <div class="h-title">
            <h1>Notes list</h1>
        </div>
        <div class="text-father">
            <div class="text-child">            
            <table>
                <tr>
                    <th>Recipe list</th>    
                </tr>
    
                <?php foreach($data as $d) { ?>            
                <tr class="">                
                    <td><?php echo anchor("Recipelist/update/".$d['id'],$d['name']) ?></td>                            
                </tr>
                <?php } ?>
            </table>        
            </div>
        </div>
    </div>
</div>


```

```php
#update.php
<style>
    .box-father{
        display:flex;
        justify-content:center;
    }
    .box-child{   
        width:400px;
        height:400px;
        border:3px solid black;
        background-color:#fff7d1;
        border-radius:10px;
        padding:15px;
        
    }

    .h-title{
        text-align:center;
    }
    .text-father{
        display:flex;
        justify-content:center;
    }
</style>


<div class="box-father">
    <div class="box-child">
        <div class="h-title">
            <h1>Update Recipe</h1>
        </div>
        <div class="text-father">
            <div class="text-child">                            
            <?php echo form_open("Recipelist/update") ?>
                <label for="text">Recipe Title:</label><br>
                <input type="text" name="name" value="<?php echo $name ?>"><br>
                <label for="detail">Recipe Detail:</label><br>
                <textarea id="detail" name="detail" rows="10" cols="35"><?php echo $detail ?></textarea><br>
                <input type="hidden" name="id" value="<?php echo $id ?>">
                <input type="submit" value="submit">
            </form>
                <?php echo anchor("Recipelist/delete/".strval($id),"delete") ?>
            </div>
        </div>
    </div>
</div>


```

##### 12.5 add to menu

```php
 <div class="dropdown">
    <button class="dropbtn">Recipe List
      <i class="fa fa-caret-down"></i>
    </button>
    <div class="dropdown-content">
    <?php echo anchor("Recipelist/insert","Insert a Recipe"); ?>
  <?php echo anchor("Recipelist/list","Show My Recipes List"); ?>
    </div>
  </div>
```



after git commit and push, in the server, use git pull and run the database migration to add the table to the live database in server `./vendor/bin/doctrine-migrations migrate`. All Done!



> PHP小知识：
> 
> empty和isset的区别
> 
> 



|         | ""    | "apple" | NULL  | FALSE | 0     | undefined    | TRUE  | array() | 123   |
| ------- | ----- | ------- | ----- | ----- | ----- | ------------ | ----- | ------- | ----- |
| isset   | TRUE  | TRUE    | FALSE | TRUE  | TRUE  | FALSE        | TRUE  | TRUE    | TRUE  |
| empty   | TRUE  | FALSE   | TRUE  | TRUE  | TRUE  | TRUE         | FALSE | TRUE    | FALSE |
| is_null | FALSE | FALSE   | TRUE  | FALSE | FALSE | Warning/TRUE | FALSE | FALSE   | FALSE |
