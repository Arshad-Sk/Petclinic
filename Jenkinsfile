pipeline {
    agent any
    
     tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    
     environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
      
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }    
       
        stage('Git Checkout ') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/jaiswaladi246/Petclinic.git'
            }
        }
        
          stage('Code Compile') {
            steps {
                    sh "mvn clean compile"
            }
        }
                
         stage('Unit Tests') {
            steps {
                    sh "mvn test"
            }
        }
        
        
         stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
    
                }
            }
        }
                
         stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
           stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }
        
        stage("Docker Build "){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'b9f5bd82-bfa9-4e28-a440-6792e37b103e', toolName: 'docker') {
                        
                        sh "docker build -t petclinic1 ."
                       
                                           }
                }
            }
        }
        
        
          stage("Docker Tag & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'b9f5bd82-bfa9-4e28-a440-6792e37b103e', toolName: 'docker') {
                        
                        sh "docker tag petclinic1 sk77arshad/pet-clinic123:latest "
                        sh "docker push sk77arshad/pet-clinic123:latest "
                       
                                           }
                }
            }
        }
                
          stage("TRIVY"){
            steps{
                sh " trivy image sk77arshad/pet-clinic123:latest"
            }
        }
        
                 stage("Deploy To Tomcat"){
            steps{
                sh "cp  /var/lib/jenkins/workspace/RealworldCICD/target/petclinic.war /opt/tomcat9/webapps/"
            }
        }       
        
}
}
