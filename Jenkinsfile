pipeline {
    agent any
    tools {
        maven 'local maven'
    }
    stages {
        stage('build') {
            steps {
                sh 'mvn -B -U clean install'
            }
        }

        stage('test') {
            steps {
                echo 'testing the application...'
            }
        }

        stage('deploy') {
            steps {
                echo 'deploying the application...'
            }   
        }
    }
}