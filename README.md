## Start jenkins
```
docker run \
--name jenkins \
-e JNLP_PROTOCOL_OPTS=-Dorg.jenkinsci.remoting.engine.JnlpProtocol3.disabled=false \
-v /mnt/sda1/jenkins_home:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
-p 8080:8080 \
-p 50000:50000 \
-d --user root \
jenkinsci/blueocean
```

## Jenkins agent node
```
docker run --name deploy \
-v /var/run/docker.sock:/var/run/docker.sock \
-d \
adriagalin/jenkins-jnlp-slave \
-url <url> <token> <node name>
```
## Jenkinsfile
```
pipeline {
    agent any
    environment {
        APP_NAME = "test app name"
        IMAGE_NAME = "registry.gitlab.com/sommai.k/simple"
    }
    stages {
        stage("Build") {
            steps {
                sh "docker build -t ${env.IMAGE_NAME}:${env.BUILD_NUMBER} ."
            }
        }
        stage("Package") {
            steps {
                withCredentials(
                [usernamePassword(
                    credentialsId: 'gitlab-id',
                    passwordVariable: 'gitlabPassword',
                    usernameVariable: 'gitlabUser'
                )]
            ){
                sh "docker login -u ${env.gitlabUser} -p ${env.gitlabPassword} registry.gitlab.com"
                sh "docker push ${env.IMAGE_NAME}:${env.BUILD_NUMBER}"
            }
            }
        }
        stage('Deploy') {
          steps {
              script {
                  try {
                    sh "docker service update simple --with-registry-auth --image ${env.IMAGE_NAME}:${env.BUILD_NUMBER}"
                    sh "echo update service"
                  } catch (e){
                    sh "docker service create --with-registry-auth --name simple -p 80:80 ${env.IMAGE_NAME}:${env.BUILD_NUMBER}"
                    sh "echo create service"
                  }
              }
          }
       }
    }
}
```
