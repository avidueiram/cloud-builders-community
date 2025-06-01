# firebase-puppeteer

This build step invokes `firebase` commands that can be used in [Google Cloud Build](https://cloud.google.com/cloud-build/).

You can find the official Google Cloud guide for this build processor [here](https://cloud.google.com/build/docs/deploying-builds/deploy-firebase).

Arguments passed to this builder will be passed to `firebase` directly, allowing callers to run [any firebase command](https://firebase.google.com/docs/cli/#command_reference).

Important note: This variant (`firebase-puppeteer`) is an extended version of the official `firebase` Cloud Build image. It is designed for projects that need to run headless Chrome tests (e.g. using Puppeteer or Playwright) while Firebase emulators are running.

_No global Puppeteer installed — projects can use their own `puppeteer` or `puppeteer-core` version_

## Usage

The Firebase CLI can be authenticated in Cloud Build by IAM Roles that are granted to the Cloud Build service account.

Ensure you have the following APIs enabled:

1. [Cloud Resource Manager API](https://console.developers.google.com/apis/api/cloudresourcemanager.googleapis.com)
2. [Firebase Management API](https://console.developers.google.com/apis/api/firebase.googleapis.com)

And add permission to the cloudbuilder:

- Open Cloud Build service account settings
- Give the Cloud Build service account the **Firebase Admin** and **Service Account User** roles:

Trigger a build with the following command:

```bash
gcloud builds submit .
```

_Remember to set your gcloud cli to the correct project with: `gcloud config set project <project-id>`_

## Examples

See examples in the `examples` subdirectory.
