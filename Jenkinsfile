pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS=credentials("dockerhub")
        DOCKER_HUB_REPO = "gonzalesl/pipeline-project"
        CONTAINER_NAME = "flask-container"
        STUB_VALUE = "200"
    }
    stages {
        stage('Stubs-Replacement'){
            steps {
                // 'STUB_VALUE' Environment Variable declared in Jenkins Configuration 
                echo "STUB_VALUE = ${STUB_VALUE}"
                sh "sed -i 's/<STUB_VALUE>/$STUB_VALUE/g' config.py"
                sh 'cat config.py'
            }
        }
        stage('Login') {
            steps {
                sh "echo $DOCKER_HUB_CREDENTIALS_PSW |  docker login -u $DOCKER_HUB_CREDENTIALS_USR --password-stdin"
            }
        }
        stage('Build') {
            steps {
                //  Building new image
                sh 'docker image build -t $DOCKER_HUB_REPO:latest .'
                sh 'docker image tag $DOCKER_HUB_REPO:latest $DOCKER_HUB_REPO:$BUILD_NUMBER'

                //  Pushing Image to Repository
                sh 'docker push $DOCKER_HUB_REPO:$BUILD_NUMBER'
                sh 'docker push $DOCKER_HUB_REPO:latest'
                
                echo "Image built and pushed to repository"
            }
        }
        stage('Deploy') {
            steps {
                script{
                    //sh 'BUILD_NUMBER = ${BUILD_NUMBER}'
                    if (BUILD_NUMBER == "8") {
                        sh 'docker run --name $CONTAINER_NAME -d -p 5000:5000 $DOCKER_HUB_REPO'
                    }
                    else {
                        sh 'docker stop $CONTAINER_NAME'
                        sh 'docker rm $CONTAINER_NAME'
                        sh 'docker run --name $CONTAINER_NAME -d -p 5000:5000 $DOCKER_HUB_REPO'
                    }
                    //sh 'echo "Latest image/code deployed"'
                }
            }
        }
    }
}
