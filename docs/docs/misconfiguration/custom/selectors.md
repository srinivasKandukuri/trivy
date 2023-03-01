# Input Selectors

## Overview
Sometimes you might want to limit a certain policy to only be run on certain resources. This can be
achieved with input selectors.

## Use case
For instance, if you have a custom policy that you only want to be evaluated if a certain resource type is being scanned.
In such a case you could utilize input selectors to limit its evaluation on only those resources.

!!! example
    ```
        # METADATA
        # title: "RDS Publicly Accessible"
        # description: "Ensures RDS instances are not launched into the public cloud."
        # custom:
        #   input:
        #     selector:
        #     - type: cloud
        #       subtypes:
        #         - provider: aws
        #           service: rds
        package builtin.aws.rds.aws0999

        deny[res] {
        instance := input.aws.rds.instances[_]
        instance.publicaccess.value
        res := result.new("Instance has Public Access enabled", instance.publicaccess)
    ```

Observe the following `subtypes` defined:
```yaml
        #       subtypes:
        #         - provider: aws
        #           service: rds
```

They will ensure that the policy is only run when the input to such a policy contains an `RDS` instance. 

## Enabling selectors and subtypes
Currently, the following selectors are supported:

1. Cloud `type: cloud`
2. Kubernetes `type: kubernetes`
3. Dockerfile `type: dockerfile`


The following selectors also support the corresponding subtypes:

| Type                     | Subtype fields required | Example                         |
|--------------------------|-------------------------|---------------------------------|
| Cloud (AWS, Azure, etc.) | `provider`, `service`   | `provider: aws`, `service: rds` |


You can also define more than one subtype if your policy applies to multiple resources, in this case the policy
will be evaluated for either of the subtypes.

Currently, subtypes are only supported with Cloud resources. We plan to add support for all Trivy misconfiguration (Kubernetes and Dockerfiles) types shortly.

## Default behaviour
If no subtypes or selectors are specified, the policy will be evaluated regardless of input.