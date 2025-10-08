# Cluster Toolkit cloud builder
This builder can be used to run the Cluster Toolkit tool in GKE.

## Getting started

If you are new to Google Cloud Build, we recommend you start by visiting the [manage resources page](https://console.cloud.google.com/cloud-resource-manager) in the Cloud Console, [enable billing](https://cloud.google.com/billing/docs/how-to/modify-project), [enable the Cloud Build API](https://console.cloud.google.com/flows/enableapi?apiid=cloudbuild.googleapis.com), and [install the Cloud SDK](https://cloud.google.com/sdk/docs/).

This builder can be used to run the Cluster Toolkit tool in the GCE.

### Building this builder

To build this builder with the default version, run the following command in this directory.
```sh
$ gcloud builds submit --config=cloudbuild.yaml
```

To override the builder version for Cluster Toolkit, run the following command in this directory:

```
$ gcloud builds submit --config=cloudbuild.yaml \
  --substitutions=_GCLUSTER_VERSION="v1.67.0"
```

## Using this builder

### Cluster Toolkit backend
The default backend for Cluster Toolkit is local, which will store state information the working directory. Most build platforms (including Cloud Build) do not persist the working directory between builds. Losing this state information is no bueno.

Cluster Toolkit stores state information about infrastructure it has provisioned.
It uses this to plan out the delta between what your configuration files specifiy, and what's actually out there.
This state can be stored in different ways by Cluster Toolkit.


There are a couple of options for managing Cluster Toolkit state across builds:

###### Ignore the issue

In your build, you'll want to initialize Cluster Toolkit and refresh the local state.
This is really **not a good idea**; it'll be slow and not multi-run safe
(if multiple runs kick off concurrently, there'll be nastiness such as race conditions).

###### Persist the state in a GCS bucket manually

In your build, set up steps to manually fetch the state before running Cluster Toolkit,
then push it back up after Cluster Toolkit is done. This will help by removing the need
to init & refresh on every build; but will not address the concurrency issues.

###### Use a backend for remote storage

This is probably what you want to do. You'll still need to set up your GCS storage,
and you'll need to configure the backend in your configurations.
Some backends support locking of the remote state. This helps address the concurrency issue.
[gcs-backend](examples/gcs-backend/README.markdown) is an example of this approach.

<!-- ### Concurrently deploying to multiple regions

Platform teams usually attempt to complete the full set of their deployments within a certain time. [This example](./examples/infra_at_scale/) demonstrates how you can concurrently deploy your infrastructure to multiple regions with Cloud Build and Cluster Toolkit. -->

### Using this builder image anywhere else
This image can be run on any Docker host, without GCE. Why would you want to do this?
It'll let you run Cluster Toolkit locally, with no environment dependencies other than a Docker host installation.
You can use the [local Cloud Build][cloud-build-local-debug] for this;
but if you're curious or have weird / advanced requirements (for example, if you want
to run Cluster Toolkit as a build step on another platform like Travis or Circle CI,
and don't want to use Cloud Build, it is an option).

You'll need to:

 1. Provide a service account key file
 2. Mount your project directory at '/workspace' when you run docker
 ```sh
docker run -it --rm -e GCLOUD_SERVICE_KEY=${GCLOUD_SERVICE_KEY} \
  --mount type=bind,source=$PWD,target=/workdir \
  -w="/workdir" \
  gcr.io/$PROJECT_ID/gcluster <command>
```

[cloud-build-local-debug]: https://cloud.google.com/cloud-build/docs/build-debug-locally