 
PROJECT 4 – CI/CD PIPELINE FOR 
JAVA APPLICATION DEPLOYMENT 
USING GITHUB ACTIONS 
1.	TECHNOLOGIES USED 
	Technology 	Purpose 
AWS EC2 	Hosting application servers 
AWS AMI 	Creating identical server configurations 
Ubuntu Linux 	Operating system 
Java 17 	Java development and runtime 
Maven 	Building Java applications 
Apache Tomcat 9 Hosting Java web applications 
GitHub 	Source code management 
GitHub Actions 	CI/CD automation 
WAR 	Application deployment package 
2.	AWS EC2 INSTANCE CREATION 
1.	Open the AWS Management Console. 
2.	Navigate to the EC2 service. 
3.	Click Launch Instance. 
4.	Enter the instance name as dev-server. 
5.	Select Ubuntu Server as the AMI. 
6.	Select the required instance type. 
7.	Select the required VPC and subnet. 
8.	Enable Auto-assign Public IP. 
9.	Configure the Security Group: 
o	SSH (Port 22) → My IP. 
o	Custom TCP (Port 8080) → Anywhere (0.0.0.0/0). 
10.	Launch the EC2 instance. 
11.	Wait until the instance state becomes Running. 
12.	Connect to the instance using EC2 Instance Connect. 
Screenshot 1: DEV server running in AWS EC2 
  
3.	JAVA AND MAVEN INSTALLATION 
3.1	Update Ubuntu Packages 
1.	Connect to the DEV server terminal. 
2.	Switch to the root user. 
3.	Update the Ubuntu package repository. 
Commands: 
sudo -i apt update -y 
3.2	Install Java 
1.	Execute the following command to install Java 17. apt install openjdk-17-jdk -y 
2.	Verify the Java installation. 
java -version 
3.3	Install Maven 
1.	Execute the following command to install Maven. apt install maven -y 
2.	Verify the Maven installation. 
mvn -version 
Screenshot 2: Java and Maven installation with version verification. 
  
4.	APACHE TOMCAT 9 INSTALLATION AND CONFIGURATION 
4.1	Download Apache Tomcat 9 
1.	Open the official Apache Tomcat website. 
2.	Navigate to the Tomcat 9 download page. 
3.	Locate the Binary Distributions section. 
4.	Select the Core TAR.GZ package. 
5.	Copy the download link. 
6.	Return to the EC2 terminal. 
7.	Execute the wget command using the copied download link. 
Command: wget <copied-tomcat-download-link> 
8.	Verify the downloaded file. 
ls 
4.2	Extract the TAR.GZ File 
1.	Extract the downloaded Tomcat archive. tar -xf apache-tomcat-9*.tar.gz 
2.	List the files and directories. 
ls 
3.	Rename the extracted Tomcat directory. mv apache-tomcat-9* tomcat 
4.	Navigate to the Tomcat directory. 
cd tomcat 
4.3	Configure Host Manager 
1. Navigate to the Tomcat directory. 
cd bin cd .. 
2.	Navigate to the webapps directory. cd webapps 
3.	Open the Host Manager directory. 
cd host-manager ls 
4. Navigate to the META-INF directory. 
cd META-INF ls 
5.	Open the context.xml file. vi context.xml 
6.	Locate the existing Valve access restriction configuration. 
7.	Comment out the relevant Valve section using HTML comment tags. 
<!-- 
Existing Valve configuration --> 
8.	Save and exit the file. 
4.4	Configure Manager Application 
1.	Navigate back to the webapps directory. cd ../.. 
2.	Open the Manager directory. 
cd manager ls 
3. Navigate to the META-INF directory. 
cd META-INF ls 
4. Open the context.xml file. 
vi context.xml 
5.	Locate the existing Valve access restriction configuration. 
6.	Comment out the relevant Valve section using HTML comment tags. 
7.	Save and exit the file. 
4.5	Configure Tomcat Users 
1. Navigate to the Tomcat configuration directory. 
cd ../.. cd cd tomcat cd conf 
2.	List the configuration files. 
ls 
3.	Open the tomcat-users.xml file. vi tomcat-users.xml 
4.	Add the required Tomcat Manager roles. 
Configuration: 
<role rolename="manager-gui"/> 
<role rolename="manager-script"/> 
 
