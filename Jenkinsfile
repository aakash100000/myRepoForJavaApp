pipeline {
    
    agent any
    tools{
         maven 'Apache Maven 3.8.5'
    }
    
    stages {
        
        stage('Checkout'){
            
            steps{
                
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'b7f81384-3d3e-447d-80b9-d1226a295251', url: 'https://github.com/aakash100000/myRepoForJavaApp.git']]])
            }
            
            
        }
        
        stage('Build'){
            
            steps {
                
                bat 'mvn clean install -f MyWebApp/pom.xml'
            }
            
        }
        
        stage('Code Quality'){
            
            steps {
                
                withSonarQubeEnv('SonarQube Scanner') {
    
                          bat 'mvn sonar:sonar -f MyWebApp/pom.xml'
                    }
            }
            
        }
        
        stage('Nexus Upload'){
            
            steps {
                
                nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: '', groupId: 'com.mkyong', nexusUrl: 'localhost:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
            }
            
        }
        
        stage('DEV Deploy'){
            
            steps {
                
                deploy adapters: [tomcat9(credentialsId: 'a0ff79e5-01b9-45bc-94b2-f2565dbf0bb1', path: '', url: 'http://localhost:8181/')], contextPath: null, war: '**/*.war'
            }
            
        }
        
        stage('Send Slack Message'){
            
            steps {
                
               slackSend channel: 'jenkins-project', message: 'Dev Deployment was successfule'
            }
            
        }
        
        stage('Dev approval'){
            steps{
                echo 'Taking approval from Dev Manager for QA Deployment'
                timeout(time:7,unit:'DAYS'){
                    input message:'Do you want to deploy into QA ?', submitter:'admin'
                }
            }
        }
        
        stage('QA Deploy'){
            
            steps{
                echo 'Deploy to QA Tomcat'
            }
            
            
        }
    }
}
   
