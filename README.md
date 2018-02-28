### Jenkins pipeline

This is what you are going to need to do this how to :
1) A kubernetes cluster ( it can be the real deal or minikube)
2) Account with docker hub to push your images( Or you can use your own registry if you know what you are doing :) )
3) Account with github
4) Fork my repo ( `git clone https://github.com/airwolfnh/kubernetes-ci-cd-jenkins.git`)
5) Docker 

So let's do this 
1) If you are doing with minikube, you can start it like this :
1.1) Start up the Kubernetes cluster with Minikube, giving it some extra resources. : minikube start --memory 4000 --cpus 2 --kubernetes-version v1.8.0
1.2) Enable the Minikube add-ons Heapster and Ingress: minikube addons enable heapster; minikube addons enable ingress

2) Deploy the public nginx image from DockerHub into a pod. Nginx is an open source web server that will automatically download from Docker Hub if it’s not available locally : kubectl run nginx --image nginx --port 80

3) Create a service for deployment. This will expose the nginx pod so you can access it with a web browser: kubectl expose deployment nginx --type NodePort --port 80

4) Launch a web browser to test the service. The nginx welcome page displays, which means the service is up and running : minikube service nginx 

5) git clone ....

6) Let’s make a change to an HTML file in the cloned project. Open the /applications/hello-airwolfnh/index.html file in your favorite text editor (for example, you can use nano by running the command 'nano applications/hello-airwolfnh/index.html' in a separate terminal). Change some text inside one of the tags. For example, change “Hello to you all!” to “Hello world!”. Save the file.

7) Now let’s build an image, giving it a special name that points to our local cluster registry.

docker build -t airwolfnh/hello-airwolfnh:latest -f applications/hello-airwolfnh/Dockerfile applications/hello-airwolfnh


8) Now you have to push this image to your registry , for me that would be :
docker loging ( put your user/pass)
docker push docker.io/airwolnh/hello-airwolfnh:latest ( replace airwolfnh with your id , ex: docker push docker.io/MY_DOCKER_USERNAME/hello-airwolfnh:latest)

9) With the image  now on docker registry, the last thing to do is apply the manifest to create and deploy the airwolfnh-kenzan pod based on the image.

kubectl apply -f applications/hello-airwolfnh/k8s/deployment.yaml

10) Launch a web browser and view the service : minikube service hello-airwolfnh


11) Now it's time to deploy jenkins.
kubectl apply -f manifests/jenkins.yml; kubectl rollout status deployment/jenkins

Now let's open the jenkins url 
```shell
minikube service jenkins
```

If you are not using minikube, do this instead to get the port 
```shell
kubectl get service jenkinskubectl get service jenkins
NAME      TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
jenkins   NodePort   10.106.52.24   <none>        80:30300/TCP   1d
```

12)
Now that you can see jenkins on your browser, it's going to ask for the admin password, type this to get it:
```shell
kubectl exec -it $(kubectl get pods --selector=app=jenkins --output=jsonpath={.items..metadata.name}) cat /root/.jenkins/secrets/initialAdminPassword
```

13) Now go back to your browser and type the password, also click Install suggested plugins and wait for the process to complete.
14) Create an admin user and credentials, and click Save and Finish. (Make sure to remember these credentials as you will need them for repeated logins.) Click Start using Jenkins.
15) We now want to create a new pipeline for use with our Hello-airwolfnh app. On the left, click New Item. Enter the item name as "Hello-airwolfnh Pipeline", select Pipeline, and click OK.
16) Under the Pipeline section at the bottom, change the Definition to be "Pipeline script from SCM". 
17) Change the SCM to Git.
18) Change the Repository URL to be the URL of your forked Git repository, such as https://github.com/[GIT USERNAME]/kubernetes-ci-cd-jenkins. Click Save. On the left, click Build Now to run the new pipeline.
19) Now view the hello-airwolfnh application.
```shell
minikube service hello-kenzan
```

Or if you are not using minikube :
```shell
$ kubectl get service hello-airwolfnh
NAME           TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
hello-airwolfnh   NodePort   10.104.169.226   <none>        80:31098/TCP   21h
```


13) Push a change to your fork. Run job again. View the changes by refreshing the webpage for the application on your browser.








Add into the jenkins bit
1) add git credentials
2) add docker credentials
3) openblue ocean
4) multi branch pipeline
5) How to deploy to multiple environments?
6) Show a video configuring jenkins


