# Convertion of lists to markdown

## 0. Original list (what we need)
```
1.  System finds OCSP responder addresses from the global configuration.

2.  System verifies the OCSP response:

    2.1.  System verifies that the certificate identified in a response received from the OCSP responder corresponds to the one in the corresponding request.

        2.1.1  System verifies that the signature on the OSCP response is valid.

    2.2.  System verifies that the signer is currently authorized to sign the OCSP responses for the certificate authority that has issued the certificate the OCSP response was given to.

3.  System gets an OCSP response from one of the found OCSP responders.
```

## 1. Plain text (does not work as intended)

1.  System finds OCSP responder addresses from the global configuration.

2.  System verifies the OCSP response:

    2.1.  System verifies that the certificate identified in a response received from the OCSP responder corresponds to the one in the corresponding request.

        2.1.1.  System verifies that the signature on the OSCP response is valid.

    2.2.  System verifies that the signer is currently authorized to sign the OCSP responses for the certificate authority that has issued the certificate the OCSP response was given to.

3.  System gets an OCSP response from one of the found OCSP responders.

## 2. Autonumbering with full number (does not work for third level, only first level is autonumber)

1.  System finds OCSP responder addresses from the global configuration.

2.  System verifies the OCSP response:

    2.1.  System verifies that the certificate identified in a response received from the OCSP responder corresponds to the one in the corresponding request.

        2.1.1.  System verifies that the signature on the OSCP response is valid.

    2.2.  System verifies that the signer is currently authorized to sign the OCSP responses for the certificate authority that has issued the certificate the OCSP response was given to.

3.  System gets an OCSP response from one of the found OCSP responders.

## 3. Auto numbering using single number on each level (converts numbers to roman and alfabet numeration, hard to find items with full numbers like "2.1.1.")

1.  System finds OCSP responder addresses from the global configuration.

2.  System verifies the OCSP response:

    1.  System verifies that the certificate identified in a response received from the OCSP responder corresponds to the one in the corresponding request.

        1.  System verifies that the signature on the OSCP response is valid.

            1.  System verifies that the signature on the OSCP response is valid.

    2.  System verifies that the signer is currently authorized to sign the OCSP responses for the certificate authority that has issued the certificate the OCSP response was given to.

3.  System gets an OCSP response from one of the found OCSP responders.

## 4. Disabling autonumbering (no indent, needs escaping of first level numbers)

1\.  System finds OCSP responder addresses from the global configuration.

2\.  System verifies the OCSP response:

2.1.  System verifies that the certificate identified in a response received from the OCSP responder corresponds to the one in the corresponding request.

2.1.1.  System verifies that the signature on the OSCP response is valid.

2.2.  System verifies that the signer is currently authorized to sign the OCSP responses for the certificate authority that has issued the certificate the OCSP response was given to.

3\.  System gets an OCSP response from one of the found OCSP responders.

## 5. Indent with `&nbsp;` (not readable in md form and hard to produce, wraps long lines with the wrong ident)

&nbsp;&nbsp;&nbsp;&nbsp;1.  System finds OCSP responder addresses from the global configuration.

&nbsp;&nbsp;&nbsp;&nbsp;2.  System verifies the OCSP response:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.1.  System verifies that the certificate identified in a response received from the OCSP responder corresponds to the one in the corresponding request.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.1.1.  System verifies that the signature on the OSCP response is valid.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.2.  System verifies that the signer is currently authorized to sign the OCSP responses for the certificate authority that has issued the certificate the OCSP response was given to.

&nbsp;&nbsp;&nbsp;&nbsp;3.  System gets an OCSP response from one of the found OCSP responders.

## 6. Bulleted + numbered list v1 (still needs escaping, not so beautiful, but has indents and is easy to produce)

* 1\. System finds OCSP responder addresses from the global configuration.

* 2\. System verifies the OCSP response:

    * 2.1. System verifies that the certificate identified in a response received from the OCSP responder corresponds to the one in the corresponding request.

        * 2.1.1. System verifies that the signature on the OSCP response is valid.

    * 2.2. System verifies that the signer is currently authorized to sign the OCSP responses for the certificate authority that has issued the certificate the OCSP response was given to.

## 7. Bulleted + numbered list v2 (still needs escaping, not so beautiful, but has indents and is easy to produce; probly is the best solution)

1\. System finds OCSP responder addresses from the global configuration.

2\. System verifies the OCSP response:

* 2.1. System verifies that the certificate identified in a response received from the OCSP responder corresponds to the one in the corresponding request.

    * 2.1.1. System verifies that the signature on the OSCP response is valid.

* 2.2. System verifies that the signer is currently authorized to sign the OCSP responses for the certificate authority that has issued the certificate the OCSP response was given to.

3\. System gets an OCSP response from one of the found OCSP responders.

## 8. Quotation v1 (do lines look better than bullets? Gray font visibility? Bad compatibility with other markdown variants.)

> 1.  System finds OCSP responder addresses from the global configuration.

> 2.  System verifies the OCSP response:

>> 2.1.  System verifies that the certificate identified in a response received from the OCSP responder corresponds to the one in the corresponding request.

>>> 2.1.1.  System verifies that the signature on the OSCP response is valid.

>> 2.2.  System verifies that the signer is currently authorized to sign the OCSP responses for the certificate authority that has issued the certificate the OCSP response was given to.

> 3.  System gets an OCSP response from one of the found OCSP responders.

## 9. Quotation v2 (first level needs escaping)

1\.  System finds OCSP responder addresses from the global configuration.

2\.  System verifies the OCSP response:

> 2.1.  System verifies that the certificate identified in a response received from the OCSP responder corresponds to the one in the corresponding request.

>> 2.1.1.  System verifies that the signature on the OSCP response is valid.

> 2.2.  System verifies that the signer is currently authorized to sign the OCSP responses for the certificate authority that has issued the certificate the OCSP response was given to.

3\.  System gets an OCSP response from one of the found OCSP responders.
