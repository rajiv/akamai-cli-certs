# Akamai CLI: certs

A plugin for the [Akamai CLI](https://github.com/akamai/cli) to perform certificate operations for the Akamai Secure CDN.

https://github.com/rajiv/akamai-cli-certs

## Install

First, install the [Akamai CLI](https://github.com/akamai/cli). Next, setup `.edgerc` as detailed in the [AkamaiOPEN-edgegrid-ruby](https://github.com/akamai/AkamaiOPEN-edgegrid-ruby) gem instructions. Then install this plugin:

    $ akamai install rajiv/akamai-cli-certs

## Updating

To update to the latest version:

    $ akamai update certs

## Usage

All commands support global options to specify an edgerc file and configuration section:

    $ akamai certs --edgerc='~/.edgerc-qa' --section='cps'

#### List certificates

To list certificates for a given contract:

    $ akamai certs list <contract_id>

#### List ciphers

To list ciphers for all certificates on a given contract:

    $ akamai certs ciphers <contract_id>

#### Certificate information

To get detailed information for a certificate, by enrollment ID:

    $ akamai certs info <enrollment_id>

#### Download the end-entity (leaf) certificate for an enrollment

To print the end-entity (leaf) certificate for a deployed certificate in production, by enrollment ID:

    $ akamai certs leaf <enrollment_id>

The certificate is printed in PEM format. This command can be used to view the parsed certificate:

    $ akamai certs leaf <enrollment_id> | openssl x509 -noout -text

And to print the SHA-256 hash of the certificate's public key:

    $ akamai certs leaf <enrollment_id> | openssl x509 -noout -pubkey | openssl pkey -pubin -outform der | openssl dgst -sha256 -binary | openssl enc -base64

#### Download the certificate and trust chain for an enrollment

To print the certificate and trust chain for a deployed certificate in production, by enrollment ID:

    $ akamai certs chain <enrollment_id>

The certificates are printed in PEM format, with the end-entity certificate first.


## Authors

Authored by [Rajiv Aaron Manglani](https://www.rajivmanglani.com/).

## License

Copyright 2017 Akamai Technologies, Inc. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
