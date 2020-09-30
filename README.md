# adcs-submit

A helper script for certmonger integration with the Active Directory Certificate Services web pages.

## Disclaimer

This is a work-in-progress example of how I created a certmonger helper script for use against a third-party Certificate Service.  At the time
of writing, I haven't used the helper in a production environment or done extensive testing, hopefully, I'll be able to update this at a 
later time with glowing reports of stable operations.

## Requirements

adcs-submit requires the following components for correct function:

- Python 3: the effort to make it compatible with Version 2.x didn't seem worth it.
- [certsrv](https://github.com/magnuswatn/certsrv): A Python client for the Microsoft AD Certificate Services web page.
- [pyOpenSSL](https://github.com/pyca/pyopenssl): A Python wrapper around the OpenSSL library.
- [requests](https://github.com/psf/requests): A simple, yet elegant HTTP library.
- [requests_ntlm](https://github.com/requests/requests-ntlm): NTLM authentication support for Requests.

## Known limitations

- The version 1.0 script only works for certificates that are issued without Administrator intervention.  I hope to be able to support
the POLL operation soon.

## Installation

To install the helper, after installing certmonger:

1. Download adcs-submit script:

```
# curl -so /usr/libexec/certmonger/adcs-submit https://raw.githubusercontent.com/ag-wood/adcs-submit/master/adcs-submit
# chmod +x /usr/libexec/certmonger/adcs-submit
```

2. Configure adcs-submit by running:

```
# /usr/libexec/certmonger/adcs-submit  --config
Username []:    mydomain\myuser
Password []:    mypassword
Issuing CA []:  myca.domain.internal
Default Template []:    webserver
```

> **NOTE:**
>
> The above configuration creates /etc/certmonger/.adcs_config - a 'hidden' file with obfuscated credentials that is only accessible by root.   Do NOT use a domain administrator account or any privileged account as there is no way to properly secure a password that needs to run unattended.  Use an account that has been configured to allow enrolment for the specific templates required.

3. Register the helper with certmonger

The rest is described in the [certmonger documentation](https://pagure.io/certmonger/blob/master/f/doc) or in [Red Hat Documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system-level_authentication_guide/certmongerx), but for completeness, registration is achieved by running:

```
# getcert add-ca -c ADCS -e /usr/libexec/certmonger/adcs-submit
# getcert list-cas
  .
  .
  .
CA 'ADCS':
        is-default: no
        ca-type: EXTERNAL
        helper-location: /usr/libexec/certmonger/adcs-submit
```
