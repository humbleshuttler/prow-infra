Copyright 2018 Google LLC

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

  https://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

# My PROW cluster

Here lives the my [prow](https://github.com/kubernetes/test-infra/tree/master/prow) config for personal prow cluster

Prow deck: http://34.102.160.45/

I am using [osconfig project](https://github.com/iamsubratp/osconfig) as one of my test repository to test my prow cluster.

## Cluster setup steps

#### GitHub bot account set up

Deploying prow manually, ensure you have created a
GitHub account for prow to use.  Prow will ignore most GitHub events generated
by this account, so it is important this account be separate from any users or
automation you wish to interact with prow. For example, you still need to do
this even if you'd just setting up a prow instance to work against your own
personal repos.

- Ensure the bot user has the following permissions
  - Write access to the repos you plan on handling
  - Owner access (and org membership) for the orgs you plan on handling (note
    it is possible to handle specific repos in an org without this)
- Create a [personal access token][1] for the GitHub bot account, adding the
   following scopes (more details [here][8])
  - Must have the `public_repo` and `repo:status` scopes
  - Add the `repo` scope if you plan on handing private repos
  - Add the `admin_org:hook` scope if you plan on handling a github org
- Set this token aside for later (we'll assume you wrote it to a file on your
   workstation at `/path/to/oauth/secret`)

#### Manual deployment

Prow runs in a kubernetes cluster, so first figure out which cluster you want to deploy prow into. If you already have a cluster, skip to the next step.

You can use the [GCP cloud console](https://console.cloud.google.com/) to set up a project and [create a new Kubernetes Engine cluster](https://console.cloud.google.com/kubernetes).

##### Create the cluster

I'm assuming that `PROJECT` and `ZONE` environment variables are set.

```sh
export PROJECT=your-project
export ZONE=us-west1-a
```

Run the following to create the cluster. This will also set up `kubectl` to
point to the new cluster.

```sh
gcloud container --project "${PROJECT}" clusters create prow \
  --zone "${ZONE}" --machine-type n1-standard-4 --num-nodes 2
```
