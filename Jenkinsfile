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

                // To run Maven on a Windows agent, use
                //sh "mvn clean package -DskipTests"
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

        stage('UNIT TESTS'){
             steps(){

                 //Ejecutar los tests
                 echo 'Ejecutando Tests'
                 sh "mvn test"
            }
        }

        stage("Sonar scan"){
            steps(){
                echo 'Sonar analysis'
                withSonarQubeEnv('sonar'){
                    sh 'mvn clean package sonar:sonar -Dsonar.junit.reportsPath=/target/surefire-reports/'
                }
            }
        }

        stage('Upload artifact'){
            steps{
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: 'localhost:8081',
                    groupId: 'com.camila',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: 'DevOpsRepo',
                    credentialsId: 'nexus',
                    artifacts: [
                        [artifactId: 'webApp',
                         classifier: '',
                         file: 'web/target/time-tracker-web-0.5.0-SNAPSHOT.war',
                         type: 'war'],
                         [artifactId: 'coreApp',
                         classifier: '',
                         file: 'core/target/time-tracker-core-0.5.0-SNAPSHOT.jar',
                         type: 'jar']
                        ]
                    )
                }
            }

    }
}
