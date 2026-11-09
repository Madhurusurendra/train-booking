 
# Train-Ticket-Booking Application — Multi-Environment CI/CD Deployment

## 1. Project Overview

The **Train-Ticket-Booking Application** is a Java web application packaged as a WAR file and deployed on Apache Tomcat 9.

This project demonstrates how to automate the build and deployment process using **Git, GitHub, Maven, GitHub Actions, Apache Tomcat, and AWS EC2**.

The application is deployed independently across four environments:

- DEV – Development and initial validation
- TEST – Functional and integration testing
- PRE-PROD – Final validation before production
- PROD – Live production environment

The objective is to implement a reliable CI/CD pipeline that reduces manual deployment work and supports controlled application releases.

## 2. Project Objectives

- Build the Java application using Maven.
- Automate CI/CD using GitHub Actions.
- Deploy the application independently to DEV, TEST, PRE-PROD, and PROD.
- Use Git branches to manage environment deployments.
- Package the application as a WAR file.
- Deploy the WAR file to Apache Tomcat 9.
- Store environment-specific credentials securely.
- Reduce manual deployment activities.
- Separate testing and production environments.
- Implement controlled production releases.

## 3. Technologies Used

| Technology | Purpose |
|---|---|
| Java 17 | Application development |
| Maven | Build and dependency management |
| Apache Tomcat 9 | Application server |
| Git | Version control |
| GitHub | Source code repository |
| GitHub Actions | CI/CD automation |
| AWS EC2 | Application hosting |
| Ubuntu Linux | Server operating system, if used |
| Shell scripting | Deployment automation |
| GitHub Environments | Environment-specific secrets and approvals |

## 4. Project Architecture

### 4.1 CI/CD Deployment Flow

```text
        Developer
            |
            v
    GitHub Repository
            |
            v
    Environment Branch
            |
            v
   GitHub Actions Workflow
            |
            v
      Checkout Code
            |
            v
       Setup Java 17
            |
            v
      Maven Build
      mvn clean verify
            |
            v
       WAR File Created
            |
            v
    Environment Deployment
            |
            v
     AWS EC2 / Tomcat 9
            |
            v
 Train-Ticket-Booking Application
            |
            v
          Browser
```

### 4.2 Multi-Environment Architecture

```text
                 GitHub Repository
                         |
                         v
                 GitHub Actions
                         |
        +----------------+----------------+
        |                |                |
        v                v                v
       DEV              TEST           PRE-PROD
        |                |                |
        v                v                v
     Tomcat 9          Tomcat 9         Tomcat 9
        |                |                |
        v                v                v
    Application      Application      Application

                         |
                         v
                       PROD
                         |
                         v
                      Tomcat 9
                         |
                         v
                     Application
```

The environments should be isolated from one another. They may use separate EC2 instances, separate Tomcat services, or another suitable infrastructure-isolation model.

## 5. Environment Details

| Environment | Purpose | Example Branch |
|---|---|---|
| DEV | Development and initial testing | Dev |
| TEST | Functional and integration testing | Test |
| PRE-PROD | Final testing before release | Pre-Prod |
| PROD | Live application | main |

**Note:** These are example branch names. Use the exact branch names configured in your GitHub repository. Git branch names are case-sensitive.

### Environment Promotion Flow

```text
DEV
 |
 v
TEST
 |
 v
PRE-PROD
 |
 v
PROD
```

Promote the application to the next environment only after successful validation in the current environment.

## 6. Project Folder Structure

```text
Train-Ticket-Booking-application/
|
+-- .github/
|   |
|   +-- workflows/
|       |
|       +-- dev.yml
|       +-- test.yml
|       +-- pre-prod.yml
|       +-- prod.yml
|
+-- src/
|   |
|   +-- main/
|       |
|       +-- java/
|       +-- resources/
|       +-- webapp/
|           |
|           +-- WEB-INF/
|           |   |
|           |   +-- web.xml
|           |
|           +-- index.jsp
|
+-- pom.xml
|
+-- README.md
```

The folder structure may vary depending on the application implementation. Maven generates the `target/` directory during the build.

## 7. CI/CD Pipeline Process

