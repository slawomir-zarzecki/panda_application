pipeline {
    agent {
  label 'docker_slave'
}


    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MVN_36"
    }
    
    environment {
        IMAGE = ""
        VERSION =""
      // IMAGE = readMavenPom().getArtifactId()
      // VERSION  = readMavenPom().getVersion()
        
    }

    stages {
        
        stage('Cleaning app'){
           
           steps {
             
             sh 'docker rm -f pandaapp || true'
          
           } 
            
            
        }
        
        stage('Get code from Git'){
         steps{
        //git branch: 'jenkins_pipeline_mine', credentialsId: 'GitHub2', url: 'https://github.com/slawomir-zarzecki/panda_application.git'
        // git credentialsId: 'GitHub2', url: 'https://github.com/slawomir-zarzecki/panda_application.git'
          checkout scm   
         }
        }
        
        
        stage('When'){
        when { branch 'dev'}
        steps{  sh "echo 'Hello'" }
        }
        
    
        
        stage('Build and run JUnit test') {
            steps {
                // Get some code from a GitHub repository
               

                // Run Maven on a Unix agent.
                sh "mvn clean install"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            
        }
        
        stage('Create docker image'){
         steps {
             
            sh "mvn package -Pdocker -Dmaven.test.skip=true"
            
         }    
       
            
        }
        
      stage('Start app'){
          
         steps {
             script {
                 IMAGE = readMavenPom().getArtifactId()
                 VERSION  = readMavenPom().getVersion()   
                }
             
            sh "docker run -d --name pandaapp -p 0.0.0.0:8080:8080 -t ${IMAGE}:${VERSION}"
            
         }    
       
            
        }
        
        
        stage('Selenium Tests'){
         steps{
             
             sh "mvn clean test -Pselenium" 
         }    
            
        }
        
        stage('Deploy image to artifactory'){
         steps{
             configFileProvider([configFile(fileId: '5bce6a62-e35a-49f4-b215-6d4dc9f5ffec', variable: 'MAVEN_GLOBAL_SETTINGS')]) {
             sh "mvn -s $MAVEN_GLOBAL_SETTINGS deploy -Dmaven.test.skip=true "
}
           
         }    
       
        }
        
        
        
  
        
    }
    
         post {
          
               always {
                
                sh 'docker stop pandaapp'
                junit '**/target/surefire-reports/TEST-*.xml'
                archiveArtifacts 'target/*.jar'
                //deleteDir()
                   
           }
            }
    
}
