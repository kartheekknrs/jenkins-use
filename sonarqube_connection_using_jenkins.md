# configer jenkins with sonarcube

Installing the Jenkins extension
Jenkins extension version 2.11 or later is required.

## Proceed as follows:

1. From the Jenkins Dashboard, navigate to Manage Jenkins > Manage Plugins and install the SonarQube Scanner plugin.

2. Back at the Jenkins Dashboard, navigate to Credentials > System from the left navigation.

3. Click the Global credentials (unrestricted) link in the System table.

4. Click Add credentials in the left navigation and add the following information:

  * Kind: Secret Text

  * Scope: Global

  * Secret: Generate a token at User > My Account > Security in SonarQube Server, and copy and paste it here.

5. Click OK.

6. From the Jenkins Dashboard, navigate to Manage Jenkins > Configure System.

7. From the SonarQube Servers section, click Add SonarQube. Add the following information:

  * Name: Give a unique name to your SonarQube Server instance.

  * Server URL: Your SonarQube Server instance URL.

  * Credentials: Select the credentials created during step 4.

8. Click Save



## Installing the SonarScanner instance(s)

This step is mandatory if you want to trigger any of your analyses with the SonarScanner CLI. You can define as many scanner instances as you wish. Then, for each Jenkins job, you will be able to choose which launcher to use to run the analysis.

To install and configure the scanner instances:

1. Log into Jenkins as an administrator and go to Manage Jenkins > Global Tool Configuration.

2. Scroll down to the SonarScanner configuration section and select Add SonarScanner. It is based on the typical Jenkins tool auto-installation. You can either choose to point to an already installed version of the SonarScanner CLI (uncheck Install automatically) or tell Jenkins to grab the installer from a remote location (check Install automatically).
If you don’t see a drop-down list with all available SonarScanner CLI versions but instead see an empty text field, this is because Jenkins still hasn’t downloaded the required update center file (the default period is one day). You may force this refresh by selecting Check Now in Manage Plugins > Advanced tab.

# Adding an analysis stage to the Jenkins file

You must use the **withSonarQubeEnv** step in the SonarQube Server analysis stage of your pipeline job. This step is used to set the environment variables necessary to connect to the specified SonarQube Server instance. The connection details are retrieved from the Jenkins global configuration.

The **withSonarQubeEnv()** method can take the following optional parameters:

**installationName(string):** name of the SonarQube Server installation as configured in Jenkins.
This is necessary if several SonarQube SonarQube Server hosts are configured in Jenkins.

**credentialsId(string):** if you want to overwrite the credentials configured in the Jenkins global configuration.

**envOnly(boolean):** set it to true if you only want the SonarQube Server environment variables to be expanded in the build context

## OTHER Projects

```
pipeline {
  agent any
  stages {
    stage('SonarQube analysis') {
      steps {
        script {
            scannerHome = tool '<sonarqubeScannerInstallation>'// must match the name of an actual scanner installation directory on your Jenkins build agent
        }
        withSonarQubeEnv('<sonarqubeInstallation>') {// If you have configured more than one global server connection, you can specify its name as configured in Jenkins
          sh "${scannerHome}/bin/sonar-scanner"
        }
      }
    }
  }
} 
```

## FOR JAVA & MAVEN Projects

In this code he used direct variables like url and login cradentials . if we follow above steps we only use **mvn sonar:sonar**
```
    stage('Static Code Analysis') {
      environment {
        SONAR_URL = "http://34.201.116.83:9000"
      }
      steps {
        withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
          sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
        }
      }
    }
```