### Stage 1: Source Code Management

- Developers maintain the Train-Ticket-Booking application source code in GitHub.
- Git is used for version control.
- Separate branches are maintained for different environments.

### Stage 2: Pipeline Trigger

When a developer pushes code to a configured branch, the corresponding GitHub Actions workflow starts.

A manual trigger using `workflow_dispatch` can also be configured.

### Stage 3: Checkout Source Code

GitHub Actions downloads the repository source code.

```yaml
- name: Checkout Source Code
  uses: actions/checkout@v4
```

### Stage 4: Setup Java 17

Configure Java 17 for the Maven build.

```yaml
- name: Set up Java 17
  uses: actions/setup-java@v4
  with:
    distribution: temurin
    java-version: '17'
    cache: maven
```

### Stage 5: Maven Build

Execute the Maven command:

```bash
mvn -B clean verify
```

This command cleans previous build output and runs the Maven lifecycle through the `verify` phase.

If the project is configured with WAR packaging, Maven creates the WAR artifact.

### Stage 6: Verify WAR File

Check the generated WAR file:

```bash
ls -lh target/
```

Check the expected artifact:

```bash
test -f target/Train-Ticket-Booking-application.war
```

Expected artifact:

```text
target/Train-Ticket-Booking-application.war
```

### Stage 7: Application Deployment

Deploy the generated WAR file to the selected environment's Apache Tomcat 9 server.

Deployment can be performed using:

- Tomcat Manager Text API, or
- SSH/SCP-based deployment.

Use the deployment method configured in the corresponding workflow.

### Stage 8: Deployment Verification

After deployment, verify the application using the environment URL.

```text
http://<ENVIRONMENT_TOMCAT_HOST>:8080/Train-Ticket-Booking-application/
```

Replace the hostname with the correct server address for each environment.

## 8. Maven Commands

| Command | Purpose |
|---|---|
| `mvn clean` | Removes previous build output |
| `mvn compile` | Compiles Java source code |
| `mvn test` | Executes unit tests |
| `mvn package` | Packages the application |
| `mvn verify` | Runs verification checks |
| `mvn -B clean verify` | Cleans and builds through the verify phase |

## 9. Maven Configuration

In `pom.xml`, configure WAR packaging:

```xml
<packaging>war</packaging>

<build>
    <finalName>Train-Ticket-Booking-application</finalName>
</build>
```

Maven generates:

```text
target/Train-Ticket-Booking-application.war
```

## 10. Environment-Specific GitHub Actions Workflows

Create four workflow files inside:

```text
.github/workflows/
```

| Workflow file | Branch example | GitHub Environment |
|---|---|---|
| `dev.yml` | Dev | DEV |
| `test.yml` | Test | TEST |
| `pre-prod.yml` | Pre-Prod | PRE-PROD |
| `prod.yml` | main | PROD |

Each workflow should perform the following activities:

1. Checkout source code.
2. Set up Java 17.
3. Run Maven build.
4. Verify the WAR file.
5. Deploy to the selected Tomcat server.
6. Perform deployment verification.
7. Report the workflow result.

Ensure the `on.push.branches` value in each YAML file matches the actual branch name.

## 11. GitHub Environments and Secrets

Create the following environments in GitHub:

1. Open your GitHub repository.
2. Go to **Settings**.
3. Select **Environments**.
4. Create DEV, TEST, PRE-PROD, and PROD.
5. Add the appropriate environment-specific secrets.

### Example secrets for Tomcat Manager deployment

```text
TOMCAT_HOST
TOMCAT_USER
TOMCAT_PASSWORD
```

### Example secrets for SSH/SCP deployment

```text
EC2_HOST
EC2_USER
EC2_SSH_KEY
```

Use only the secrets required by your deployment method.

For PROD, configure required reviewers or other available protection rules to control releases.

**Security:** Never hardcode passwords, private keys, or access tokens in workflow files or commit them to GitHub.

## 12. Apache Tomcat 9 Configuration

Apache Tomcat 9 is used to host the Train-Ticket-Booking application.

Expected application context path:

```text
/Train-Ticket-Booking-application
```