<user username="YOUR_USERNAME"       password="YOUR_PASSWORD"       roles="manager-gui,manager-script"/> 
5.	Save and exit the file. 
4.6	Restart Apache Tomcat 
1.	Navigate to the Tomcat directory. 
2.	Open the bin directory. 
cd .. cd bin 
3.	Stop the Tomcat server. 
./shutdown.sh 
4.	Start the Tomcat server. 
./startup.sh 
4.7	Verify Tomcat Installation 
1.	Open a web browser. 
2.	Enter the DEV server public IP address with port 8080. http://DEV-PUBLIC-IP:8080/ 
3.	Verify that the Apache Tomcat welcome page is displayed. 
4.	Click the Manager App option. 
5.	Enter the configured Tomcat username and password. 
6.	Verify that the Manager application opens successfully. 
Screenshot 3: Apache Tomcat welcome page. 
  
Screenshot 4: Tomcat Manager application. 
  
5.	CREATING AMI FROM DEV SERVER 
1.	Open the AWS Management Console. 
2.	Navigate to the EC2 service. 
3.	Select the configured dev-server instance. 
4.	Click the Actions menu. 
5.	Select Image and Templates. 
6.	Click Create Image. 
7.	Enter an appropriate AMI name. 
8.	Provide an optional description. 
9.	Review the image configuration. 
10.	Click Create Image. 
11.	Navigate to the AMIs section. 
12.	Verify that the AMI is successfully created. 
The created AMI contains the configured server environment with Java, Maven and Apache Tomcat. 
Screenshot 5: Successfully created AMI. 
  
6.	LAUNCHING THREE ADDITIONAL EC2 INSTANCES 
1.	Open the AWS EC2 console. 
2.	Navigate to the AMIs section. 
3.	Select the AMI created from the DEV server. 
4.	Click Launch Instance from AMI. 
5.	Enter the instance name as test-server. 
6.	Select the required instance type. 
7.	Configure the VPC and subnet. 
8.	Configure the Security Group. 
9.	Launch the instance. 
10.	Repeat the same process to launch pre-prod-server. 
11.	Repeat the same process to launch prod-server. 
12.	Wait until all three instances reach the Running state. 
6.1	Environment Details 
Instance Name Environment dev-server 	DEV test-server 	TEST pre-prod-server PRE-PROD prod-server 	PROD 
6.2	Verify Software on Additional Servers 
1.	Connect to the TEST server. 
2.	Verify Java installation. 
java -version 
3.	Verify Maven installation. 
mvn -version 
4.	Verify that Apache Tomcat is present. 
5.	Repeat the verification process on the PRE-PROD server. 
6.	Repeat the verification process on the PROD server. 
Screenshot 6: All four EC2 instances running. 
  
NOTE : Java, Maven and Tomcat verification on additional servers. 
 
7.	GITHUB REPOSITORY SECRETS CONFIGURATION 
1.	Open your GitHub account. 
2.	Open the Train Booking project repository. 
3.	Click the Settings tab. 
4.	Navigate to Secrets and variables. 
5.	Select Actions. 
6.	Click New repository secret. 
7.	Create the following secrets: 
	Secret Name 	Value 
TOMCAT_HOST 	DEV server Tomcat URL 
TOMCAT_USER 	Tomcat Manager username TOMCAT_PASSWORD Tomcat Manager password 
8. Save each secret. 
These secrets are used to securely store Tomcat connection details and authentication credentials. 
Screenshot 8: GitHub repository secrets configuration. 
  
8.	CREATING GITHUB ENVIRONMENTS 
1.	Open the GitHub repository. 
2.	Navigate to Settings. 
3.	Select Environments. 
4.	Click New environment. 
5.	Enter the environment name as DEV. 
6.	Click Configure environment. 
7.	Add the following environment secrets: 
o TOMCAT_HOST o TOMCAT_USER o TOMCAT_PASSWORD 
8.	Save the secrets. 
9.	Repeat the same process to create the TEST environment. 
10.	Repeat the same process to create the PRE-PROD environment. 
11.	Repeat the same process to create the PROD environment. 
12.	Configure the respective EC2 server connection details inside each environment. 
Example TOMCAT_HOST value: http://EC2-PUBLIC-IP:8080 8.1 Environment Configuration Environment 	TOMCAT_HOST 
DEV 	DEV server public IP 
TEST 	TEST server public IP 
PRE-PROD 	PRE-PROD server public IP 
PROD 	PROD server public IP 
Screenshot 9: Four GitHub Environments. 
  
Screenshot 10: Environment-specific secrets configuration. 
  
