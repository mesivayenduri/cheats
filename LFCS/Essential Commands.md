## Generating Private Key and CSR
```bash
openssl genrsa -out privatekey.key 4096
openssl req -new -key privatekey.key -out csr_request.csr
```

## Verify the CSR
```bash
openssl req -text -noout -verify -in request.csr
```


## Generating X509 Certifiate
```bash
openssl x509 -req -days 365 -in csr_request.csr -signkey privatekey.key -out certificate.crt
```

## Verify Certificate
```bash
openssl x509 -in certificate.crt -text -noout
```