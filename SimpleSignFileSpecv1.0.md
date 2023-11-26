Simple Sign File Spec v1.0

Schema: `https://openwebstandard.org/simple-sign-file/v1`


| Field               | Type        | Comment                                      |
|---------------------|-------------|----------------------------------------------|
| schema              | String      | `Required` Constant, Value is Schema         |
| version             | String      | `Required` Constant, `v1`                    |
| filename            | String      | `Optional` Filename                          |
| digest              | String      | `Required` Digest, e.g. `sha256-*`           |
| timestamp           | Long        | `Required` Unix Epoch Time in millis         |
| attributes          | String      | `Optional` Attributes, for future us         |
| comment             | String      | `Optional` Comment                           |
| signatures          | Signature[] | `Required` Signatures, currently one         |

| Field               | Type          | Comment                                                                          |
|---------------------|---------------|----------------------------------------------------------------------------------|
| algorithm           | String        | `Required` Signature algorithm, e.g. `SHA256withECDSA`                           | 
| signature           | String        | `Required` Signature in base64                                                   |
| certificates        | Certificate[] | `Required` X509 Certificate in PEM, Singing Cert\[, Intermediate Cert], RootCert |


| Tag | Filed           | Comment                           |
|-----|-----------------|-----------------------------------|
| 0   | Filename        | -                                 |
| 1   | Timestamp       | Type `i64`, serialize in BE order |
| 2   | Attributes      | -                                 |
| 3   | Comment         | -                                 |
| 254 | Digest          | -                                 |

Signature content (Tobe Signed):
```
TLV(*) = Tag in u8 || Length in u16 || UTF-8 Bytes of *
TobeSigned = "v1" || TLV(filename) || TLV(timestamp) || TLV(attributes) || TLV(comment) || TLV(digest)
```


Signature file `*.simple-sig` sample:
```json
{
  "schema": "https://openwebstandard.org/simple-sign-file/v1",
  "version": "v1",
  "filename": "example.zip",
  "digest": "sha256-HEX(SHA256(filename-content))",
  "timestamp": 1700964163340,
  "attributes": "****",
  "comment": "***",
  "signatures": [
    {
      "algorithm": "SHA256withECDSA",
      "signature": "Base64(Sign(SHA256("v1"||TLV(filename)||TLV(timestamp)||TLV(attributes)||TLV(comment)||TLV(digest))))",
      "certificates": ["-----BEGIN CERTIFICATE-----\n*****\n-----END CERTIFICATE-----", ...]
    }
  ]
}
```

