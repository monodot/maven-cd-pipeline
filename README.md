# maven-cd-pipeline

An example pipeline for continuous deployment of a Java Maven application (with release/versioning) on OpenShift.

This assumes that:

- You're running the version of Jenkins that comes bundled with OpenShift - because the _Jenkinsfile_ assumes a Jenkins agent node `maven` is defined.

## Deploy on OpenShift

To deploy on an OpenShift cluster, first create a Nexus instance if one doesn't already exist:

    $ oc create -f https://raw.githubusercontent.com/redhat-cop/openshift-templates/master/nexus/nexus-deployment-template.yml
    $ oc new-app nexus

Then, assuming you've already got the Jenkins templates installed into your cluster:

    $ oc new-app jenkins-ephemeral

Finally create the pipeline, imagestream and buildconfig:

    $ oc process -f openshift-template.yml \
        -p NAMESPACE=your-namespace-here \
        -p GIT_USERNAME=jenkinsgitusername \
        -p GIT_PASSWORD=jenkinsgitpass \
        | oc create -f -

