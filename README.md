# CA

openssl genrsa -out ca-key.pem 2048

openssl req -new -key ca-key.pem -x509 \
  -days 1000 \
  -out ca.pem \
  -subj "/C=AU/ST=NSW/L=Sydney/O=DSys/O=ASIOCN=ASIOCA"

# CSR 
**MUST** use SANs https://frasertweedale.github.io/blog-redhat/posts/2017-07-11-cn-deprecation.html !!!
openssl req -nodes -newkey rsa:2048 -keyout wildcard.lan.asio.key -out wildcard.lan.asio.csr -subj "/C=AU/ST=NSW/L=Sydney/OU=ASIO/CN=*.lan.asio" -addext "subjectAltName = DNS:*.lan.asio"

# Cert
openssl x509 -req -in wildcard.lan.asio.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out wildcard.lan.asio.pem -days 1000 -extfile <(printf "subjectAltName=DNS:*.lan.asio")

kubectl delete -n istio-ingress secrets tls-wildcard

kubectl create -n istio-ingress secret tls tls-wildcard   --key=/home/geoff/ca/wildcard.lan.asio.key   --cert=/home/geoff/ca/wildcard.lan.asio.pem

# artifactory
openssl req -nodes -newkey rsa:2048 -keyout artifactory-jcr.lan.asio.key -out artifactory-jcr.lan.asio.csr -subj "/C=AU/ST=NSW/L=Sydney/OU=ASIO/CN=artifactory-jcr.lan.asio" -addext "subjectAltName = DNS:artifactory-jcr.lan.asio"

openssl x509 -req -in artifactory-jcr.lan.asio.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out artifactory-jcr.lan.asio.pem -days 1000 -extfile <(printf "subjectAltName=DNS:artifactory-jcr.lan.asio")

kubectl delete -n artifactory-jcr secrets tls-artifactory-jcr

kubectl create -n artifactory-jcr secret tls tls-artifactory-jcr   --key=/home/geoff/ca/artifactory-jcr.lan.asio.key   --cert=/home/geoff/ca/artifactory-jcr.lan.asio.pem

