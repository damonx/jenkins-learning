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
            when {
                expression {
                    return env.BRANCH_NAME != 'main'
                }
            }
            steps {
                echo 'testing the application...'
            }
        }

        stage('deploy') {
            when {
                expression {
                    return env.BRANCH_NAME == 'main'
                }
            }
            steps {
                echo 'deploying the application...'
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