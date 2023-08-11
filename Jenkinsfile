pipeline {
    agent any

    triggers {
        pollSCM('*/3 * * * *')
    }

    environment {
        AWS_ACCESS_KEY_ID = credentials('AWSAccessKey')
        AWS_SECRET_ACCESS_KEY = credentials('AWSSecretAccessKey')
        AWS_DEFAULT_REGION = 'ap-southeast-2'
        HOME = '.'
    }

    stages {
        stage('Prepare') {
            agent any

            steps {
                echo "Lets start Long Journey!"
                echo "Cloning Repository"

                git url: "https://github.com/jamkim1997/jenkins",
                    branch: "main",
                    credentialsId: 'github_jenkins'
            }

            post {
                success {
                    echo "Successfully pulled Repository"
                }

                always {
                    echo "I tried.."
                }

                cleanup {
                    echo "after all other post condition"
                }
            }
        }

        stage('Deploy Frontend') {
            steps {
                echo "Deploying Frontend"
                dir ('./nodeapp/website') {
                    sh "aws s3 sync ./ s3://jenkinstesingjere"
                }
            }

            post {
                success {
                    echo "Successfully Cloned Repository"

                    mail to: 'tacop70809@gmail.com',
                        subject: "Deploy Frontend Success",
                        body: "Successfully deployed frontend!"
                     }

                    failure {
                        echo "I failed :("

                        mail to: 'tacop70809@gmail.com',
                            subject: "Failed pipeline",
                            body: "Something is wrong with deploy frontend"
                    }
            }
        }
        stage('Lint Backend') {
            agent {
                docker {
                    image 'node:latest'
                }
            }

            steps {
                dir ('./server') {
                    sh '''
                    npm install&&
                    npm run lint
                    '''
                }
            }
        }
    }
}