
# **Deploying Jenkins War File in Tomcat**

Launch an ubuntu instance 

![Screenshot Capture - 2024-01-25 - 14-24-01](https://github.com/keedevops/Jenkins/assets/155215036/447c2ae4-c307-4eca-b3c9-b0fcc42a3b26)

Connect the instance and upgrade the system with the following commands

```
sudo apt-get update -y
sudo apt-get upgrade -y

```
**Installing Java 11**
```
sudo apt-get install openjdk-11-jdk -y
java -version

```

**Installing Tomcat**

```
wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.83/bin/apache-tomcat-9.0.83.tar.gz

```

**Creating a directory for Tomcat**


```
mkdir /etc/tomcat
sudo mkdir /etc/tomcat
sudo tar -xf apache-tomcat-9.0.83.tar.gz -C /etc/tomcat/
sudo chown ubuntu:ubuntu /etc/tomcat/ -R
sudo chmod +x /etc/tomcat/apache-tomcat-9.0.83/bin/*.sh

```

**To find the java home directory try using this command**

```
$(dirname $(dirname $(readlink -f $(which javac))))

```
**To run tomcat as a service**

```
sudo nano /etc/systemd/system/tomcat.service

```

**Copy the contents below and paste it onto the service file**

```
[Unit]
Description=Tomcat 9 servlet container
After=network.target

[Service]
Type=forking

User=root
Group=root

Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom -Djava.awt.headless=true"

Environment="CATALINA_BASE=/etc/tomcat/apache-tomcat-9.0.83"
Environment="CATALINA_HOME=/etc/tomcat/apache-tomcat-9.0.83"
Environment="CATALINA_PID=/etc/tomcat/apache-tomcat-9.0.83/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/etc/tomcat/apache-tomcat-9.0.83/bin/startup.sh
ExecStop=/etc/tomcat/apache-tomcat-9.0.83/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```

**Starting the Tomcat as service**

To restart the daemon to notify systemd that a new unit file exists and start the Tomcat service

```
sudo systemctl daemon-reload
sudo systemctl start tomcat.service
sudo systemctl status tomcat.service

```
Check the security details in the instance whether we have custom TCP permissions for port 8080 to run the tomcat service

![image](https://github.com/keedevops/Jenkins/assets/155215036/9025022a-f3f7-4767-add1-e6e263de0cc0)

When the home page website appears our tomcat service is working

To get jenkins latest war file i reffered this link https://vikash-sharma749.medium.com/jenkins-installation-steps-with-war-file-in-linux-2da1be169f1

![Screenshot Capture - 2024-01-25 - 14-49-49](https://github.com/keedevops/Jenkins/assets/155215036/dac9a92e-9e8b-4673-b0bc-4513f5633cac)

```
wget https://get.jenkins.io/war-stable/2.289.2/jenkins.war

```
Now we have downloaded the jenkins war file in the root both jenkins.war and apache-tomcat-9.0.83 will be downloaded in the same root
```
ls -la
```
This will list all the files in the root directory. We have to now copy the jenkins.war file to tomcat webapps folder to run jenkins in the tomcat as a extra security layer

```
sudo find / -name "webapps"  

```
The path will appear like this 
```
/etc/tomcat/apache-tomcat-9.0.83/webapps

```
**Now copy the jenkins.war file to the webapps**

```
 sudo cp jenkins.war /etc/tomcat/apache-tomcat-9.0.83/webapps

```
**To check whether the file is copied onto the directory**

```
ls /etc/tomcat/apache-tomcat-9.0.83/webapps

```
After copying our jenkins.war file to the tomcat webapps we should be getting a page like this

![image](https://github.com/keedevops/Jenkins/assets/155215036/f04726e5-80cd-4184-a31c-4b8613020d1f)

To unlock the key in the cli try this command

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

```


