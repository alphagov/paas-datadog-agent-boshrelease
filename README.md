# BOSH Release for datadog-agent

This release only supports Ubuntu Trusty stemcells - it uses the python-dev apt package.
Packaging requires Internet connectivity - Python packages are downloaded to the install target.

## Usage

To use this bosh release, first upload it to your bosh:

```
bosh target BOSH_HOST
git clone https://github.com/onemedical/datadog-agent-boshrelease.git
cd datadog-agent-boshrelease
bosh upload release
```

Configure the agent on each of your instance groups:

```
instance_groups:
- name: myservice
  jobs:
  - name: datadog-agent
    release: datadog-agent
    properties:
      api_key: abc123...
      tags:
        service: myservice
```

### Collecting AWS tags

To collect AWS tags, set up a Datadog role following the [Datadog instructions](http://docs.datadoghq.com/integrations/aws/).
Instead of creating a "Role for Cross-Account Access," create an "Amazon EC2" role.
Use that role name in your cloud config and enable EC2 tag collection on the instance group:

```
vm_extensions:
- name: datadog
  cloud_properties:
    iam_instance_profile: datadog-instance-profile
```

```
instance_groups:
- name: myservice
  vm_extensions: [datadog]
  jobs:
  - name: datadog-agent
    release: datadog-agent
    properties:
      api_key: abc123...
      collect_ec2_tags: true
      tags:
        service: myservice
```

## Development

As a developer of this release, create new releases and upload them:

```
bosh create release --force && bosh -n upload release
```