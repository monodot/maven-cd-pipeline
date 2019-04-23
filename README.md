# maven-cd-pipeline

An example pipeline for continuous deployment of a Java Maven application (with release/versioning) on OpenShift.

## Deploy on OpenShift

To deploy on an OpenShift cluster, first create a Nexus instance if one doesn't already exist:

    $ oc create -f https://raw.githubusercontent.com/redhat-cop/openshift-templates/master/nexus/nexus-deployment-template.yml
    $ oc new-app nexus

Then, assuming you've already got the Jenkins templates installed into your cluster:

    $ oc new-app jenkins-ephemeral

Finally create the pipeline, imagestream and buildconfig:

    $ oc process -f openshift-pipeline.yml -p NAMESPACE=tdonohue-cicd | oc create -f -

