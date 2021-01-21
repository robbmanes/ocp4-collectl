# ocp4-collectl
Run the `collectl` utility for performance monitoring and troubleshooting on Red Hat OpenShift 4 clusters.

**This repository has been archived in favor of [openshift4-debug](https://github.com/robbmanes/openshift4-debug), which provides the same functionality.  Please use the newer set of utilities instead.**

# About
The ocp4-collectl utility runs as a DaemonSet across your OpenShift 4.X cluster, allowing you gather per-node collectl reports.

It runs as a privileged container in a DaemonSet, with full access to /proc, /sys, and /dev/mem, and with root access to the node.  It runs in its own security context, but has full privileged access as the root user.

Each pod log will be from a different node, where it will track occurances of hung tasks and their respective stacks, along with timestamps.

This is a debugging tool provided without warranty.  It offers no support from Red Hat or any other official source.  Please use at your own risk.

# Deploy on a Red Hat OpenShift 4.X cluster
- Assuming you have set values as desired in the `ocp4-collectl-deploy.yaml` file, create resources with:
```
$ oc create -f ocp4-collectl-deploy.yaml
```
Be aware that this will launch the `collectl` pod across all nodes in your cluster.  If this is not desired, please use a NodeSelector in the DaemonSet portion of the template.

# Remove from a Red Hat OpenShift 4.X cluster
- To remove all resources relating to `ocp4-collectl` on your cluster, assuming you deployed with the above instructions, run:
```
$ oc delete all -l=app=ocp4-collectl; oc delete scc ocp4-collectl-scc; oc delete sa ocp4-collectl-sa; oc delete project ocp4-collectl
```

# Configuration
By default, this DaemonSet mounts the node's /var/log/ directory and creates a /var/log/collectl directory which will fill with files as `collectl` runs.  These files are using default `collectl` settings as providing by the `collectl` package from [EPEL](https://fedoraproject.org/wiki/EPEL).  A ConfigMap can be created and mounted over /etc/collectl.conf to alter configuration.  In addition, for more persistant storage or remote storage, feel free to remove the hostPath which is mounting in /var/log and use your own volume, but be aware; if you are troubleshooting and it potentially relies on volume-performance issues, you may either exacerbate the problem or cause new issues, as is true with any writing or reading to volumes with any application.

This container image is built using the `registry.access.redhat.com/ubi7/ubi` image and gets collectl from EPEL, and versioning of the container image matches the collectl version present in the image.

# License
    Copyright 2020 Robert Thomas Manes <robbmanes@protonmail.com>

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
