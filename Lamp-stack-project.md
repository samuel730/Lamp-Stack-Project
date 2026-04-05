# LAMP Stack Lab Guide

## Introduction
A **LAMP stack** is a combination of open-source software typically installed together to host dynamic websites and web applications written in PHP.  

**LAMP** stands for:  
- **Linux** — Operating System  
- **Apache** — Web Server  
- **MySQL** — Database System  
- **PHP** — Programming Language  

This guide demonstrates how to **set up a LAMP stack** on an Ubuntu 22.04 / Ubuntu Bionic server and test a simple PHP-MySQL project.

---

## Prerequisites
- Ubuntu 22.04 / 18.04 server (Vagrant, VirtualBox, or physical machine)  
- Sudo-enabled user  
- Git installed (optional, for version control)

---

## Step 1 — Install Apache and Update Firewall

```bash
sudo apt update
sudo apt install apache2 -y
sudo ufw allow in "Apache."
```

<img width="1280" height="445" alt="Screenshot 2026-04-03 152548" src="https://github.com/user-attachments/assets/7da809f0-6d1a-4936-8af2-c9dd2c12401f" />
<img width="1015" height="183" alt="Screenshot 2026-04-03 152820" src="https://github.com/user-attachments/assets/d36f5209-b6a0-47d9-8087-5916b329b54d" />
<img width="2529" height="492" alt="Screenshot 2026-04-03 152637" src="https://github.com/user-attachments/assets/b64e398c-c452-4d18-affd-a8e9ef6618b8" />

Test Apache by opening your server IP in a browser:
```
http://localhost:8080
```

## Step 2 — Install MySQL
```
sudo apt install mysql-server -y
sudo mysql
```

<img width="2560" height="1440" alt="image" src="https://github.com/user-attachments/assets/140c15c0-f1e8-4ba3-8209-50526ec1dedb" />
<img width="1958" height="623" alt="Screenshot 2026-04-05 115735" src="https://github.com/user-attachments/assets/059707c7-3951-4413-b836-202596f93589" />
<img width="1245" height="466" alt="Screenshot 2026-04-05 115931" src="https://github.com/user-attachments/assets/b78ccc5c-d15a-4840-8abe-1b2cad39d13f" />
<img width="1496" height="397" alt="Screenshot 2026-04-05 115838" src="https://github.com/user-attachments/assets/26a5ea71-4b87-4b85-9a51-7d0ada93a19a" />

Secure MySQL:
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'StrongRootPassword!';
exit
sudo mysql_secure_installation
```

<img width="1384" height="282" alt="image" src="https://github.com/user-attachments/assets/ba4154e5-0856-4053-ae52-600d1e1aff81" />
<img width="1372" height="1384" alt="image" src="https://github.com/user-attachments/assets/da11fe1a-5f32-4360-9ed8-5f3d283bb891" />
<img width="1692" height="626" alt="image" src="https://github.com/user-attachments/assets/057c9189-456a-4cdc-b263-a19a0f6394a5" />

## Step 3 — Install PHP
```
sudo apt install php libapache2-mod-php php-mysql -y
```

<img width="1990" height="1062" alt="image" src="https://github.com/user-attachments/assets/6047b5a9-abb4-48f3-ae26-111d3f24e6dc" />

Test PHP:
```
sudo nano /var/www/html/info.php
```

<img width="978" height="34" alt="image" src="https://github.com/user-attachments/assets/911392d3-f278-4506-a667-f4252f54dfe0" />

Add:
```
<?php phpinfo(); ?>
```

<img width="2560" height="1440" alt="Screenshot 2026-04-05 120614" src="https://github.com/user-attachments/assets/2a13fbe7-df79-4260-aeaf-f5ab0338b5cc" />

Open in Browser
```
http://localhost:8080/info.php
```
<img width="2560" height="1440" alt="Screenshot 2026-04-05 120733" src="https://github.com/user-attachments/assets/1b9d926a-34f4-4e6a-8a7f-cf8be1d3c800" />
<img width="2560" height="1439" alt="Screenshot 2026-04-05 120719" src="https://github.com/user-attachments/assets/d710ab02-1c67-41d6-ad60-0b2f5fcac1b5" />

Delete the file after testing:
```
sudo rm /var/www/html/info.php
```
<img width="1296" height="44" alt="image" src="https://github.com/user-attachments/assets/0608bc56-9568-4f51-8241-9a23d226bd04" />

## Step 4 — Create a Project Directory
```
sudo mkdir -p /var/www/html/student
sudo chown -R $USER:$USER /var/www/html/student
```
<img width="1314" height="960" alt="image" src="https://github.com/user-attachments/assets/a0f25540-6bf2-4d0a-a5b6-4dfe29aa0531" />

## Step 5 — Create MySQL Database and User
```
sudo mysql -u root -p
```
Inside MySQL:
```
CREATE DATABASE example_database;
CREATE USER 'example_user'@'localhost' IDENTIFIED BY 'StrongPassword!123';
GRANT ALL PRIVILEGES ON example_database.* TO 'example_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

<img width="1314" height="960" alt="image" src="https://github.com/user-attachments/assets/65639914-9003-439a-b8b3-b55b35063ab2" />

## Step 6 — Create a Sample Table
```
mysql -u example_user -p example_database
```
Inside MySQL:
```
CREATE TABLE todo_list (
    id INT AUTO_INCREMENT PRIMARY KEY,
    content VARCHAR(255) NOT NULL
);

INSERT INTO todo_list (content) VALUES
('Learn LAMP stack setup'),
('Test PHP-MySQL connection'),
('Build a small project');
EXIT;
```

<img width="1462" height="932" alt="image" src="https://github.com/user-attachments/assets/3639abf4-533d-40ee-ae3e-138efaa17c9f" />

## Step 7 — Create PHP Script to Test Database Connection
```
nano /var/www/html/student/todo_list.php
```
Paste:
```
<?php
$user = "example_user";
$password = "StrongPassword!123";
$database = "example_database";
$table = "todo_list";

try {
    $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
    echo "<h2>TODO LIST</h2><ol>";
    foreach($db->query("SELECT content FROM $table") as $row) {
        echo "<li>" . $row['content'] . "</li>";
    }
    echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
?>
```
Open in browser:
```
http://localhost:8080/student/todo_list.php
```

You should see the TODO list displayed.

<img width="2560" height="1440" alt="image" src="https://github.com/user-attachments/assets/40478465-4ecf-4941-a69d-ea14ac16908c" />

## Conclusion

You have successfully set up a LAMP stack and tested a simple PHP-MySQL application. Your project is now ready for further development or collaboration on GitHub.
