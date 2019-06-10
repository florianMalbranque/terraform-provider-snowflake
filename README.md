# Terraform Provider: Snowflake

----

**Please note**: If you believe you have found a security issue, _please responsibly disclose_ by contacting us at [security@chanzuckerberg.com](mailto:security@chanzuckerberg.com).

----

[![Join the chat at https://gitter.im/chanzuckerberg/terraform-provider-snowflake](https://badges.gitter.im/chanzuckerberg/terraform-provider-snowflake.svg)](https://gitter.im/chanzuckerberg/terraform-provider-snowflake?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) [![Build Status](https://travis-ci.com/chanzuckerberg/terraform-provider-snowflake.svg?branch=master)](https://travis-ci.com/chanzuckerberg/terraform-provider-snowflake) [![codecov](https://codecov.io/gh/chanzuckerberg/terraform-provider-snowflake/branch/master/graph/badge.svg)](https://codecov.io/gh/chanzuckerberg/terraform-provider-snowflake)

This is a terraform provider plugin for managing [Snowflake](http://snowflakedb.com) accounts.

## Install

The easiest way is to run this command:

```shell
curl https://raw.githubusercontent.com/chanzuckerberg/terraform-provider-snowflake/master/download.sh | bash -s -- -b $HOME/.terraform.d/plugins
```

It runs a script generated by [godownloader](https://github.com/goreleaser/godownloader) which installs into the proper directory for terraform (~/.terraform.d/plugins).

You can also just download a binary from our [releases](https://github.com/chanzuckerberg/terraform-provider-snowflake/releases) and follow the [Terraform directions for installing 3rd party plugins](https://www.terraform.io/docs/configuration/providers.html#third-party-plugins).

TODO fogg config

## Authentication

We currently only support username + password auth and suggest that you only do so via environment variables. So a config something like–

```hcl
provider "snowflake" {
  account = "..."
  role    = "..."
  region  = "..."
}
```

and

```shell
export SNOWFLAKE_USER='...'
export SNOWFLAKE_PASSWORD='...'
terraform ...
```

## Resources

We support managing a subset of snowflakedb resources, with a focus on access control and management.

You can see a number of examples [here](examples).

<!-- START -->

### snowflake_database

#### properties

|            NAME             |  TYPE  | DESCRIPTION | OPTIONAL | REQUIRED  | COMPUTED | DEFAULT |
|-----------------------------|--------|-------------|----------|-----------|----------|---------|
| comment                     | string |             | true     | false     | false    | ""      |
| data_retention_time_in_days | int    |             | true     | false     | true     | <nil>   |
| name                        | string |             | false    | true      | false    | <nil>   |

### snowflake_role

#### properties

|  NAME   |  TYPE  | DESCRIPTION | OPTIONAL | REQUIRED  | COMPUTED | DEFAULT |
|---------|--------|-------------|----------|-----------|----------|---------|
| comment | string |             | true     | false     | false    | <nil>   |
| name    | string |             | false    | true      | false    | <nil>   |

### snowflake_role_grants

#### properties

|   NAME    |  TYPE  |              DESCRIPTION              | OPTIONAL | REQUIRED  | COMPUTED | DEFAULT |
|-----------|--------|---------------------------------------|----------|-----------|----------|---------|
| role_name | string | The name of the role we are granting. | false    | true      | false    | <nil>   |
| roles     | set    | Grants role to this specified role.   | true     | false     | false    | <nil>   |
| users     | set    | Grants role to this specified user.   | true     | false     | false    | <nil>   |

### snowflake_share

#### properties

|  NAME   |  TYPE  |                                              DESCRIPTION                                              | OPTIONAL | REQUIRED  | COMPUTED | DEFAULT |
|---------|--------|-------------------------------------------------------------------------------------------------------|----------|-----------|----------|---------|
| comment | string | Specifies a comment for the managed account.                                                          | true     | false     | false    | <nil>   |
| name    | string | Specifies the identifier for the share; must be unique for the account in which the share is created. | false    | true      | false    | <nil>   |

### snowflake_user

#### properties

|        NAME        |  TYPE  |                                                                                                        DESCRIPTION                                                                                                         | OPTIONAL | REQUIRED  | COMPUTED | DEFAULT |
|--------------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-----------|----------|---------|
| comment            | string |                                                                                                                                                                                                                            | true     | false     | false    | <nil>   |
| default_namespace  | string | Specifies the namespace (database only or database and schema) that is active by default for the user’s session upon login.                                                                                                | true     | false     | false    | <nil>   |
| default_role       | string | Specifies the role that is active by default for the user’s session upon login.                                                                                                                                            | true     | false     | true     | <nil>   |
| default_warehouse  | string | Specifies the virtual warehouse that is active by default for the user’s session upon login.                                                                                                                               | true     | false     | false    | <nil>   |
| disabled           | bool   |                                                                                                                                                                                                                            | true     | false     | true     | <nil>   |
| has_rsa_public_key | bool   | Will be true if user as an RSA key set.                                                                                                                                                                                    | false    | false     | true     | <nil>   |
| login_name         | string | The name users use to log in. If not supplied, snowflake will use name instead.                                                                                                                                            | true     | false     | true     | <nil>   |
| name               | string | Name of the user. Note that if you do not supply login_name this will be used as login_name. [doc](https://docs.snowflake.net/manuals/sql-reference/sql/create-user.html#required-parameters)                              | false    | true      | false    | <nil>   |
| password           | string | **WARNING:** this will put the password in the terraform state file. Use carefully.                                                                                                                                        | true     | false     | false    | <nil>   |
| rsa_public_key     | string | Specifies the user’s RSA public key; used for key-pair authentication. Must be on 1 line without header and trailer.                                                                                                       | true     | false     | false    | <nil>   |
| rsa_public_key_2   | string | Specifies the user’s second RSA public key; used to rotate the public and private keys for key-pair authentication based on an expiration schedule set by your organization. Must be on 1 line without header and trailer. | true     | false     | false    | <nil>   |

### snowflake_warehouse

#### properties

|      NAME      |  TYPE  | DESCRIPTION | OPTIONAL | REQUIRED  | COMPUTED | DEFAULT |
|----------------|--------|-------------|----------|-----------|----------|---------|
| comment        | string |             | true     | false     | false    | ""      |
| name           | string |             | false    | true      | false    | <nil>   |
| warehouse_size | string |             | true     | false     | true     | <nil>   |
<!-- END -->

## Development

To do development you need Go installed, this repo cloned and that's about it. It has not been tested on Windows, so if you find problems let us know.

If you want to build and test the provider localling there is a make target `make install-tf` that will build the provider binary and install it in a location that terraform can find.

### Testing

For the Terraform resources, there are 3 levels of testing - internal, unit and acceptance tests.

The 'internal' tests are run in the `github.com/chanzuckerberg/terraform-provider-snowflake/pkg/resources` package so that they can test functions that are not exported. These tests are intended to be limited to unit tests for simple functions.

The 'unit' tests are run in  `github.com/chanzuckerberg/terraform-provider-snowflake/pkg/resources_test`, so they only have access to the exported methods of `resources`. These tests exercise the CRUD methods that on the terraform resources. Note that all tests here make use of database mocking and are run locally. This means the tests are fast, but are liable to be wrong in suble ways (since the mocks are unlikely to be perfect).

You can run these first two sets of tests with `make test`.

The 'acceptance' tests run the full stack, creating, modifying and destroying resources in a live snowflake account. To run them you need a snowflake account and the proper environment variables set- SNOWFLAKE_ACCOUNT, SNOWFLAKE_USER, SNOWFLAKE_PASSWORD, SNOWFLAKE_ROLE. These tests are slower but have higher fidelity.

To run all tests, including the acceptance tests, run `make test-acceptance`.

Note that we also run all tests in our [Travis-CI account](https://travis-ci.com/chanzuckerberg/terraform-provider-snowflake).
