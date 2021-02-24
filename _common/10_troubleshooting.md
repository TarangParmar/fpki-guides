---
layout: default 
title: Troubleshooting
collection: common
permalink: common/troubleshooting/
---

{% include alert-success.html content="Common issues and troubleshooting solutions related to the Federal PKI Root CA migration are listed below.  If you need help or have any questions, email us at fpkirootupdate@gsa.gov for help." %} 
 
## Windows Troubleshooting
- [How come my workstation isn't chaining to FCPCA G2?](#how-come-my-workstation-isnt-chaining-to-fcpca-g2)
- [How come I can't login to an application or website with my PIV after migrating to FCPCA G2?](#how-come-i-cant-login-to-an-application-or-website-with-my-piv-after-migrating-to-fcpca-g2)
- [How can I verify network configurations aren't preventing certificate validation?](#how-can-i-verify-network-configurations-arent-preventing-certificate-validation)
- [Is there any logging I can enable to help verify what's going on?](#is-there-any-logging-i-can-enable-to-help-verify-whats-going-on)
- [Are there any useful commands that I should be familiar with?](#are-there-any-useful-commands-that-i-should-be-familiar-with)

## macOS Troubleshooting
- [How come my workstation isn't chaining to FCPCA G2?](#how-come-my-workstation-isnt-chaining-to-fcpca-g2)
- [How come I can't login to an application or website with my PIV after migrating to FCPCA G2?](#how-come-i-cant-login-to-an-application-or-website-with-my-piv-after-migrating-to-fcpca-g2)
- [How can I verify network configurations aren't preventing certificate validation?](#how-can-i-verify-network-configurations-arent-preventing-certificate-validation)
- [Are there any useful commands that I should be familiar with?](#are-there-any-useful-commands-that-i-should-be-familiar-with)

## iOS Troubleshooting
- [Why am I seeing TLS certificate errors?](#why-am-i-seeing-tls-certificate-errors)


## Solutions

### Windows

#### How come my workstation isn't chaining to FCPCA G2?

In some cases, even after distributing the Federal Common Policy CA G2 as a Trusted Root CA, Microsoft-based systems may still attempt to build a path to the Federal Common Policy CA.  In the event that the Federal Common Policy CA has been distrusted, that may result in errors as pictured below:

![validation errors]({{site.baseurl}}/img/error-distribute-intermediates.png) 

**Recommended Steps:**
1. Verify the distribution of the Federal Common Policy CA G2 as a [Trusted Root CA Certificate]({{site.baseurl}}/common/distribute-os/)
2. Verify the [distrust of the Federal Common Policy CA]({{site.baseurl}}/common/migrate/#distrust-the-fcpca-g1)
3. Distribute the [intermediate CA certificates issued by the Federal Common Policy CA G2]({{site.baseurl}}/common/certificates/#certificates-issued-by-the-federal-common-policy-ca-g2)

If you're still having problems, reach out to us at fpkirootupdate@gsa.gov.



#### How come I can't login to an application or website with my PIV after migrating to FCPCA G2?

It's possbile the application or website you are attempting to authenticate to has not yet distributed the Federal Common Policy CA G2 certificate, or the new intermediate CA certificates issued by the Federal Common Policy CA G2.  Depending on how your system is configured, this may result in Transport Layer Security (TLS) client authentication errors.

The steps below should **only** be performed by System Administrators and require [OpenSSL](https://www.openssl.org/){:target="_blank"}{:rel="noopener noreferrer"}.

**Recommended Steps**:
1. Run the following OpenSSL command, replacing the application or website's fully qualified domain name (FQDN) with the placeholder below:<br>
```openssl s_client -connect [FQDN].gov:443```

     For example, to test https://piv.treasury.gov, you would execute:<br>
     ```openssl s_client -connect piv.treasury.gov:443```
     
2. Review the output for a line that reads "Acceptable client certificate CA names".  This presents a listing of the certificates the website is willing to accept a certificate from for authentication. 

3. Verify an entry exists for the Federal Common Policy CA G2.  This will appear as the following:
     ```/C=US/O=U.S. Government/OU=FPKI/CN=Federal Common Policy CA G2```
     
4. Verify an extry exists for each CA certificate on the chain between the CA that issued your certificate, and the Federal Common Policy CA G2.

**Note:** the presence of the names of intermediate CA certificates issued from the Federal Common Policy CA G2 in the list of "Acceptable client certificate CA names" does not guarantee the right certificates have been added.  You may need to work with the website administrator to verify the correct intermediate CA certificates issued from the Federal Common Policy CA G2 have been added. 

#### How can I verify network configurations aren't preventing certificate validation?

TBD

#### Is there any logging I can enable to help verify what's going on?

TBD

#### Are there any useful commands that I should be familiar with?

TBD


### macOS

#### How come my workstation isn't chaining to FCPCA G2?

TBD

#### How come I can't login to an application or website with my PIV after migrating to FCPCA G2?

TBD

#### How can I verify network configurations aren't preventing certificate validation?

TBD

#### Are there any useful commands that I should be familiar with?

TBD


### iOS

#### Why am I seeing TLS certificate errors?

TBD

