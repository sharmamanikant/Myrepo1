pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/ReyazShaik/java-project-maven-new.git'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('SonarQube Scanning') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar' 
                }                
            }
        }
        stage('Artifacts') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcatcreds', path: '', url: 'http://43.205.95.19:8080/')], contextPath: 'myapp', war: '**/*.war'
            }
        }
        stage('Nexus') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'myapp', classifier: '', file: 'target/myapp.war', type: '.war']], credentialsId: 'nexus', groupId: 'in.reyaz', nexusUrl: '3.6.41.43:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'hotstar', version: '8.3.3-SNAPSHOT'
            }
        }
        stage('Uploading to S3') {
            steps {
                s3Upload consoleLogLevel: 'INFO', dontSetBuildResultOnFailure: false, dontWaitForConcurrentBuildCompletion: false, entries: [[bucket: 'jen-project-arti/', excludedFile: '', flatten: false, gzipFiles: false, keepForever: false, managedArtifacts: false, noUploadOnFailure: false, selectedRegion: 'ap-south-1', showDirectlyInBrowser: false, sourceFile: '**/*.war', storageClass: 'STANDARD', uploadFromSlave: false, useServerSideEncryption: true]], pluginFailureResultConstraint: 'FAILURE', profileName: 's3creds', userMetadata: []
            }
        }
    }
}
