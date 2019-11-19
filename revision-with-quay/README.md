# revision-with-quay

An example pipeline for OpenShift, which builds a Maven application using a build number and then simulates promoting an image across clusters by publishing the image to Quay.

This pipeline builds the [hello-java][hellojava] application.

## To test this out...

Create a new namespace:

    $ oc new-project my-namespace

Clone the [Red Hat CoP containers-quickstarts repo][contquick] repository and use the template provided to build a slave image for Jenkins containing the _skopeo_ utility (we will use this utility to copy a container image to Quay):

    $ git clone https://github.com/redhat-cop/containers-quickstarts && cd containers-quickstarts
    $ oc process -f .openshift/templates/jenkins-slave-image-mgmt-template.yml | oc apply -f-

Create a secret containing your Quay registry credentials:

    $ oc create secret docker-registry quay \
        --docker-username=yourusername \
        --docker-password=yourpassword
        --docker-server=quay.examplecat.com

Clone this repo and process the included template, specifying credentials for Git that Jenkins can use to commit to the application source repository:

    $ git clone https://github.com/monodot/maven-cd-pipelines && cd revision-with-quay
    $ oc process -f openshift-template.yml -p NAMESPACE=my-namespace -p GIT_USERNAME=something -p GIT_PASSWORD=something | oc apply -f -

[hellojava]: https://github.com/monodot/hello-java
[contquick]: https://github.com/redhat-cop/containers-quickstarts
