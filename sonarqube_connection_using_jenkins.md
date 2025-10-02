# configer jenkins with sonarcube

Installing the Jenkins extension
Jenkins extension version 2.11 or later is required.

## Proceed as follows:

1. From the Jenkins Dashboard, navigate to Manage Jenkins > Manage Plugins and install the SonarQube Scanner plugin.

2. Back at the Jenkins Dashboard, navigate to Credentials > System from the left navigation.

3. Click the Global credentials (unrestricted) link in the System table.

4. Click Add credentials in the left navigation and add the following information:

4.1 Kind: Secret Text

4.1 Scope: Global

4.2 Secret: Generate a token at User > My Account > Security in SonarQube Server, and copy and paste it here.

5. Click OK.

6. From the Jenkins Dashboard, navigate to Manage Jenkins > Configure System.

7. From the SonarQube Servers section, click Add SonarQube. Add the following information:

7.1 Name: Give a unique name to your SonarQube Server instance.

7.2 Server URL: Your SonarQube Server instance URL.

7.3 Credentials: Select the credentials created during step 4.

8. Click Save
