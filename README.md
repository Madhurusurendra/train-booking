# Project 4 – CI/CD Pipeline for Java Application Deployment Using GitHub Actions

## 1. Project Overview

This project demonstrates a Continuous Integration and Continuous Deployment (CI/CD) pipeline for a Java web application. GitHub Actions is used to build the application with Maven and deploy the generated WAR package to Apache Tomcat servers hosted on AWS EC2.

The project uses four environments:

- **DEV**
- **TEST**
- **PRE-PROD**
- **PROD**

## 2. Technologies Used

| Technology | Purpose |
|---|---|
| AWS EC2 | Hosting application servers |
| AWS AMI | Creating identical server configurations |
| Ubuntu Linux | Operating system |
| Java 17 | Java development and runtime |
| Maven | Building Java applications |
| Apache Tomcat 9 | Hosting Java web applications |
| GitHub | Source code management |
| GitHub Actions | CI/CD automation |
| WAR | Application deployment package |

## 3. AWS EC2 Instance Creation

1. Open the AWS Management Console and navigate to EC2.
2. Select **Launch Instance**.
3. Name the first instance `dev-server`.
4. Select Ubuntu Server as the AMI and choose the required instance type.
5. Select the required VPC and subnet, and enable a public IP if required.
6. Configure the security group. The document specifies SSH (port 22) from **My IP** and custom TCP port **8080**.
7. Launch the instance and wait until its state is **Running**.
8. Connect to the instance using EC2 Instance Connect.

**Screenshot – DEV EC2 instance**
<img width="1919" height="505" alt="Screenshot 2026-11-08 213720" src="https://github.com/user-attachments/assets/75376fdd-fd8f-40a7-be7f-fc23dab2794f" />



## 4. Java and Maven Installation

Update Ubuntu packages and install Java 17 and Maven:

```bash
sudo -i
apt update -y
apt install openjdk-17-jdk -y
java -version
apt install maven -y
mvn -version
```

**Screenshot – Java and Maven installation**
<img width="1124" height="354" alt="Screenshot 2026-11-08 214343" src="https://github.com/user-attachments/assets/52e8e1c6-21d3-4d13-aa3a-b8a808dd3774" />



## 5. Apache Tomcat 9 Installation and Configuration

### 5.1 Download and extract Tomcat

1. Open the official Apache Tomcat 9 download page.
2. Under **Binary Distributions**, select the Core TAR.GZ package and copy its download link.
3. On the EC2 terminal, download it using the copied link.

```bash
wget <copied-tomcat-download-link>
ls
tar -xf apache-tomcat-9*.tar.gz
ls
mv apache-tomcat-9* tomcat
cd tomcat
```

### 5.2 Configure Host Manager and Manager

The document configures the Host Manager and Manager applications by editing their `META-INF/context.xml` files and commenting out the relevant Valve access-restriction section.

```bash
cd /path/to/tomcat/webapps/host-manager/META-INF
vi context.xml
```

Repeat for:

```bash
/path/to/tomcat/webapps/manager/META-INF/context.xml
```

Save the files after editing.

### 5.3 Configure Tomcat users

Edit `tomcat-users.xml` in Tomcat's `conf` directory and add the roles and a user. Replace the placeholders with your own credentials.

```xml
<role rolename="manager-gui"/>
<role rolename="manager-script"/>

<user username="YOUR_USERNAME"
      password="YOUR_PASSWORD"
      roles="manager-gui,manager-script"/>
```

### 5.4 Start Tomcat and verify

The source document starts Tomcat using its scripts:

```bash
cd /path/to/tomcat/bin
./shutdown.sh
./startup.sh
```

Open the following address in a browser:

```text
http://DEV-PUBLIC-IP:8080/
```

Verify that the Tomcat welcome page appears, then test the Manager application using the configured credentials.

**Security note:** Keep Tomcat Manager access restricted to trusted IP addresses. Do not expose the Manager application or credentials publicly.

**Screenshots – Tomcat installation and Manager**
<img width="1911" height="967" alt="Screenshot 2026-11-08 224210" src="https://github.com/user-attachments/assets/62b9d963-c008-4107-ae91-a2bbaa6570b5" />
<img width="1912" height="960" alt="Screenshot 2026-11-09 125807" src="https://github.com/user-attachments/assets/0960bc9f-5869-4f13-b7a8-48d93dcdc61f" />



## 6. Create an AMI from the DEV Server

1. Open EC2 and select the configured `dev-server`.
2. Choose **Actions → Image and Templates → Create Image**.
3. Enter an AMI name and optional description.
4. Review the image settings and create the image.
5. Open the AMIs section and verify that the image is available.

The AMI is intended to contain the configured server environment, including Java, Maven and Apache Tomcat.

**Screenshot – AMI creation**

<img width="1900" height="510" alt="Screenshot 2026-11-09 134733" src="https://github.com/user-attachments/assets/aacee428-c978-4e9a-8dbb-a544bdee59e1" />


## 7. Launch TEST, PRE-PROD and PROD Instances

1. In EC2, open the AMIs section and select the AMI created from DEV.
2. Choose **Launch Instance from AMI**.
3. Create `test-server`.
4. Repeat the process for `pre-prod-server` and `prod-server`.
5. Wait until all instances reach the **Running** state.
6. Connect to each server and verify Java, Maven and Tomcat.

