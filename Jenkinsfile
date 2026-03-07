pipeline {
    agent any

    environment {
        NEW_VERSION = "1.0.${BUILD_NUMBER}"
        SERVER_CREDENTIALS = credentials('damonx-server')
    }

    tools {
        maven 'local maven'
    }

    stages {
        stage('build') {
            steps {
                sh 'mvn -B -U clean install'
                echo "building version ${env.NEW_VERSION}"
            }
        }

        stage('test') {
            when {
                expression { env.BRANCH_NAME != 'main' }
            }
            steps {
                echo 'testing the application...'
            }
        }

        stage('deploy') {
            when {
                expression { env.BRANCH_NAME == 'main' }
            }
            steps {
                echo 'deploying the application...'

                withCredentials([usernamePassword(
                    credentialsId: 'damonx-server-credentials',
                    usernameVariable: 'USER',
                    passwordVariable: 'PWD'
                )]) {
                    sh "echo Deploying with user ${USER}"
                }
            }
        }
    }

    post {
        always {
            echo 'Aha! This will always run, regardless of the build result.'
        }
        success {
            echo 'YAY! Build succeeded!'
        }
        failure {
            echo 'Whoops! Build failed.'
        }
    }
}