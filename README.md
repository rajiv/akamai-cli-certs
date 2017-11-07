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

    $ akamai certs help
    NAME
        akamai-certs - Manage Secure CDN certificates

    SYNOPSIS
        akamai-certs [global options] command [command options] [arguments...]

    VERSION
        0.4.0

    GLOBAL OPTIONS
        --edgerc=arg  - (default: /Users/user/.edgerc)
        --section=arg - (default: default)
        --version     - Display the program version
        --help        - Show this message

    COMMANDS
        help    - Shows a list of commands or help for one command
        list    - List certificates for a contract
        ciphers - List ciphers for all certificates on a contract
        info    - Show detailed information for an enrollment
        leaf    - Print the end-entity (leaf) certificate for an enrollment in PEM format
        chain   - Print the certificate and trust chain for an enrollment in PEM format

### Configuration

All commands support global options to specify an edgerc file and configuration section:

    $ akamai certs --edgerc='~/.edgerc-qa' --section='cps'

#### List certificates

To list certificates for a given contract:

    $ akamai certs list <contract_id>
    Fetching certificates for Contract ID Z-2QQQBB

    ID    | SANS | EXPIRES                 | CN
    ------|------|-------------------------|-------------------------------------
    90075 | 82   | 2018-10-24 23:59:59     | san-001.example.com
    90077 | 77   | 2018-06-10 23:59:59     | san-002.example.com
    90144 | 1    | 2017-11-12 23:59:59     | info.example.com
    90147 | 2    | 2018-07-19 23:59:59     | www.example.com
    90148 | 2    | 2018-07-09 23:59:59     | www-test.example.com

#### List ciphers

To list ciphers for all certificates on a given contract:

    $ akamai certs ciphers <contract_id>
    Fetching certificates for Contract ID Z-2QQQBB

    ID    | MUST_HAVE                      | PREFERRED                      | CN
    ------|--------------------------------|--------------------------------|-------------------------------------
    90075 | ak-akamai-default-2017q3       | ak-akamai-default-2017q3       | san-001.example.com
    90077 | ECDHE-RSA-AES256-GCM-SHA384... | ECDHE-RSA-AES256-GCM-SHA384... | san-002.example.com
    90144 | AES256-GCM-SHA384:AES128-GC... | AES256-GCM-SHA384:AES256-SH... | info.example.com
    90147 | ak-akamai-default-2017q3       | ak-akamai-default-2017q3       | www.example.com
    90148 | ak-akamai-default-2017q3       | ak-akamai-default-2017q3       | www-test.example.com

#### Certificate information

To get detailed information for a certificate, by enrollment ID:

    $ akamai certs info [--staging] <enrollment_id>
    Fetching production certificate details for enrollment 90075

    Network:     production
    Common Name: san-001.example.com
    Subject:     /CN=san-001.example.com
    Not Before:  2018-07-25 21:03:46 UTC
    Expires:     2018-10-24 23:59:59 UTC
    Issuer:      /C=US/O=Let's Encrypt/CN=Let's Encrypt Authority X3

This command defaults to showing information from production, and takes an optional `--staging` flag to display information from the certificate deployed on the staging network.

#### Download the end-entity (leaf) certificate for an enrollment

To print the end-entity (leaf) certificate for a deployed certificate in production, by enrollment ID:

    $ akamai certs leaf [--staging] <enrollment_id>
    Fetching production certificate for enrollment 90075

    -----BEGIN CERTIFICATE-----
    MIIFITCCBAmgAwIBAgISAzJQDgXWWcaJSElMtFP/XeQOMA04CSqGSIb3DQEBCwUA
    MEoxCzAJBgNVBAYTAlVTMRYwFAYADM9KEw1MZXQncyBFbmNyeXB0MSMwIQYDVQQD
    AApMZXQncy2FbmNyeXB0IEF1dGhvcml0eSBYMzAeFw0xNzA4MTQyMDQ5MDBaFw2z
    ...
    -----END CERTIFICATE-----

The certificate is printed in PEM format. This command defaults to showing information from production, and takes an optional `--staging` flag to display information from the certificate deployed on the staging network.

This command can be used to view the parsed certificate:

    $ akamai certs leaf <enrollment_id> | openssl x509 -noout -text

And to print the SHA-256 hash of the certificate's public key:

    $ akamai certs leaf <enrollment_id> | openssl x509 -noout -pubkey | openssl pkey -pubin -outform der | openssl dgst -sha256 -binary | openssl enc -base64

#### Download the certificate and trust chain for an enrollment

To print the certificate and trust chain for a deployed certificate, by enrollment ID:

    $ akamai certs chain [--staging] <enrollment_id>
    Fetching production certificate chain for enrollment 90075

    -----BEGIN CERTIFICATE-----
    MIIFITCCBAmgAwIBAgISAzJQDgXWWcaJSElMtFP/XeQOMA04CSqGSIb3DQEBCwUA
    MEoxCzAJBgNVBAYTAlVTMRYwFAYADM9KEw1MZXQncyBFbmNyeXB0MSMwIQYDVQQD
    AApMZXQncy2FbmNyeXB0IEF1dGhvcml0eSBYMzAeFw0xNzA4MTQyMDQ5MDBaFw2z
    ...
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
    MIIEkjCCA3qgAwIBAgIQCgFBQgAAAVOFc2oLheynCDANBgkqhkiG9w0BAQsFADA/
    MSQwIgYDVQQKExtEaWdpdGFsIFNpZ25hdHVyZSBUcnVzdCBDby4xFzAVBgNVBAMT
    DkRTVCBSb290IENBIFgzMB4XDTE2MDMxNzE2NDA0NloXDTIxMDMxNzE2NDA0Nlow
    ...
    -----END CERTIFICATE-----

The certificates are printed in PEM format, with the end-entity certificate first. This command defaults to showing information from production, and takes an optional `--staging` flag to display information from the certificate deployed on the staging network.


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