9.	CREATING GITHUB ACTIONS WORKFLOW 
9.1	Create Workflow File 
1.	Open the project repository in VS Code. 
2.	Navigate to the project root directory. 
3.	Create a folder named .github if it does not exist. 
4.	Inside .github, create another folder named workflows. 
5.	Inside the workflows folder, create a file named cicd.yaml. 
6.	Open the cicd.yaml file. 
7.	Add the CI/CD workflow configuration. 
Workflow file location: 
.github/     workflows/         cicd.yaml 
9.2	Complete CI/CD YAML Configuration 
The workflow contains five jobs: 
1.	Build 
2.	Deploy DEV 
3.	Deploy TEST 
4.	Deploy PRE-PROD 
5.	Deploy PROD Complete YAML configuration: 
name: CI-CD Pipeline - Train Booking 
 on:   push:     branches:       - main   workflow_dispatch: 
 jobs: 
 
  # JOB 1 - BUILD   build:     name: Build     runs-on: ubuntu-latest 
     steps: 
-	name: Checkout code         uses: actions/checkout@v5 
 
-	name: Setup JDK         uses: actions/setup-java@v5         with:           distribution: temurin           java-version: '17'           cache: maven 
 
-	name: Validate with Maven         run: mvn validate 
 
-	name: Compile with Maven         run: mvn compile 
 
-	name: Test with Maven         run: mvn test 
 
-	name: Package with Maven run: mvn clean package -DskipTests 
 
-	name: Prepare WAR         run: | 
          mkdir -p deploy 
          WAR_FILE=$(find target -maxdepth 1 -name "*.war" -print -quit)           if [ -z "$WAR_FILE" ]; then             echo "ERROR: WAR file not found"             exit 1           fi 
          cp "$WAR_FILE" deploy/train-booking.war           ls -lh deploy/ 
 
-	name: Upload WAR Artifact         uses: actions/upload-artifact@v4         with:           name: train-booking-war           path: deploy/train-booking.war           if-no-files-found: error 
 
  # JOB 2 - DEV   deploy-dev:     name: Deploy DEV     needs: build     runs-on: ubuntu-latest     environment: DEV 
     steps: 
-	name: Download WAR 
        uses: actions/download-artifact@v4         with:           name: train-booking-war           path: deploy 
 
-	name: Deploy to DEV Tomcat         env: 
          TOMCAT_HOST: ${{ secrets.TOMCAT_HOST }} 
          TOMCAT_USER: ${{ secrets.TOMCAT_USER }}           TOMCAT_PASSWORD: ${{ secrets.TOMCAT_PASSWORD }}         run: |           set -e 
          test -n "$TOMCAT_HOST" || exit 1           test -n "$TOMCAT_USER" || exit 1           test -n "$TOMCAT_PASSWORD" || exit 1 
 
          curl --fail --show-error \ 
-u	"$TOMCAT_USER:$TOMCAT_PASSWORD" \ 
            -T deploy/train-booking.war \ 
            
"${TOMCAT_HOST%/}/manager/text/deploy?path=/TrainBook&update=true"  
  # JOB 3 - TEST   deploy-test:     name: Deploy TEST     needs: build     runs-on: ubuntu-latest     environment: TEST 
     steps: 
-	name: Download WAR 
        uses: actions/download-artifact@v4 with: 
          name: train-booking-war           path: deploy 
 
-	name: Deploy to TEST Tomcat         env: 
          TOMCAT_HOST: ${{ secrets.TOMCAT_HOST }} 
          TOMCAT_USER: ${{ secrets.TOMCAT_USER }}           TOMCAT_PASSWORD: ${{ secrets.TOMCAT_PASSWORD }}         run: |           set -e 
          test -n "$TOMCAT_HOST" || exit 1           test -n "$TOMCAT_USER" || exit 1           test -n "$TOMCAT_PASSWORD" || exit 1 
 
          curl --fail --show-error \ 
-u	"$TOMCAT_USER:$TOMCAT_PASSWORD" \ 
            -T deploy/train-booking.war \ 
            
"${TOMCAT_HOST%/}/manager/text/deploy?path=/TrainBook&update=true" 
 
  # JOB 4 - PRE-PROD   deploy-preprod:     name: Deploy PrePROD     needs: build     runs-on: ubuntu-latest     environment: PRE-PROD 
     steps: 
-	name: Download WAR 
        uses: actions/download-artifact@v4         with:           name: train-booking-war           path: deploy 
 
-	name: Deploy to PRE-PROD Tomcat         env: 
          TOMCAT_HOST: ${{ secrets.TOMCAT_HOST }} 
          TOMCAT_USER: ${{ secrets.TOMCAT_USER }}           TOMCAT_PASSWORD: ${{ secrets.TOMCAT_PASSWORD }}         run: |           set -e 
          test -n "$TOMCAT_HOST" || exit 1           test -n "$TOMCAT_USER" || exit 1           test -n "$TOMCAT_PASSWORD" || exit 1 
 
          curl --fail --show-error \ 
