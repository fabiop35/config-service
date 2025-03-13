#Book CNSIA: Externalized configuration management using Spring Cloud Config Server.

#create project
curl https://start.spring.io/starter.zip -d groupId=com.cnsia.polarbookshop -d artifactId=config-service -d name=config-service -d packageName=com.cnsia.polarbookshop.configservice -d dependencies=cloud-config-server,devtools -d javaVersion=21 -d bootVersion=3.4.1 -d type=gradle-project -o config-service.zip

#Get the configuration that's returned when no Spring profile is active
http :8888/catalog-service/default

#Get the configuration for the scenario where the prod profile is active
 http :8888/catalog-service/prod

#Package the application as a container using Buildpack
./gradlew bootBuildImage

#run the image
docker run --rm --name config-service -p 8888:8888 config-service:0.0.1-SNAPSHOT

#import local image: 
minikube image load config-service:0.0.1-SNAPSHOT

#list the available images: 
minikube image ls --format table

#create a deployment resource from a container image: 
kubectl create deployment config-service --image=config-service:0.0.1-SNAPSHOT

#command to expose a Deployment as a Service - make applications accessible
kubectl expose deployment config-service --name=config-service --port=8888

#forwarding a port from localhost to a Service inside the cluster: 
kubectl port-forward service/config-service 8001:8888

#Get the configuration
http :8001/catalog-service/default

#build and publish the image
./gradlew bootBuildImage --imageName ghcr.io/fabiop35/config-service --publishImage -PregistryUrl=ghcr.io -PregistryUsername=fabiop35 -PregistryToken=<github_token>
