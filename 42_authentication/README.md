# Authentication
**Authentication** konusuyla ilgili dosyalara buradan erişebilirsiniz.



**Key ve CSR oluşturma**
```
$ openssl genrsa -out balaban.key 2048  # private key oluşturur. 2048 --> dosya boyutu

$ openssl req -new -key balaban.key -out balaban.csr -subj "/CN=balaban1432@gmail.com/O=DevTeam"

req   --> CSR oluşturma isteği
-new  --> yeni bir CSR olsun diyorum
-key  --> bu key i kullan diyorum
-out  --> sonucu bu dosyaya yaz diyorum
-subj --> CN ile belirtilen değer kullanıcı adı olacak, O ile belirlenen değerler de bu kullanıcının hangi gruplara üye olacağını belirler.

```

**CertificateSigningRequest oluşturma**

```
$ cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: balaban
spec:
  groups:
  - system:authenticated
  request: $(cat balaban.csr | base64 | tr -d "\n")
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
EOF
```

**CSR onaylama ve crt'yi alma**

```
$ kubectl get csr

$ kubectl certificate approve balaban

$ kubectl get csr balaban -o yaml

$ kubectl get csr balaban -o jsonpath='{.status.certificate}' | base64 -d >> balaban.crt 
```

**kubectl config ayarları**

```
$ kubectl config set-credentials balaban1432@gmail.com --client-certificate=balaban.crt --client-key=balaban.key

balaban1432@gmail.com --> req ile oluşturduğum kullanıcı adı

$ kubectl config set-context balaban-context --cluster=minikube --user=balaban1432@gmail.com

balaban-context --> oluşturmak istediğim context adı

$ kubectl config use-context balaban-context
```