For Tomcat Manager Text API deployment, a deployment user needs the `manager-script` role.

Example Tomcat user configuration:

```xml
<role rolename="manager-script"/>

<user username="deploy"
      password="CHANGE_THIS_PASSWORD"
      roles="manager-script"/>
```

This is an example only. Use a strong secret password and restrict Manager access to trusted networks. Do not expose the Tomcat Manager application publicly.

**Important:** Tomcat context paths are case-sensitive. The URL must match the deployed WAR filename or the context configured for the application.

## 13. AWS EC2 Deployment

AWS EC2 provides the servers used to host the application environments.

General setup process:

1. Provision or identify the EC2 instances for the environments.
2. Install the required Java runtime.
3. Install and configure Apache Tomcat 9.
4. Ensure the Tomcat service is running.
5. Configure secure deployment credentials.
6. Configure network access and security groups.
7. Deploy the WAR file.
8. Verify the application URL.
9. Check Tomcat logs if deployment fails.

### Useful Server Commands

Check whether Tomcat is listening on port 8080:

```bash
sudo ss -lntp | grep 8080
```

Check the Tomcat process:

```bash
ps -ef | grep '[t]omcat'
```

Check the application:

```bash
curl -I http://localhost:8080/Train-Ticket-Booking-application/
```

Check Tomcat logs:

```bash
tail -n 100 /root/tomcat/logs/catalina.out
```

The log path may differ depending on your Tomcat installation.

## 14. Application URLs

| Environment | Example URL |
|---|---|
| DEV | `http://<DEV_HOST>:8080/Train-Ticket-Booking-application/` |
| TEST | `http://<TEST_HOST>:8080/Train-Ticket-Booking-application/` |
| PRE-PROD | `http://<PRE-PROD_HOST>:8080/Train-Ticket-Booking-application/` |
| PROD | `http://<PROD_HOST>:8080/Train-Ticket-Booking-application/` |

Replace the placeholders with the actual server addresses.

For production, configure HTTPS through an appropriate reverse proxy, load balancer, or other secure entry point.

## 15. Git Commands Used

