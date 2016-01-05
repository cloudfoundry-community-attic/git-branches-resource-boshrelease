BOSH Release for git-branches-resource
============================================

Tracks when branches (refs) are added or removed from a git repository, and returns a list of all current branches whenever any are added or removed.

This BOSH release packages @pivotaltracker's [git-branches-resource](https://github.com/pivotaltracker/git-branches-resource) for Concourse, to make it simple to include the additional Concourse resource in your BOSH deployed Concourse system.

Final releases are automatically created based on any changes to the upstream git-branches-resource

See the build pipeline https://ci.starkandwayne.com/pipelines/git-branches-resource-boshrelease for status.

<!-- TODO: Final releases are available on https://bosh.io/releases as well as this project's own [GitHub releases](https://github.com/cloudfoundry-community/git-branches-resource-boshrelease/releases). -->

Installation
------------

To use this bosh release, first upload it to the BOSH/bosh-lite that is running Concourse:

```
bosh upload release https://bosh.io/d/github.com/cloudfoundry-community/git-branches-resource-boshrelease
```

Next, update your Concourse deployment manifest to add the resource.

Add the `git-branches-resource` release to the list:

```yaml
releases:
  - name: concourse
    version: latest
  - name: garden-linux
    version: latest
  - name: git-branches-resource
    version: latest
```

Into the `worker` job, add the `{release: git-branches-resource, name: just_install_packages}` job template that will install the package:

```yaml
jobs:
- name: worker
  templates:
    ...
    - {release: git-branches-resource, name: just_install_packages}
```

The final change is to add the `git-branches-resource` package to the list of `additional_resource_types`:

```yaml
jobs:
- name: worker
  ...
  properties:
    groundcrew:
      additional_resource_types:
      - type: git-branches
        image: /var/vcap/packages/git-branches-resource
```

And `bosh deploy` your Concourse manifest.


Setup pipeline in Concourse
---------------------------

```
fly -t snw c -c pipeline.yml --vars-from credentials.yml git-branches-resource-boshrelease
```