-u	"$TOMCAT_USER:$TOMCAT_PASSWORD" \ 
            -T deploy/train-booking.war \ 
            
"${TOMCAT_HOST%/}/manager/text/deploy?path=/TrainBook&update=true" 
 
  # JOB 5 - PROD   deploy-prod:     name: Deploy PROD     needs: build     runs-on: ubuntu-latest     environment: PROD 
     steps: 
-	name: Download WAR uses: actions/download-artifact@v4 
        with:           name: train-booking-war           path: deploy 
 
-	name: Deploy to PROD Tomcat         env: 
          TOMCAT_HOST: ${{ secrets.TOMCAT_HOST }} 
          TOMCAT_USER: ${{ secrets.TOMCAT_USER }}           TOMCAT_PASSWORD: ${{ secrets.TOMCAT_PASSWORD }}         run: |           set -e 
          test -n "$TOMCAT_HOST" || exit 1           test -n "$TOMCAT_USER" || exit 1           test -n "$TOMCAT_PASSWORD" || exit 1 
 
          curl --fail --show-error \ 
-u	"$TOMCAT_USER:$TOMCAT_PASSWORD" \ 
            -T deploy/train-booking.war \ 
            
"${TOMCAT_HOST%/}/manager/text/deploy?path=/TrainBook&update=true" 
8. Save the YAML file. 
Screenshot 11: GitHub Actions workflow YAML file in VS Code. 
10.	COMMITTING AND PUSHING CHANGES TO GITHUB 
1.	Open Git Bash in the project directory. 
2.	Check the modified files. git status 
3.	Add the changes. git add . 
4.	Commit the changes. git commit -m "Add CI-CD pipeline" 
5.	Push the changes to the main branch. git push origin main 
6.	Open the GitHub repository. 
7.	Click the Actions tab. 
8.	Select the newly triggered CI/CD workflow. 
9.	Verify that the Build job starts. 
10.	After successful Build execution, verify that all four deployment jobs start. 
Screenshot 13: All five jobs completed successfully with green status marks. 
  
11.	APPLICATION DEPLOYMENT VERIFICATION 
11.1	DEV Environment Verification 
1.	Open a web browser. 
2.	Enter the DEV server public IP address. http://DEV-PUBLIC-IP:8080/ 
3.	Verify that the Apache Tomcat welcome page is displayed. 
4.	Click the Manager App option. 
5.	Enter the configured Tomcat username and password. 
6.	Verify that the Train Booking application is listed in the Manager interface. 
7.	Click the Train Booking application path. 
8.	Verify that the application page opens successfully. 
Application URL: http://DEV-PUBLIC-IP:8080/TrainBook/ 
Screenshot 14: Successfully deployed DEV application. 
  
11.2	TEST Environment Verification 
1.	Open a web browser. 
2.	Enter the TEST server public IP address. http://TEST-PUBLIC-IP:8080/ 
3.	Open the Tomcat Manager application. 
4.	Enter the configured username and password. 
5.	Verify that the Train Booking application is deployed. 
6.	Open the application and verify its output. 
Application URL: http://TEST-PUBLIC-IP:8080/TrainBook/ 
Screenshot 15: Successfully deployed TEST application. 
  
11.3	PRE-PROD Environment Verification 
1.	Open a web browser. 
2.	Enter the PRE-PROD server public IP address. http://PRE-PROD-PUBLIC-IP:8080/ 
3.	Open the Tomcat Manager application. 
4.	Enter the configured username and password. 
5.	Verify that the Train Booking application is deployed. 
6.	Open the application and verify its output. 
Application URL: http://PRE-PROD-PUBLIC-IP:8080/TrainBook/ 
Screenshot 16: Successfully deployed PRE-PROD application. 
  
11.4	PROD Environment Verification 
1.	Open a web browser. 
2.	Enter the PROD server public IP address. http://PROD-PUBLIC-IP:8080/ 
3.	Open the Tomcat Manager application. 
4.	Enter the configured username and password. 
5.	Verify that the Train Booking application is deployed. 
6.	Open the application and verify its output. 
Application URL: http://PROD-PUBLIC-IP:8080/TrainBook/ 
Screenshot 17: Successfully deployed PROD application. 
  
Important notes before using this matter: 
•	This is the original project content, not a verification that every step has been completed. 
•	The document uses Java 17 and main branch. Your actual setup has used Java 11 and the master branch, so those details may need updating. 
•	The workflow uses Tomcat Manager credentials and /TrainBook as the deployment path. Your actual WAR name and application context path must match. 
•	Keep Tomcat Manager access restricted to trusted sources rather than exposing it publicly. 
 
