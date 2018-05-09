pipeline {
    agent any
    environment { 
        imageName = "sommaik/hello-nginx"
    }
    stages {
        stage("Prepare"){
            steps {
                echo "Hello World"
            }
        }
        stage("check version"){
            steps {
                sh "docker --version"
            }
        }
        stage("build image"){
            steps {
                sh "docker build -t ${env.imageName} ."
                sh "docker tag ${env.imageName} ${env.imageName}:1.${env.BUILD_NUMBER}"
            }
        }
        stage("push image"){
            
            
            steps {
                script{
                    docker.withRegistry('https://hub.docker.com', 'docker-user') {
                        def image = docker.build("${env.imageName}:1.${env.BUILD_NUMBER}", "--build-arg .")
                        image.push()
                    }
                }
                //sh "docker push ${env.imageName}"
            }
        }

    }
}

