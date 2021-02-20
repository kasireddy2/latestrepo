### create a nano EC2 instance
* Open https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fconsole.aws.amazon.com%2Fconsole%2Fhome%3Fregion%3Dus-east-1%26state%3DhashArgs%2523%26isauthcode%3Dtrue&client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fhomepage&forceMobileApp=0&code_challenge=kjgCo5TsLR7cx-PxzUq9LPsn0Kt1n3zMf5WBWGqLhEo&code_challenge_method=SHA-256 in any browser
* Select Root user
* In root user email adress enter aws registered mail id
* Enter password for that account
* click on services
* select EC2 under compute
* click on instances under instances section 
* click on launch instances
* select ubuntu 18.4 image 
* In instance type choose t2
* In configure instance details . keep all fields as default.
* In storage also keep as it is.
* In add tags also keep as defaults only.
* In configure security group . select add rule: Type as All traffic & Source as anywhere. Click on Review & Launch
* In step 7 , click on Launch.
* Generate a key pair & Launch instance.
### Create a Git hub account
* Go to github.com
* Create a git hub account with a username,email and password.
* once the hub is created , create a New repository with public access
### Create a Docker hub account
* Go to https://hub.docker.com/signup
* Create a docker hub account with a docker id,email and password.reate a Docker hub account.
### Create a Nexus repo account
* copy the external ip of the instance created in aws and using this ip and pemkey open a session in mobax or putty
* give username as ubuntu 
* nano nexus_installation.sh  
* copy and paste this in the scripted file
---------------------------------------------------------
echo "installing java before starting using other script in this repo"
cd /opt
echo "Download Nexus tar file"
wget http://download.sonatype.com/nexus/3/nexus-3.22.0-02-unix.tar.gz
echo "Extract the tar file"
tar -xvf nexus-3.22.0-02-unix.tar.gz
echo "Rename the nexus tar file to nexus"
mv nexus-3.22.0-02 nexus
echo "make user changes in nexus.rc"
sed -i 's/#run_as_user=""/run_as_user="root"/' /opt/nexus/bin/nexus.rc
echo "Modify memory settings"
sed -i 's/2703/512/' /opt/nexus/bin/nexus.vmoptions
echo "create nexus.service file"
touch /etc/systemd/system/nexus.service
echo "[Unit]" >> /etc/systemd/system/nexus.service
echo "Description=nexus service" >> /etc/systemd/system/nexus.service
echo "After=network.target" >> /etc/systemd/system/nexus.service
echo "[Service]" >> /etc/systemd/system/nexus.service
echo "Type=forking" >> /etc/systemd/system/nexus.service
echo "LimitNOFILE=65536" >> /etc/systemd/system/nexus.service
echo "User=root" >> /etc/systemd/system/nexus.service
echo "Group=root" >> /etc/systemd/system/nexus.service
echo "ExecStart=/opt/nexus/bin/nexus start" >> /etc/systemd/system/nexus.service
echo "ExecStop=/opt/nexus/bin/nexus stop" >> /etc/systemd/system/nexus.service
echo "User=root" >> /etc/systemd/system/nexus.service
echo "Restart=on-abort" >> /etc/systemd/system/nexus.service
echo "[Install]" >> /etc/systemd/system/nexus.service
echo "WantedBy=multi-user.target" >> /etc/systemd/system/nexus.service

chmod 755 /etc/systemd/system/nexus.service

echo "start nexus service"
serv=nexus
sstat=$(pidof $serv | wc -l )
if [ $sstat -gt 0 ]
then
echo "$serv running!!"
else
systemctl start $serv
echo "$serv service is UP now!!!"
fi
echo "you can access it in the browser by URL — http://public_dns_name:8081 . Wait for couple of minutes before the url is accessible"
echo "default username is admin, password is `cat /opt/sonatype-work/nexus3/admin.password` "
-----------------------------------------------------
* sh nexus_installation.sh 

### Build a Jenkins server
* copy the external ip of the instance created in aws and using this ip and pemkey open a session in mobax or putty
* give username as ubuntu 
* nano java_installation.sh
* copy the below and paste it in the script file
----------------------------------------------------------
#!/bin/bash

echo "update your vm"
apt-get -y update

echo "Installing java version 8"
apt install -y openjdk-8-jdk

echo "check java version"
java -version

echo "export java varaibales"

echo "JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64/bin/java"" >> /etc/environment

