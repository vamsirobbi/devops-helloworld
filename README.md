# devops
1. **Create and push source code to repository**: Maven web project in GitHub
* `$ mvn archetype:generate -DgroupId=io.github.likarajo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp`
* push the project files to Git repository

2. **Run Web Server**: (in either of the follwing ways)
* Pull Tomcat docker image and run it
  * `$ docker run -it --rm -d -p 9090:8080 tomcat:8.0`
  * `$ docker ps`  
  `CONTAINER ID        IMAGE          PORTS`  
  `b39e0ace38a0        tomcat:8.0     0.0.0.0:8088->8080/tcp`
* Download, install, and run startup.sh script
  * Set the desired connector port in *$TOMCAT_HOME/conf/server.xml* file
  * `$ $TOMCAT_HOME/bin/startup.sh`
  * `$ ps -ef | grep tomcat`  
 501 11066     1   0  1:40PM ??         0:06.20 /Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/bin/java -Djava.util.logging.config.file=/usr/local/apache-tomcat-x.x.xconf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 -Dignore.endorsed.dirs= -classpath /usr/local/apache-tomcat-x.x.x/bin/bootstrap.jar:/usr/local/apache-tomcat-x.x.x/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/apache-tomcat-x.x.x -Dcatalina.home=/usr/local/apache-tomcat-x.x.x -Djava.io.tmpdir=/usr/local/apache-tomcat-x.x.x/temp org.apache.catalina.startup.Bootstrap start 
* Using the IP and port, open the Tomcat page in web browser  
`https://[IP]:[PORT]`

2A. **Configure the web server**
* Create role(s) and user(s) in the file *$TOMCAT_HOME/conf/tomcat-users.xml*  
  >`<role rolename="manager-gui"/>`  
  `<role rolename="manager-script"/>`  
  `<user username="admin" password="admin" roles="manager-gui, manager-script"/>`  
  `<user username="deployer" password="deployer" roles="manager-script"/>`  
  `<user username="tomcat" password="passw0rd" roles="manager-gui/>`

3. **Run CI/CD Server**: (in either of the following ways)
* Pull Jenkins docker image and run it
  * `$ docker run -it --rm -d -p 8080:8080 jenkins`    
  * `$ docker ps`  
  `CONTAINER ID        IMAGE         PORTS`  
  `b39e0ace38a0        jenkins       0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp` . 
* Download, install, and run jenkins.war file
  * `$ java -jar $JENKINS_HOME/jenkins.war`
  * `$ ps -ef | grep jenkins`  
270 10642     1   0 12:47PM ??         6:25.05 /usr/bin/java -Dfile.encoding=UTF-8 -XX:PermSize=256m -XX:MaxPermSize=512m -Xms256m -Xmx512m -Djava.io.tmpdir=/Users/Shared/Jenkins/tmp -jar /Applications/Jenkins/jenkins.war --httpPort=8080
* Using the IP and port, open the Jenkins page in web browser    
`https://[IP]:[PORT]`
* Jenkins initial setup is required using an admin user created and a password generated   
which can be found at: /var/jenkins_home/secrets/initialAdminPassword OR by `$docker logs <container id>` 

3A. **Configure the CI/CD server**
* Install Git, GitHub plugins
* Install Maven Integration plugin
* Specify JDK installation
* Specify Maven installation
* Install Deploy to Container plugin
* Set Web Server credentials -> Global credentials
  * Use the user (from *$TOMCAT_HOME/conf/tomcat-users.xml*) with *manager-script* role

4. **Create New Job and configure for Integration i.e. Build**
* Specify Project name: devops-helloworld
* Specify Repository URL: https://github.com/likarajo/devops-helloworld.git
* Specify Branch: master
* Specify Build -> Root POM: helloworld/pom.xml; Goals and Options: clean install package
* Build the project

5. **Configure the job for Deployment**
* Specify Post-build Actions: Deploy war/ear to a container
  * WAR/EAR Files: \**/*.war (the workspace directory)
  * Add Container: Tomcat 8.x; Credentials:<of manager-script user>; URL:<ip and port>
* Build the project
 
6. **On successful build**
* the war file gets deployed to *$TOMCAT_HOME/webapps*

7. **Configure the job for Continuous Integration/Build and Deployment (CI CD)**
* Check the required option(s) in Build Triggers section

 




