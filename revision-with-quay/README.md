# revision-with-quay

An example pipeline which builds a Maven application using a build number and then publishes the image to Quay.

To test this out:

    $ oc process -f openshift-template.yml -p NAMESPACE=my-namespace -p GIT_USERNAME=something -p GIT_PASSWORD=something | oc apply -f -
