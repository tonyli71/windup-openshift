# windup-openshift
## OpenShift image construction
For building this project and creating Docker images you have to have a Docker instance running locally on your machine so that you can pull the built images.
### Install Docker for Fedora
1. Install Docker: `$ sudo dnf install docker`
1. Configure `docker` group (ref. [Manage Docker as a non-root user](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)):
   1. `$ sudo groupadd docker`
   1. `$ sudo usermod -aG docker $USER`
   1. Log out and log back in so that your group membership is re-evaluated
1. Test Docker is working: `$ docker run hello-world`

For any issue related to installation, you can refer to [Docker documentation](https://docs.docker.com/install/linux/docker-ce/fedora/).
### Install Docker for other OS
[Install Docker](https://docs.docker.com/install/)
### Create an account to Docker Hub
You need an account on https://hub.docker.com in order to push your images and have them available in the [docker.io repository](https://docs.openshift.org/latest/architecture/core_concepts/builds_and_image_streams.html#important-terms).  
So sign up yourself to Docker Hub at https://hub.docker.com taking care that the Docker ID you choose will be the `<your_docker_id>` value in the next steps.
### Create Docker images
1. Build this project: `$ mvn clean install -Ddocker.name.windup.web=<your_docker_id>/windup-web-openshift -Ddocker.name.windup.web.executor=<your_docker_id>/windup-web-openshift-messaging-executor`
1. Push images to docker hub:
   1. `$ docker login`
   1. `$ docker push <your_docker_id>/windup-web-openshift`
   1. `$ docker push <your_docker_id>/windup-web-openshift-messaging-executor`

If you want you can also set the tag for the built images (e.g. if you are working on a specific branch and you want to create images tagged with the branch name), you just have to add the tag name to the `docker.name.windup.web` and `docker.name.windup.web.executor` system properties' values (i.e. from the above example `-Ddocker.name.windup.web=<your_docker_id>/windup-web-openshift:tag_value -Ddocker.name.windup.web.executor=<your_docker_id>/windup-web-openshift-messaging-executor:tag_value`)
   
## OpenShift template deployment
There are two different ways for deploying RHAMT on OpenShift based upon if you have [`cluster-admin privileges`](https://docs.openshift.org/latest/architecture/additional_concepts/authorization.html#roles): if you have those privileges you can go decide to follow [Template deployment in OpenShift catalog](#template-deployment-in-openshift-catalog) (because you can operate on the default `openshift` project) or [Import template in Openshift Web Console](#import-template-in-openshift-web-console) otherwise you can just go with the latter approach ([Import template in Openshift Web Console](#import-template-in-openshift-web-console))

### Template deployment in OpenShift catalog
1. login to Openshift: `$ oc login`
1. create the template: `$ oc create -f ./rhamt_template.json -n openshift`

Now, if you go to OpenShift Web Console home page, you'll see the Red Hat Application Migration Toolkit in the list of the available templates and so you can deploy it to a project just like any other template.

### Import template in Openshift Web Console
1. copy the raw content of file [web-template.json](web/templates/web-template.json)
1. paste it in the "Import YAML / JSON" wizard in Openshift Web Console
1. save and wait for the deployment to end
