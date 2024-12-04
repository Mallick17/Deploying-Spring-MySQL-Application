# Deploying-Spring-MySQL-Application
## 1. Launch Two Servers or Instances
- **Application Server**
- **Database Server**
## 2. Connect to Servers Using MobaXterm
### Application Server Setup
- **Install Java 17**
  ```bash
  yum install java-17* -y
  ```
- **Install Git**
  ```bash
  yum install git -y
  ```
- **Install and start Jenkins:**
  - Jenkins will run on port **8080**.
### Database Server Setup
- **Install MySQL**
  ```bash
  yum install mysql -y
  ```
## 3. Create an RDS Instance in AWS
### RDS Configuration:
- Database Name: `database-1`
- Master Username: `admin`
- Master Password: `password`
- Connect EC2 Database Server to the RDS
### Set Up Connectivity
- Navigate to **RDS > Database > Connectivity & Security > Security.**
- Click on **VPC Security Groups** linked to the **RDS instance**.
- *Edit Inbound Rules*:
  - MYSQL/Aurora: Custom
  - All Traffic: Anywhere IPv4
  - Save Rules.
- *Edit Outbound Rules*:
  - MYSQL/Aurora: Anywhere IPv4
  - All Traffic: Anywhere IPv4
  - Save Rules.
## 4. Configure Jenkins
### Install Maven:
- Go to Manage Jenkins > Maven Installations > Add Maven.
- Name: Maven
- Click **Apply & Save**.
### Install Required Jenkins Plugins:
- Go to Manage Jenkins > Plugins > Available Plugins.
- Search and install:
  - `Pipeline Stage View`
## 5. Update the Spring-MySQL Application
- Navigate to RDS > Database > Connectivity & Security.
  - **Copy the RDS Endpoint**.
- Go to the Git Repository (Spring-MySQL Project):
  - Path: `src/main/resources/application.properties`
  -  Update the following lines:
    ```properties
    spring.datasource.url=jdbc:mysql://<<paste RDS endpoint>>:3306
    spring.datasource.username=<<RDS Username>>
    ```
  - Commit the changes.
- Update GitHub Webhooks:
  - Navigate to **Settings > Webhooks in the Spring MySQL project**.
  - **Update or create a new webhook**.
## 6. Set Up Jenkins Pipeline
- 1. **Create a New Item**:
     - Name the project and select Pipeline Project.
- 2. **Configure Build Triggers**:
     - Check GitHub hook trigger for GITScm polling.
- 3. **Pipeline Script**:
     - Add the pipeline script:
```groovy
pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {
        stage('Pull Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/ShivaHK18/Spring_mySql_project.git'
            }
        }
        stage('Prepare Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Deploy') {
            steps {
                sh 'java -jar target/my-shop-1.0.jar'
            }
        }
    }
}
```
- 4. **Save and Build:**
     -  Click **Apply & Save**.
     -  Click **Build Now**.      

## 7. Test the Application
- The last build will be running.
  - **Copy the App URL:1234 from Jenkins and test the site in a browser**.
- Verify the database setup using MySQL commands.



