Clone the repository:

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
```

Move into the project directory:

```bash
cd <YOUR_REPOSITORY_DIRECTORY>
```

List local branches:

```bash
git branch
```

List remote branches:

```bash
git branch -r
```

List all branches:

```bash
git branch -a
```

Switch branches:

```bash
git switch Dev
git switch Test
git switch Pre-Prod
git switch main
```

Add and commit changes:

```bash
git add .
git commit -m "Update Train-Ticket-Booking Application"
```

Push to the intended branch:

```bash
git push origin Dev
```

View commit history:

```bash
git log --oneline
```

Use the correct branch name when pushing changes.

## 16. Troubleshooting

### Problem 1: Application returns HTTP 404

Possible reasons:

- WAR file was not deployed.
- Incorrect application context path.
- WAR filename does not match the URL.
- Application failed during startup.

Check the deployed application:

```bash
ls -lah <TOMCAT_HOME>/webapps/
```

Check Tomcat logs:

```bash
tail -n 100 <TOMCAT_HOME>/logs/catalina.out
```

Verify the application URL and context path.

### Problem 2: Tomcat Manager returns HTTP 403

Possible reasons:

- Deployment user does not have the `manager-script` role.
- Tomcat Manager access restrictions block the GitHub Actions runner.
- Incorrect deployment permissions.

Check the Tomcat user roles and Manager access configuration. Do not remove network restrictions just to make deployment work.

### Problem 3: Maven build fails

Run:

```bash
mvn -B clean verify
```

Check the first meaningful error in the Maven output, Java version, dependencies, and `pom.xml` configuration.

### Problem 4: WAR file is missing

Verify that the project uses WAR packaging and the expected `finalName`.

Run:

```bash
mvn clean verify
ls -lh target/
```

### Problem 5: GitHub Actions workflow does not trigger

Check:

- Workflow file is inside `.github/workflows/`.
- Branch name matches the configured trigger exactly.
- GitHub Actions is enabled for the repository.
- Code was pushed to the correct repository and branch.
- YAML indentation and syntax are valid.

### Problem 6: Application does not start

Check Tomcat logs, application configuration, XML files, and compatibility with Tomcat 9.

## 17. Security Best Practices

- Store credentials in GitHub Secrets.
- Never upload private keys or passwords to GitHub.
- Follow least-privilege access for AWS and EC2.
- Restrict SSH access to trusted sources.
- Restrict Tomcat Manager access.
- Avoid exposing Tomcat port 8080 publicly in production.
- Use HTTPS for production access.
- Require approval before production deployment.
- Maintain deployment logs and rollback procedures.
- Avoid unnecessary broad `sudo` permissions in deployment scripts.

## 18. Future Enhancements

- Add automated unit and integration tests.
- Integrate SonarQube for code quality.
- Add dependency and security scanning.
- Integrate Nexus or JFrog for artifact management.
- Implement automated health checks.
- Add versioned artifacts and rollback automation.
- Configure centralized monitoring and logging.
- Configure HTTPS using a reverse proxy or load balancer.
- Containerize the application using Docker.
- Explore Kubernetes deployment.

## 19. Project Summary

This project demonstrates an automated CI/CD pipeline for a Java-based Train-Ticket-Booking application.

Using Git, GitHub, Maven, GitHub Actions, Apache Tomcat 9, and AWS EC2, the application can be built and deployed independently across DEV, TEST, PRE-PROD, and PROD environments.

The pipeline reduces manual deployment work, improves release consistency, and provides a controlled process for promoting application changes to production.
## 20. Author

**Name:** Sreenivas Sonthi  
**GitHub:** [sreenu-sonthi](https://github.com/sreenu-sonthi)

**Project:** Train-Ticket-Booking Application — Multi-Environment CI/CD Deployment

## 📸 Project Screenshots

### 1. EC2 instance
<img width="1915" height="431" alt="Screenshot 2026-11-04 144308" src="https://github.com/user-attachments/assets/197c3837-864f-466a-a020-71c06468e175" />

### 2. Screenshot of AMI used for different environment

<img width="1617" height="377" alt="Screenshot 2026-11-04 154851" src="https://github.com/user-attachments/assets/8d1abe67-59ee-4885-a8fd-143190485a2c" />

### 3. EC2 instance for different environments:

<img width="1622" height="372" alt="Screenshot 2026-11-04 154943" src="https://github.com/user-attachments/assets/7ebd86b1-838f-4da3-9b0a-3660608183da" />

### 4. DEV-Environment deployment output:

<img width="1915" height="870" alt="Screenshot 2026-11-04 155056" src="https://github.com/user-attachments/assets/d7715fe9-dcfb-415f-861f-98f97d25ce4d" />
<img width="1908" height="961" alt="Screenshot 2026-11-04 144152" src="https://github.com/user-attachments/assets/a3342207-2e7f-4b79-aee8-fbefb5d6ab6f" />


### 5. TEST-Environment deployment output:
<img width="1917" height="876" alt="Screenshot 2026-11-04 155037" src="https://github.com/user-attachments/assets/f3fe6bb9-e179-46d9-88f4-b1acb63d9718" />
<img width="1916" height="970" alt="Screenshot 2026-11-04 153359" src="https://github.com/user-attachments/assets/89eae5ef-c7d1-4e8b-9034-f00acede90b0" />


### 6. PRE-PROD-Environment deployment output:
<img width="1916" height="871" alt="Screenshot 2026-11-04 155021 (1)" src="https://github.com/user-attachments/assets/ec2386c8-9606-4100-8a3c-1fb3c104bdb5" />

<img width="1912" height="968" alt="Screenshot 2026-11-04 154100 (1)" src="https://github.com/user-attachments/assets/58bebd16-f32e-4bbb-b5f6-4e693b02ec86" />


### 7. PROD-Environment deployment output:
<img width="1917" height="868" alt="Screenshot 2026-11-04 155115" src="https://github.com/user-attachments/assets/75a7d7a5-a197-4f01-adad-294f73849d06" />

<img width="1917" height="962" alt="Screenshot 2026-11-04 154635" src="https://github.com/user-attachments/assets/328defb2-215d-4e10-87ff-ed6ed7b28e51" />