Sample:
```json
{
  "schema": "https://openwebstandard.org/simple-sign-file/v1",
  "filename": "example.txt",
  "digest": "sha256-381115cd053205800af207e66229b337f3b63ad92b11f607a9032f186a437fd2",
  "timestamp": 1700984401942,
  "attributes": "attr1,attr2",
  "comment": "this is a sample file sig",
  "signatures": [
    {
      "algorithm": "SHA256withECDSA",
      "signature": "MGQCMCr9DzgzPY9+nJNG6bY8MSOOzUVbWLygDWO5LjQQlulvJiQuEusYztnuKK8hb/R5egIwBFj2smo1AT0lSa33/iX0Wo4v9GP2dAsSKgDE0ebKQkTy2cwnqci8acCf7JzK2v+N",
      "certificates": [
        "-----BEGIN CERTIFICATE-----\nMIIB+DCCAX6gAwIBAgIVALe/Gyof7wdOqA5Hw+BfxLKsKctUMAoGCCqGSM49BAMC\nMCQxIjAgBgNVBAMMGUhhdHRlciBFQyBJbnRlcm1lZGlhdGUgQ0EwHhcNMjMxMDMw\nMDAwMDAwWhcNMzMxMDMwMDAwMDAwWjAcMRowGAYDVQQDDBFIYXR0ZXIgU2lnbmlu\nZyBDQTB2MBAGByqGSM49AgEGBSuBBAAiA2IABNA3bQZm7Fz93A7wjR4TZnfZ/yZD\nJDA/bMOyU0R1Xj2nyp164jWut7Y7k+wEUQObOqb6mtml3YK24kDSc75+vTBAzSsz\nJWVpS4XgYGZ1u41L7Ns7un56uZocnuP2liFcSqN4MHYwDgYDVR0PAQH/BAQDAgWg\nMAwGA1UdEwEB/wQCMAAwFgYDVR0lAQH/BAwwCgYIKwYBBQUHAwMwHQYDVR0OBBYE\nFP9cz42+U6fP5YZXpJLM/TschPmkMB8GA1UdIwQYMBaAFKWHFKtlvWFHtpitgmmc\nMK8CJAY8MAoGCCqGSM49BAMCA2gAMGUCMQCjs/EbpNpOa6LoKRqEu6AdKaKA4mlN\n2xIVU6cIViwv4Lj0K/nmPHnAnPOu4yiLr1UCMFKcIfdZBn5mQ9DoT6Rbefy4SH6P\ndrQlvOTIBRQh9kiQoA2clTG1d8DFc0PpRF9pXA==\n-----END CERTIFICATE-----",
        "-----BEGIN CERTIFICATE-----\nMIIB5DCCAWugAwIBAgIUPQMQohzxKUPB5kNVqucFbULevIMwCgYIKoZIzj0EAwIw\nHDEaMBgGA1UEAwwRSGF0dGVyIEVDIFJvb3QgQ0EwHhcNMjMxMDI5MDAwMDAwWhcN\nMzMxMDI5MDAwMDAwWjAkMSIwIAYDVQQDDBlIYXR0ZXIgRUMgSW50ZXJtZWRpYXRl\nIENBMHYwEAYHKoZIzj0CAQYFK4EEACIDYgAEImblRzI8dv8ea7y8kR2X0ZM56BF3\ntjjzjIJ7zmXaMO3DU9JbCdXZJoogLytTuKA5hmSPD0aXbnzQ89mZ7KWVA2qI2cjH\nwN5u+KtQM2oPvhH0nhMVFifcM7IeP6quihqko2YwZDAOBgNVHQ8BAf8EBAMCAQYw\nEgYDVR0TAQH/BAgwBgEB/wIBADAdBgNVHQ4EFgQUpYcUq2W9YUe2mK2CaZwwrwIk\nBjwwHwYDVR0jBBgwFoAUeYIe16r9vuTceUDXG0CAbI9Pp+owCgYIKoZIzj0EAwID\nZwAwZAIwd9dqszZM7lKcf+LtDc0VkbNlBZVIS0jjZfUn6nUXOizfjNM3UzLcMKVO\nTQP1pb2XAjAeISWnbTaxxQPCG/6mzfMw9CfqPS6ECuHfrXyfAw45AI7CpUArDhZW\nZKV6vlnkzHc=\n-----END CERTIFICATE-----",
        "-----BEGIN CERTIFICATE-----\nMIIB3DCCAWKgAwIBAgIUBmlMvQ8s4PNWa2dFxhZH6gpVEpUwCgYIKoZIzj0EAwIw\nHDEaMBgGA1UEAwwRSGF0dGVyIEVDIFJvb3QgQ0EwIBcNMjMxMDI5MDAwMDAwWhgP\nMjA2MzEwMjkwMDAwMDBaMBwxGjAYBgNVBAMMEUhhdHRlciBFQyBSb290IENBMHYw\nEAYHKoZIzj0CAQYFK4EEACIDYgAE3hLba+pjLyUPUiXO6DcSM0326f4yuziZiKNU\nrBKfgJ7GZ6Yydlh2Ke33vyhoBcvTQlHP4ocWGwm0RdJ0Wz+99tkxegv8VskEqIEo\nCU/U78w6DbcWvzQAAKfXUfGjjNpBo2MwYTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0T\nAQH/BAUwAwEB/zAdBgNVHQ4EFgQUeYIe16r9vuTceUDXG0CAbI9Pp+owHwYDVR0j\nBBgwFoAUeYIe16r9vuTceUDXG0CAbI9Pp+owCgYIKoZIzj0EAwIDaAAwZQIxANym\nCiIqwtBXwcvn887Z9dnrdWXDEpJanID2nvwqa57ACIhTTu3d/UzFdOM6GWDR8AIw\nbC9qIy+izBeFPfbggsz6U9nF5++LbtRHBFQ2InWoI4GZd074SGPcYRalMV3AUZ5m\n-----END CERTIFICATE-----"
      ]
    }
  ]
}
```
