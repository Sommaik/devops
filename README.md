```
docker run -p 8080:8080 -p 50000:50000 \
-e JNLP_PROTOCOL_OPTS=-Dorg.jenkinsci.remoting.engine.JnlpProtocol3.disabled=false \
-v /var/run/docker.sock:/var/run/docker.sock:ro \
-v /mnt/sda1/jenkins:/var/jenkins_home \
--name jenkins \
-d --user root jenkins/jenkins:lts
```
