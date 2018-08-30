# terraform-godaddy
[Terraform](https://www.terraform.io/) plugin for managing domain records

[ ![Codeship Status for n3integration/terraform-godaddy](https://app.codeship.com/projects/29e8c490-8b5d-0134-914d-3e63d62140d1/status?branch=master)](https://app.codeship.com/projects/184616)

<dl>
  <dt>Terraform v0.11.x</dt>
  <dd>https://github.com/n3integration/terraform-godaddy/releases/tag/v1.6.1</dd>
  <dt>Terraform v0.10.x</dt>
  <dd>https://github.com/n3integration/terraform-godaddy/releases/tag/v1.5.0</dd>
  <dt>Terraform v0.9.x</dt>
  <dd>https://github.com/n3integration/terraform-godaddy/releases/tag/v1.3.0</dd>
  <dt>Terraform v0.8.x</dt>
  <dd>https://github.com/n3integration/terraform-godaddy/releases/tag/v1.2.3</dd>
  <dt>Terraform v0.7.x</dt>
  <dd>https://github.com/n3integration/terraform-godaddy/releases/tag/v1.0.0</dd>
<dl>

## Installation

```bash
bash <(curl -s https://raw.githubusercontent.com/n3integration/terraform-godaddy/master/install.sh)
```

## API Key
In order to leverage the GoDaddy APIs, an [API key](https://developer.godaddy.com/keys/) is required. The key pair can be optionally stored in environment variables.

```bash
export GODADDY_API_KEY=abc
export GODADDY_API_SECRET=123
```

## Provider

If `key` and `secret` aren't provided under the `godaddy` `provider`, they are expected to be exposed as environment variables: `GODADDY_API_KEY` and `GODADDY_API_SECRET`.

```terraform
provider "godaddy" {
  key = "abc"
  secret = "123"
}
```

## Domain Record Resource
A `godaddy_domain_record` resource requires a `domain`. If the domain is not registered under the account that owns the key, an optional `customer` number can be specified.
Additionally, one or more `record` instances are required. For each `record`, the `name`, `type`, and `data` attributes are required. `MX` records can optionally specify `priority` or will default to `0`. Address and NameServer records can be
defined using shorthand-notation as `addresses = [""]` or `nameservers = [""]`, respectively unless you need to override the default time-to-live (3600). The available record
types include:

* A
* AAAA
* CNAME
* MX
* NS
* SOA
* TXT

```terraform
resource "godaddy_domain_record" "gd-fancy-domain" {
  domain   = "fancy-domain.com"
  customer = "1234"                         // required if provider key does not belong to customer

  record {
    name = "www"
    type = "CNAME"
    data = "fancy.github.io"
    ttl = 3600
  }

  record {
    name = "@"
    type = "MX"
    data = "aspmx.l.google.com."
    ttl = 600
    priority = 1
  }

  addresses   = ["192.168.1.2", "192.168.1.3"]
  nameservers = ["ns7.domains.com", "ns6.domains.com"]
}
```

### Additional Information
If your zone contains existing data, please ensure that your Terraform resource configuration includes all existing records, otherwise they will be removed.

This plugin also supports Terraform's [import](https://www.terraform.io/docs/import/usage.html) feature. This will at least allow you to determine the changes introduced
through `terraform plan` and update the resource configuration accordingly to preserve existing data. The supplied resource `id` to the `terraform import` command should
be the name of the domain that you would like to import. Although this is currently a manual workaround, this plugin will be updated when Terraform includes support for
fully automated imports.

## License

Copyright 2017 n3integration@gmail.com

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