echo "Restart source file"
source /etc/environment
echo "check JAVA_HOME path"
echo $JAVA_HOME
------------------------------------------------------------
* sh java_installation.sh
* nano jenkins_installation.sh
* copy the below commands and paste it in the script file
--------------------------------------------------------------
#!/bin/bash
echo "installing java using other script in this repo"
sudo bash ../Java/install_java8.bash
echo "Installing jenkins server"
echo "Adding the Jenkins repository key to your system"
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
echo "Adding the repository in your apt configuration"
echo "deb https://pkg.jenkins.io/debian-stable binary/" | sudo tee -a /etc/apt/sources.list
echo "update your package"
apt-get -y update
echo "Install jenkins"
apt-get -y install jenkins
sleep "30"
#echo "Change defualt port to 8082"
#sed -i 's/8080/8082/' /etc/default/jenkins
echo "Allowing users to signup"
sed -i 's/<disableSignup>true<\/disableSignup>/<disableSignup>false<\/disableSignup>/' /var/lib/jenkins/config.xml
echo "Restart jenkins server"
systemctl restart jenkins
echo "Iniatial admin password"
cat /var/lib/jenkins/secrets/initialAdminPassword
echo "access your server on browser by <public-ip>:8080"$ cat jenkins_installation_8080.sh 
#!/bin/bash
echo "installing java using other script in this repo"
sudo bash ../Java/install_java8.bash
echo "Installing jenkins server"
echo "Adding the Jenkins repository key to your system"
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
echo "Adding the repository in your apt configuration"
echo "deb https://pkg.jenkins.io/debian-stable binary/" | sudo tee -a /etc/apt/sources.list
echo "update your package"
apt-get -y update
echo "Install jenkins"
apt-get -y install jenkins
sleep "30"
#echo "Change defualt port to 8082"
#sed -i 's/8080/8082/' /etc/default/jenkins
echo "Allowing users to signup"
sed -i 's/<disableSignup>true<\/disableSignup>/<disableSignup>false<\/disableSignup>/' /var/lib/jenkins/config.xml
echo "Restart jenkins server"
systemctl restart jenkins
echo "Iniatial admin password"
cat /var/lib/jenkins/secrets/initialAdminPassword
echo "access your server on browser by <public-ip>:8080"
--------------------------------------------------
### Nexus Repository Configuration on Jenkins
* After installing nexus artifact uploader & nexus platform plugins , it needs to be configured
we will provide the details of our nexus server in this plugin
* go to Jenkins Dashboard -> Manage Jenkins -> Configure System -> scrolldown to Sonatype Nexus
* Add Nexus Manager Server -> Nexus Repository Manager 3.x Server
* Display name -> nexus3-server
* Server ID -> NEXUS01
* Server URL -> http://<ip-of-your-vm>:8081
* Credentials -> click Add -> Jenkins -> provide username/password of nexus server that was created just now -> click Add
now for Credentials , select the id thats created just now
* Test Connection , make sure connection succeeded is displayed
* Apply wait for saved
### Create a Jobs
Lets create the first Jenkins Job , its a simple Free style job
go to Jenkins Dashboard
* New Item -> give 01-freestyle-job
* click Freestyle project
* click OK
* under Source Code Management select Git
* for Repository URL give https://github.com/betawins/spring3-mvc-maven-xml-hello-world-1.git
* for Branch to Build give */master
* under Build
* click Add build step -> select Invoke Artifactory Maven 3
* for Maven versio give Maven
* for Goals and Options give clean package
* click Add build step -> select Nexus Artifact Uploader
* Nexus Version give NEXUS3
* Protocol give HTTP
* Nexus Url give <ip-of-your-server>:8081
* Credentials , select the credentials created earlier for nexus user
* GroupId give com.ncodeit
* Version give ${BUILD_NUMBER}
* Repository give maven-releases
* Artifacts , click Add
* ArtifactId give ncodeit-hello-world
* Type give war
* File give target/ncodeit-hello-world-$BUILD_NUMBER.war
* under Post-build Actions
* click Add post-build action
* select Slack Notifications
* select all options
* Apply , wait for saved
* save

### Run jenkins jobs

* go to Jenkins Dashboard -> click the newly created job 01-freestyle-job
* Build now
* under Build History -> click on latest running build
* click Console Output
* Jenkins will download all the dependencies to run the job
* wait till Finished: SUCCESS

### Build Docker images
### Build helm charts
### Build eks cluster
### Run Jenkins helm job 
