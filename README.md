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
2. Repeat the new-app, expose, and patch commands provided at the top of this file.
3. Repeat steps 1 and 2, but this time, call the app "even-simpler". What you have done with this step is to use the same git repo to create two different running instances (or applications) 
of the code.
4. Look for the image stream corresponding to "even-simpler". Use the "new-app" command and point to this imagestream and create a third instance of the application. Call this application
instance "simplest-of-all".
4. Provide the output of the "curl" command shown above for the "simple", "even-simpler", and "simplest-of-all" apps.
