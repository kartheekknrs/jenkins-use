
## how to use username , password variable type-1 which are stored in credentials in jenkins 
```
 pipeline {
    agent {
        docker {
            image 'docker:24.0-cli'    // docker CLI image
            args '-v /var/run/docker.sock:/var/run/docker.sock'  // bind host daemon
// it may does not work we need to add jenkins user to the docker group which helps docker to communicate with docker demonsets
// we use (sudo usermod -aG docker jenkins)   to give that access even in using agent any we use this.
        }
    }
    environment {
        DOCKER_CREDS = credentials('dockerhub-creds')
    }
    stages {
        stage('Build & Push') {
            steps {
                sh '''
                docker login -u $DOCKER_CREDS_USR -p $DOCKER_CREDS_PSW
                docker build -t my-app:latest .
                docker tag my-app:latest mydockerhubuser/my-app:latest
                docker push mydockerhubuser/my-app:latest
                '''
            }
        }
    }
} 
```
## Using Username & Password Credentials type-2 (withcredentials types mentioned bellow)
--------------------------------------
```
pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'git-creds', 
                                                 usernameVariable: 'GIT_USER', 
                                                 passwordVariable: 'GIT_PASS')]) {
                    sh '''
                        echo "Cloning repository..."
                        git clone https://$GIT_USER:$GIT_PASS@github.com/username/repo.git
                    '''
                }
            }
        }
    }
}
```


TYPES OF WITHCREDENTIALS
------------------------------
| Type                | Wrapper in Pipeline      | Example Use Case                        |
| ------------------- | ------------------------ | --------------------------------------- |
| Secret text         | `string(...)`            | API tokens, SonarQube token             |
| Username + password | `usernamePassword(...)`  | Git, DockerHub, basic auth              |
| SSH private key     | `sshUserPrivateKey(...)` | SSH deploy, Git over SSH                |
| Certificate         | `certificate(...)`       | HTTPS client auth, signing certificates |



### 1️⃣ Secret text

Stores: Single string like API token, password, or key

Pipeline example:
```
pipeline {
    agent any
    stages {
        stage('Use Secret Text') {
            steps {
                withCredentials([string(credentialsId: 'sonarqube-token', variable: 'TOKEN')]) {
                    sh 'echo "Using token: $TOKEN"'
                    // Example: SonarQube analysis
                    sh 'mvn sonar:sonar -Dsonar.login=$TOKEN'
                }
            }
        }
    }
}
```

### 2️⃣ Username with password

Stores: Username + password pair (e.g., Git, DockerHub)

Pipeline example:
```
pipeline {
    agent any
    stages {
        stage('Git Clone') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'git-creds', 
                                                 usernameVariable: 'GIT_USER', 
                                                 passwordVariable: 'GIT_PASS')]) {
                    sh 'git clone https://$GIT_USER:$GIT_PASS@github.com/kartheek/project.git'
                }
            }
        }
    }
}
```
### 3️⃣ SSH private key

Stores: SSH key (optionally with passphrase) for accessing servers or Git

Pipeline example:
```
pipeline {
    agent any
    stages {
        stage('SSH Deployment') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key-id', 
                                                   keyFileVariable: 'SSH_KEY',
                                                   usernameVariable: 'SSH_USER')]) {
                    sh '''
                    echo "Deploying via SSH..."
                    ssh -i $SSH_KEY $SSH_USER@server 'ls -la'
                    '''
                }
            }
        }
    }
}
```
keyFileVariable → temporary file path to private key

usernameVariable → SSH username

### 4️⃣ Certificate

Stores: Certificate + password (for HTTPS or secure APIs)

Pipeline example:
```
pipeline {
    agent any
    stages {
        stage('Use Certificate') {
            steps {
                withCredentials([certificate(credentialsId: 'my-cert', 
                                             keystoreVariable: 'CERT_FILE', 
                                             passwordVariable: 'CERT_PASS')]) {
                    sh '''
                    echo "Using certificate at $CERT_FILE with password $CERT_PASS"
                    keytool -list -keystore $CERT_FILE -storepass $CERT_PASS
                    '''
                }
            }
        }
    }
}
```

keystoreVariable → path to temporary certificate file

passwordVariable → certificate password




# how to create env variables in pipeline
----------------------------------
```
pipeline {
    agent any
    environment {
        APP_ENV = "production"          // simple string
        API_URL = "https://api.example.com"
    }
    stages {
        stage('Show Environment') {
            steps {
                echo "App environment is: ${env.APP_ENV}"
                echo "API URL is: ${env.API_URL}"
            }
        }
    }
}
```

# how to use jenkins credentials as variable (direct way like single variable in credentials like token not like username , password)
-------------------------------------------
```
pipeline {
    agent any
    environment {
        MY_TOKEN = credentials('my-secret-token')
    }
    stages {
        stage('Use Token') {
            steps {
                sh 'echo "Using token: $MY_TOKEN"'  // hidden in Jenkins logs
            }
        }
    }
} 
```


