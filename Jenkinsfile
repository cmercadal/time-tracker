pipeline {
    agent any
    
    tools {
        maven "mvn386"
        jdk "java11"
    }
    
    environment {
        DISABLE_AUTH = true
        DB_ENGINE = 'sqlite'
        
    }
    
    stages {
        stage('Hello') {
            steps {
                echo "Hello World ${DB_ENGINE}"
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
            }
        }
        stage("Git pulling"){
            steps {
                //git branch: 'master', url: 'https://github.com/cmercadal/time-tracker.git'
                 // Checkout the repository using SSH
                // git branch:'master', credentialsID
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/master']],
                          userRemoteConfigs: [[url: 'git@github.com:cmercadal/time-tracker.git']],
                          extensions: [[$class: 'CleanBeforeCheckout']]])
            }
        }
        
        stage("Build con Maven"){
            steps{
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
            
            post{
                success{
                echo 'archivando artefactos'
                archiveArtifacts "core/target/*.jar"
                archiveArtifacts "web/target/*.war" 
                }
            }
        }
        
        stage("Test maven"){
            steps{
                sh "mvn test"
            }
        }
        
    }
}
