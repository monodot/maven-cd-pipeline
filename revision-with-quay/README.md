# revision-with-quay

An example pipeline for OpenShift, which builds a Maven application using a build number and then simulates promoting an image across clusters by publishing the image to Quay.

This pipeline builds the [hello-java][hellojava] application.

## To test this out...

Create a new namespace:

    $ oc new-project my-namespace

Build a slave image for Jenkins which contains the _skopeo_ container image copying utility, as included in the [Red Hat CoP containers-quickstarts repo][contquick] (this is so we can copy the image to Quay):

    $ git clone https://github.com/redhat-cop/containers-quickstarts && cd containers-quickstarts
    $ oc process -f .openshift/templates/jenkins-slave-image-mgmt-template.yml | oc apply -f-

Create a secret containing your Quay registry credentials:

    $ oc create secret docker-registry quay --docker-username=yourusername --docker-password=yourpassword --docker-server=quay.yourcompany.com

Clone this repo and process the template provided, specifying the Git credentials that Jenkins can use to commit to the application source repository:

    $ git clone https://github.com/monodot/maven-cd-pipelines && cd revision-with-quay
    $ oc process -f openshift-template.yml -p NAMESPACE=my-namespace -p GIT_USERNAME=something -p GIT_PASSWORD=something | oc apply -f -

[hellojava]: https://github.com/monodot/hello-java
[contquick]: https://github.com/redhat-cop/containers-quickstarts
