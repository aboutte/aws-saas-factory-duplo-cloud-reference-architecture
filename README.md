# AWS SaaS Factory and DuploCloud Reference Architecture

- [AWS SaaS Factory and DuploCloud Reference Architecture](#aws-saas-factory-and-duplocloud-reference-architecture)
  - [Summary](#summary)
  - [How to Deploy](#how-to-deploy)
    - [Application Build](#application-build)
    - [Terraform](#terraform)
      - [Terraform Configuration](#terraform-configuration)
      - [Sample Scenarios to Deploy](#sample-scenarios-to-deploy)
        - [Single VPC, multiple tenants](#single-vpc-multiple-tenants)
        - [VPC per tenant](#vpc-per-tenant)
  - [Architecture Diagrams](#architecture-diagrams)
  - [Access to a DuploCloud Platform](#access-to-a-duplocloud-platform)
  - [Support](#support)
  - [Security](#security)
  - [License](#license)
  - [TODO](#todo)

## Summary

This project represents a reference architecture for deploying a multi tenant SaaS solution on AWS that leverages DuploCloud to provide the isoluation between tenants.  The project includes both a sample SaaS application and Infrastrucure as Code (IaC) to creates the required AWS resources to host the SaaS application.

## How to Deploy

### Application Build

The sample application in the application directory has already been built and published to a public AWS ECR registry.

### Terraform

#### Terraform Configuration

The IaC solution included in this project is Terraform.  Start in the `iac` directory.

The following environment variables will need to be set to configure the DuploCloud Terraform Provider:

> Note: See section "Access to a DuploCloud Platform" for details on how to get values for these environment variables.
> Note: If you are running the terraform interactively from your worksattion you can skip setting `duplo_token`, this value will be interactively retrieved using your browser.

```bash
export duplo_token=""
export duplo_host="https://example.duplocloud.net/"
```

#### Sample Scenarios to Deploy

See the Architecture Diagrams section for examples of customer tenancy patterns that this project can deploy.

##### Single VPC, multiple tenants

```bash
./scripts/apply.sh prod-common-usw-01 admin-infra -var cidr_bit=2

./scripts/apply.sh customera admin-tenant -var infrastructure_name="prod-common-usw-01"
./scripts/apply.sh customera aws-services
./scripts/apply.sh customera app

./scripts/apply.sh customerb admin-tenant -var infrastructure_name="prod-common-usw-01"
./scripts/apply.sh customerb aws-services
./scripts/apply.sh customerb app
```

##### VPC per tenant

```bash
./scripts/apply.sh prod-customera admin-infra -var cidr_bit=3
./scripts/apply.sh customera admin-tenant -var infrastructure_name="prod-customera"
./scripts/apply.sh customera aws-services
./scripts/apply.sh customera app

./scripts/apply.sh prod-customerb admin-infra -var cidr_bit=4
./scripts/apply.sh customerb admin-tenant -var infrastructure_name="prod-customerb"
./scripts/apply.sh customerb aws-services
./scripts/apply.sh customerb app
```

## Architecture Diagrams

The automation apart of this project supports four models of isolating customer tenants:

1. Application logic
2. Kubernetes namespace, Security Group, IAM Role, KMS Key, SSH Key
3. VPC
4. AWS Account

![Application provides tenancy](./images/1%20-%20Application%20Provides%20Tenancy.png)

![DuploCloud Tenant provides tenancy](./images/2%20-%20DuploCloud%20Tenant.png)

![DuploCloud Infrastructure provides tenancy](./images/3%20-%20DuploCloud%20Infrastructure.png)

![AWS Account + Duplocloud provides tenancy](./images/4%20-%20Cloud%20Account.png)

![DuploCloud supports hybrid combinations of tenancy](./images/5%20-%20Hybrid%20Tenancy%20Model.png)

## Access to a DuploCloud Platform

To get access to a DuploCloud platform environment for testing out the automation provided as a part of this project reach out to DuploCloud [here](https://lp.duplocloud.com/aws/saas).

## Support

Reach out to [DuploCloud](https://lp.duplocloud.com/aws/saas) for support.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

## TODO

- [ ] create pipeline for building, testing, pushing application docker image to Duplo created ECR
- [ ] create pipeline for running terraform plan, apply, destroy
- [ ] README improvements
  - [ ] Provide time estimations for each section
  - [ ] Add some clarification around the sample commands.  For example, if this used in a shared environment the names will need to be unique
  - [ ] Provide note about expeocted output when running tf apply.  "Workspace 'example name' doesn't exist." is an expected output
