def gv
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
        SERVER_CREDENTIALS = credentials('damonx-server-credentials')
    }

    tools {
        maven 'LOCAL-MAVEN'
    }

    triggers {
        pollSCM('* * * * *')
    }   
    stages {
        stage('init') {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage('build') {
            steps {
                script {
                    gv.buildApp()
                }
                echo "building version ${env.NEW_VERSION}"
                echo "${params.GREETING} My name is ${params.NAME}."
                sh 'mvn -B -U clean package'
                echo 'Building docker image hello-damonx...'
                sh 'docker build -t hello-damonx:${env.BUILD_NUMBER} ./build-support'
            }
            post {
                success {
                    echo 'Build stage succeeded!'
                    echo 'Archive the build artifacts...'
                    archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
                }
            }
        }

        stage('test') {
            when {
                expression { env.BRANCH_NAME != 'main' && params.RUN_TESTS }
            }
            steps {
                echo 'Testing the application...'
            }
        }

        stage('deploy') {
            when {
                expression { env.BRANCH_NAME == 'main' }
            }
            steps {
                timeout(time:5, unit:'DAYS'){
                    input message:'Deploy to PROD?' 
                }
                echo 'deploying the application...damonx'
                echo "deploying version ${params.VERSION} to production"

                withCredentials([usernamePassword(
                    credentialsId: 'damonx-server-credentials',
                    usernameVariable: 'USER',
                    passwordVariable: 'PWD'
                )]) {
                    sh "echo Deploying with user ${USER}"
                }
                // build job: 'deploy-to-staging'
            }
            post {
                success {
                    echo 'Deployment succeeded!'
                }
                failure {
                    echo 'Deployment failed. Please check the logs.'
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
