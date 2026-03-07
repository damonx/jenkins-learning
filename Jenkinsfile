pipeline {
    agent any
    parameters {
        string(name: 'GREETING', defaultValue: 'Hello, World!', description: 'A greeting message to display')
        string(name: 'NAME', defaultValue: 'DamonX', description: 'Your name')
        choice(name: 'VERSION', choices: ['1.0', '2.0', '3.0'], description: 'Select the version to build')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Whether to run tests after building')
    }
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
                echo "${params.GREETING} My name is ${params.NAME}."
            }
        }

        stage('test') {
            when {
                expression { env.BRANCH_NAME != 'main' && params.RUN_TESTS }
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
                echo "deploying version ${params.VERSION} to production"

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