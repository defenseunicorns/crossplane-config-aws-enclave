# crossplane-config-aws-enclave

This Crossplane Configuration provides a Composite Resource Claim called "Enclave" that can be used to provision a production-ready Virtual Private Network in AWS.

It will create:

- A VPC
- 3 public subnets
- 3 private subnets
- A DBSubnetGroup consisting of the 3 private subnets
- An Internet Gateway
- An Elastic IP Address (for the NAT Gateway)
- A NAT Gateway that the private subnets use for internet egress
- A Route Table for the public subnets that allows incoming traffic using the Internet Gateway
- 3 Route Tables (one for each private subnet) that allow outgoing internet traffic using the NAT Gateway while barring incoming internet traffic

## Usage

1. Run a Kubernetes cluster, install crossplane, and install the Crossplane CLI using the instructions [here](https://crossplane.io/docs/v1.9/getting-started/install-configure.html). Don't use the `getting-started-with-aws` configuration.
1. Install this configuration. Use the [packages](https://github.com/defenseunicorns/crossplane-config-aws-enclave/pkgs/container/crossplane-config-aws-enclave) page to find the latest version.

    ```shell
    kubectl crossplane install configuration ghcr.io/defenseunicorns/crossplane-config-aws-enclave:<version>
    ```

1. Apply your AWS credentials using the instructions [here](https://crossplane.io/docs/v1.9/getting-started/install-configure.html#get-aws-account-keyfile) under the sections "Get AWS Account Keyfile", "Create a Provider Secret", and "Configure the Provider"

1. Provision an enclave using `kubectl apply -f <filename>`. Here's an example configuration:

    ```yaml
    apiVersion: infra.bigbang.dev/v1alpha1
    kind: Enclave
    metadata:
      name: my-enclave
      namespace: default
    spec:
      parameters:
        enclaveName: my-enclave
        region: us-east-1
        availabilityZone1: us-east-1a
        availabilityZone2: us-east-1b
        availabilityZone3: us-east-1c
      compositionSelector:
        matchLabels:
          provider: aws
    ```