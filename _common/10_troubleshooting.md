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

In some cases, even after distributing the Federal Common Policy CA G2 as a Trusted Root CA, Microsoft-based systems may still attempt to build a path to the Federal Common Policy CA.  If the Federal Common Policy CA has been distrusted, that may result in errors as pictured below:

![validation errors]({{site.baseurl}}/img/error-distribute-intermediates.png) 

**Recommended Steps:**
1. Verify the distribution of the Federal Common Policy CA G2 as a [Trusted Root CA Certificate]({{site.baseurl}}/common/distribute-os/)
2. Verify the [distrust of the Federal Common Policy CA]({{site.baseurl}}/common/migrate/#distrust-the-fcpca-g1)
3. Distribute the [intermediate CA certificates issued by the Federal Common Policy CA G2]({{site.baseurl}}/common/certificates/#certificates-issued-by-the-federal-common-policy-ca-g2)

If you're still having problems, reach out to us at fpkirootupdate@gsa.gov.



#### How come I can't login to an application or website with my PIV after migrating to FCPCA G2?

It's possible the application or website you are attempting to authenticate to has not yet distributed the Federal Common Policy CA G2 certificate or the new intermediate CA certificates issued by the Federal Common Policy CA G2.  Depending on how your system is configured, this may result in Transport Layer Security (TLS) client authentication errors.

The steps below should **only** be performed by System Administrators and require [OpenSSL](https://www.openssl.org/){:target="_blank"}{:rel="noopener noreferrer"}.

**Recommended Steps**:
1. Run the following OpenSSL command, replacing the placeholder below with the application or website's fully qualified domain name (FQDN): ```openssl s_client -connect [FQDN].gov:443```

     For example, to test https://piv.treasury.gov, you would execute: ```openssl s_client -connect piv.treasury.gov:443```
     
2. Review the output for a line that reads "Acceptable client certificate CA names."  This presents a listing of the certificates the website is willing to accept a certificate from for authentication. 

3. Verify an entry exists for the Federal Common Policy CA G2.  This will appear as the following: ```/C=US/O=U.S. Government/OU=FPKI/CN=Federal Common Policy CA G2```
     
4. Verify an entry exists for each CA certificate on the chain between the CA that issued your certificate and the Federal Common Policy CA G2.

**Note:** the presence of the names of intermediate CA certificates issued from the Federal Common Policy CA G2 in the list of "Acceptable client certificate CA names" does not guarantee the right certificates have been added.  You may need to work with the website administrator to verify the correct intermediate CA certificates issued from the Federal Common Policy CA G2 have been added. 

#### How can I verify network configurations aren't preventing certificate validation?

Certificates contain extensions that tell us more information about their intended use, status, the CA that issued them, and more.  When certificates are being validated, two particular extensions are very important.

- Authority Information Access (AIA)
- Certificate Revocation List (CRL) Distribution Point (DP)

These extensions contain Hypertext Transfer Protocol (HTTP) pointers that our operating systems will use to support certificate path discovery and validation.  If local network configurations are configured to block access to these HTTP pointers, validation may fail.  Use the following steps to confirm network configurations are not preventing access to these important extension pointers.

**Recommended Steps:**
1. Export the certificate you'd like to analyze to a known location on your file system (e.g., C:\Test\PIV-Authentication.cer)

2. Start -> Run -> cmd.exe

3. Run the following command to open the CertUtil Graphical User Interface:  ```certutil -URL "http://"```

4. Click "Select" and browse the file location of the certificate you'd like to analyze.

5. Select the "Certs (from AIA)" radio button and click "Retrieve."  Verify at least one entry returns a status of "Verified".

6. Select the "CRLs (from CDP)" radio button and click "Retrieve."  Verify at least one entry returns a status of "Verified".

7. Optional: If the certificate contains an Online Certificate Status Protocol pointer within the AIA extension, select the "OCSP (from AIA)" radio button and click "Retrieve." verify at least one entry returns a status of "Verified."   

**Note:** Work with your network teams to identify if a firewall configuration is blocking traffic for any tests above that do not return a status of "Verified."


#### Is there any logging I can enable to help verify what's going on?

Microsoft systems include a PKI troubleshooting diagnostic tool availabe through the Event Viewer. This feature provides administrators with an ability to troubleshoot PKI problems by collecting detailed information about certificate validation, trust store operations, and signature verification. Follow the steps below to enable Microsoft the diagnostic tool.

**Recommended Steps:**
1. Start -> Run -> eventvwr.msc

2. Browse to "Application and Services Logs" -> "Microsoft" -> "Windows" -> "CAPI 2"

3. Right-click on "Operational" and select “Enable Log”. This will enable CAPI2 Diagnostics logging.

Note: Review failures presented in the log to learn more about specific reason certificate validation is not successful.  It may be useful to filter the log based on "Level" (Error or Information) or Event ID. Event IDs 10 and 11 are associated with certificate chain building. Event IDs 40 and 41 are associated with certificate revocation. 

#### Are there any useful commands that I should be familiar with?

TBD


### macOS

#### How come my workstation isn't chaining to FCPCA G2?

The most common cause of path building errors on macOS is because the full certificate chain is not distributed to a KeyChain (typically, the Login or System KeyChains).  Review "Are there any useful commands that I should be familiar with?", below, for how to detect certificate trust issues. 

#### How come I can't login to an application or website with my PIV after migrating to FCPCA G2?

It's possible the application or website you are attempting to authenticate to has not yet distributed the Federal Common Policy CA G2 certificate or the new intermediate CA certificates issued by the Federal Common Policy CA G2.  Depending on how your system is configured, this may result in Transport Layer Security (TLS) client authentication errors.

The steps below should **only** be performed by System Administrators and require [OpenSSL](https://www.openssl.org/){:target="_blank"}{:rel="noopener noreferrer"}.

**Recommended Steps**:
1. Run the following OpenSSL command, replacing the placeholder below with the application or website's fully qualified domain name (FQDN):<br><br>
```openssl s_client -connect [FQDN].gov:443```

     For example, to test https://piv.treasury.gov, you would execute:<br><br>
     ```openssl s_client -connect piv.treasury.gov:443```
     
2. Review the output for a line that reads "Acceptable client certificate CA names."  This presents a listing of the certificates the website is willing to accept a certificate from for authentication. 

3. Verify an entry exists for the Federal Common Policy CA G2.  This will appear as the following:<br><br>
     ```/C=US/O=U.S. Government/OU=FPKI/CN=Federal Common Policy CA G2```
     
4. Verify an entry exists for each CA certificate on the chain between the CA that issued your certificate and the Federal Common Policy CA G2.

**Note:** the presence of the names of intermediate CA certificates issued from the Federal Common Policy CA G2 in the list of "Acceptable client certificate CA names" does not guarantee the right certificates have been added.  You may need to work with the website administrator to verify the correct intermediate CA certificates issued from the Federal Common Policy CA G2 have been added. 

#### How can I verify network configurations aren't preventing certificate validation?

TBD

#### Are there any useful commands that I should be familiar with?

security verify-cert -c [PATH] -v


### iOS

#### Why am I seeing TLS certificate errors?

TBD

