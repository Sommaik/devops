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
                // sh "cat /home/jenkins/.docker/config.json"
            }
        }
        stage("deploy"){
            steps {
                sshagent(credentials['uat-server']) {
                    sh "ssh docker pull hello-world"
                }
            } 
        }
        // stage("push image"){
        //     steps {
        //         script{
        //             docker.withRegistry(
        //                 'http://registry.pnpsw.com', 'sommai_k'
        //             ) {
        //                 def image = docker.build("${env.imageName}:1.${env.BUILD_NUMBER}")
        //                 image.push()
        //             }
        //         }
        //     }
        // }

    }
}

