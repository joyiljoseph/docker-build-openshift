##  Performing a docker build within OpenShift

Clone this git repo to your local disk and then use that to create a publicly visible git rep on github.com (the public one, not the IBM enterprise git repo).
It is possible to get this working from the IBM enterprise git repo, but that is a separate excercise. After this code is in the github.com repo, run the 
following instructions.

This is extending the material in exercise 1 (building from base container). There a base image was provided in box, and you performed a docker build using that base image in your local
machine. Here, that base image that was provided in exercise 1 will be built within OpenShift. The provided Dockerfile will help you with that. In order to get started, clone this git 
repo to your local disk and then use that to create a publicly visible git rep on github.com (the public one, not the IBM enterprise git repo). It is also possible to get this working 
from the IBM enterprise git repo, but that is a separate excercise. After this code is in the github.com repo, run the following instructions.

```
oc new-app --name=simple-stuff https://github.com/<your github.com id>/docker-builds-openshift.git --as-deployment-config
oc create route edge --service=simple-stuff
```

And then, `curl -k https://<hostname for route>/simple-stuff/simple/simon`. This should produce the string "/my-special-folder does not exist"

## Task

0. Delete all existing artifiacts from the previous steps. 
```
zaphod:sa-bootcamp$ oc delete all -l app=simple-stuff
service "simple-stuff" deleted
deployment.apps "simple-stuff" deleted
buildconfig.build.openshift.io "simple-stuff" deleted
build.build.openshift.io "simple-stuff-1" deleted
imagestream.image.openshift.io "simple-stuff" deleted
imagestream.image.openshift.io "websphere-liberty" deleted
route.route.openshift.io "simple-stuff" deleted
zaphod:sa-bootcamp$ 
```

1. In the docker build, create a directory called /my-special-folder. Copy the Dockerfile in this git repo into that folder. Note that you will need to create this
folder as the root user, otherwise, your creation of the directory will fail during the build.

A Dockerfile.answer file is provided in this git repo which shows how this step is done.

2. Repeat the new-app, and "create route" commands provided at the top of this file.
```
zaphod:sa-bootcamp$ oc new-app --name=simple-stuff https://github.com/kstephen314159/day3-simple-stuff.git
--> Found container image 1db9786 (16 months old) from docker.io for "docker.io/websphere-liberty:javaee8"

    * An image stream tag will be created as "websphere-liberty:javaee8" that will track the source image
    * A Docker build using source code from https://github.com/kstephen314159/day3-simple-stuff.git will be created
      * The resulting image will be pushed to image stream tag "simple-stuff:latest"
      * Every time "websphere-liberty:javaee8" changes a new build will be triggered

--> Creating resources ...
    imagestream.image.openshift.io "websphere-liberty" created
    imagestream.image.openshift.io "simple-stuff" created
    buildconfig.build.openshift.io "simple-stuff" created
    deployment.apps "simple-stuff" created
    service "simple-stuff" created
--> Success
    Build scheduled, use 'oc logs -f buildconfig/simple-stuff' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose service/simple-stuff' 
    Run 'oc status' to view your app.
zaphod:sa-bootcamp$ oc create route edge --service=simple-stuff
route.route.openshift.io/simple-stuff created
zaphod:sa-bootcamp$
```
If you are using this repo without changes (i.e you haven't copied Dockerfile.answer on top of Dockerfile, or merged the two), then you will 
have to perform some additional steps. Perform the following command:
```
oc edit bc/simple-stuff
```
This will open a "vi" sesssion. Scroll down in the file until you come to the "dockerStrategy" element. Underneath it, as a child, add the following: "dockerfilePath: Dockerfile.answer". This is what that section of the yaml file will look like when you are done:
```
  source:
    git:
      uri: https://github.com/kstephen314159/docker-builds-openshift-answer.git
    type: Git
  strategy:
    dockerStrategy:
      dockerfilePath: Dockerfile.answer
      from:
        kind: ImageStreamTag
        name: websphere-liberty:20.0.0.5-full-java11-openj9-ubi
    type: Docker
```
After you have done this, restart the build by saying "oc start-build simple-stuff". This new build will build using the "Dockerfile.answer" dockerfile.
3. Repeat steps 1 and 2, but this time, call the app "even-simpler". What you have done with this step is to use the same git repo to create two different running instances (or applications) 
of the code.
4. Look for the image stream corresponding to "even-simpler". Use the "new-app" command and point to this imagestream and create a third instance of the application. Call this application
instance "simplest-of-all".
4. Provide the output of the "curl" command shown above for the "simple", "even-simpler", and "simplest-of-all" apps.