| Instance name | Environment |
|---|---|
| `dev-server` | DEV |
| `test-server` | TEST |
| `pre-prod-server` | PRE-PROD |
| `prod-server` | PROD |

**Screenshots – Additional EC2 instances and verification**

<img width="1919" height="555" alt="Screenshot 2026-11-09 134813" src="https://github.com/user-attachments/assets/6914bf67-2390-4a70-b0e2-16d45a2815cd" />


## 8. Configure GitHub Secrets

The PDF describes the following secrets:

| Secret | Value |
|---|---|
| `TOMCAT_HOST` | Environment's Tomcat URL |
| `TOMCAT_USER` | Tomcat Manager username |
| `TOMCAT_PASSWORD` | Tomcat Manager password |

In the GitHub repository, open **Settings → Secrets and variables → Actions** and add the required secrets. Do not put passwords directly in workflow files or commit them to the repository.

## 9. Create GitHub Environments

Create four environments under **Settings → Environments**:

- `DEV`
- `TEST`
- `PRE-PROD`
- `PROD`

   <img width="1631" height="535" alt="Screenshot 2026-11-09 124555" src="https://github.com/user-attachments/assets/91ffe91c-3555-4411-bb26-871e02b83e5a" />


Add the appropriate environment-specific secrets to each environment. The `TOMCAT_HOST` value should point to the relevant EC2 server, for example:

```text
http://EC2-PUBLIC-IP:8080
```

**Screenshots – GitHub environments and secrets**

<img width="1918" height="868" alt="Screenshot 2026-11-09 132428" src="https://github.com/user-attachments/assets/885b1b9c-0884-4945-9fca-3fa36af072fa" />
<img width="1905" height="742" alt="Screenshot 2026-11-09 132508" src="https://github.com/user-attachments/assets/2bb6e47c-fbac-4735-a45a-f782cd54d6b4" />



## 10. Create the GitHub Actions Workflow

Create the workflow file in the repository:

```text
.github/
└── workflows/
    └── cicd.yaml
```

The workflow described in the PDF contains five jobs:

1. **Build** – checks out the source code, sets up Java 17, runs Maven validation, compilation and tests, packages the WAR, and uploads it as an artifact.
2. **Deploy DEV** – downloads the WAR and deploys it to the DEV Tomcat Manager.
3. **Deploy TEST** – downloads the WAR and deploys it to TEST.
4. **Deploy PRE-PROD** – downloads the WAR and deploys it to PRE-PROD.
5. **Deploy PROD** – downloads the WAR and deploys it to PROD.

The deployment steps use the `TOMCAT_HOST`, `TOMCAT_USER` and `TOMCAT_PASSWORD` secrets and the Tomcat Manager text deployment endpoint. The application context path used in the source workflow is `/TrainBook`.

**Important:** The YAML listing in the source PDF has formatting/indentation inconsistencies in places. Validate the actual `.yaml` file in GitHub Actions before using it. Also make sure the WAR file name and application context path match the actual Java project.

## 11. Commit and Push Changes to GitHub

From the project directory, run:

```bash
git status
git add .
git commit -m "Add CI-CD pipeline"
git push origin main
```

Open the repository's **Actions** tab, select the CI/CD workflow run, and inspect the Build and deployment jobs.

**Screenshot – GitHub Actions workflow**
<img width="1901" height="749" alt="Screenshot 2026-11-09 124627" src="https://github.com/user-attachments/assets/9c9c0ab5-23ab-4f51-903f-0c9f404fca90" />
<img width="1912" height="860" alt="Screenshot 2026-11-09 140238" src="https://github.com/user-attachments/assets/cde94015-75b3-42f1-98e8-502125204283" />



## 12. Application Deployment Verification

After deployment, open each environment's Tomcat URL and verify that the application is listed in Tomcat Manager and opens successfully.

| Environment | Application URL pattern |
|---|---|
| DEV | `http://DEV-PUBLIC-IP:8080/TrainBook/` |
| TEST | `http://TEST-PUBLIC-IP:8080/TrainBook/` |
| PRE-PROD | `http://PRE-PROD-PUBLIC-IP:8080/TrainBook/` |
| PROD | `http://PROD-PUBLIC-IP:8080/TrainBook/` |

Replace each placeholder with the corresponding server's public IP address.

**Screenshots – Application verification**
<img width="1915" height="970" alt="Screenshot 2026-11-09 134546" src="https://github.com/user-attachments/assets/404a493d-f0d5-45c7-80d7-cd4afe0ec2f7" />
<img width="1916" height="967" alt="Screenshot 2026-11-09 134532" src="https://github.com/user-attachments/assets/bd679f04-782d-4595-aa48-7ed196756339" />
<img width="1917" height="973" alt="Screenshot 2026-11-09 134514" src="https://github.com/user-attachments/assets/d57c3393-e8c9-4434-a3e4-9618902882bb" />
<img width="1917" height="961" alt="Screenshot 2026-11-09 134453" src="https://github.com/user-attachments/assets/a80dcebe-8a54-4f3d-a7a4-d8ca74cb69fb" />
