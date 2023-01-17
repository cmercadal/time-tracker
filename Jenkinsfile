pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "Maven3"
        jdk "Java11"
    }

    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'master', url: 'https://github.com/bellyster/time-tracker.git'

                // Run Maven on a Unix agent.
                //sh "mvn -Dmaven.test.failure.ignore=true clean package"

                // To run Maven on a Windows agent, use
                bat "mvn clean package -DskipTests"
            }

            post {
                // failed, record the artifacts.
                success {
                    echo 'Archivando artefacto'
                    archiveArtifacts 'core/target/*.jar'
                    archiveArtifacts 'web/target/*.war'
                }
            }
        }

        stage('UNIT TESTS'){
             steps{
                 //Unix version
                 //sh: 'mvn test'
                 //Ejecutar los tests
                 echo 'Ejecutando Tests'
                 bat "mvn test"
                 }
             }

    }
}
