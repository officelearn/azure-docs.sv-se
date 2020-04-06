---
title: Skapa inträngning med automatisk TLS
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du installerar och konfigurerar en NGINX-ingressstyrenhet som använder Let's Encrypt för automatisk TLS-certifikatgenerering i ett AKS-kluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: c98310bc5dc6b8f17403505cbcdd7e51355ca2b7
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668434"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Skapa en HTTPS-ingress-styrenhet på Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [NGINX-inkommande styrenhet][nginx-ingress] i ett AKS-kluster (Azure Kubernetes Service). [Cert-manager-projektet][cert-manager] används för att automatiskt generera och konfigurera [Let's][lets-encrypt] Encrypt-certifikat. Slutligen körs två program i AKS-klustret, som var och en är tillgänglig via en enda IP-adress.

Du kan också:

- [Skapa en grundläggande ingress-styrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera http-programroutningstillägget][aks-http-app-routing]
- [Skapa en ingressstyrenhet som använder en intern, privat nätverks- och IP-adress][aks-ingress-internal]
- [Skapa en ingående styrenhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingressstyrenhet som använder Let's Encrypt för att automatiskt generera TLS-certifikat med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Den här artikeln förutsätter också att du har [en anpassad domän][custom-domain] med en [DNS-zon][dns-zone] i samma resursgrupp som AKS-klustret.

I den här artikeln används Helm för att installera NGINX-ingress-styrenheten, cert-managern och en exempelwebbapp. Se till att du använder den senaste versionen av Helm. Instruktioner för uppgradering finns i [Helm-installationsdokumenten][helm-install]. Mer information om hur du konfigurerar och använder Helm finns i [Installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingående styrenhet

Om du vill skapa ingressstyrenheten använder du `helm` kommandot för att installera *nginx-ingress*. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Om du till fullo kan dra nytta av att köra repliker av ingressstyrenheten kontrollerar du att det finns mer än en nod i AKS-klustret.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i förhandsversion i AKS) bör inte köra ingressstyrenheten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingressresurserna som heter *ingress-basic*. Ange ett namnområde för din egen miljö efter behov.

> [!TIP]
> Om du vill aktivera [IP-bevarande][client-source-ip] för klientkälla för `--set controller.service.externalTrafficPolicy=Local` begäranden till behållare i klustret lägger du till i kommandot Helm install. Klientkällans IP lagras i begäranden under *X-Forwarded-For*. När du använder en ingående styrenhet med klientkälla IP-bevarande aktiverat, ssl-vidaregång kommer inte att fungera.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Under installationen skapas en offentlig Azure-IP-adress för inkommande styrenhet. Den här offentliga IP-adressen är statisk för ingressstyrens livslängd. Om du tar bort den ingående styrenheten går den offentliga IP-adresstilldelningen förlorad. Om du sedan skapar ytterligare en ingress-styrenhet tilldelas en ny offentlig IP-adress. Om du vill behålla användningen av den offentliga IP-adressen kan du i stället [skapa en ingress-styrenhet med en statisk offentlig IP-adress][aks-ingress-static-tls].

Använd kommandot om `kubectl get service` du vill hämta den offentliga IP-adressen. Det tar några minuter innan IP-adressen tilldelas tjänsten.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Inga ingressregler har skapats ännu. Om du bläddrar till den offentliga IP-adressen visas NGINX-ingress-styrenhetens standardsida 404.

## <a name="add-an-a-record-to-your-dns-zone"></a>Lägga till en A-post i DNS-zonen

Lägg till en *A-post* i DNS-zonen med den externa IP-adressen för NGINX-tjänsten med [az-network dns-post som en tilläggspost][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name '*' \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Du kan också konfigurera en FQDN för IP-adressen för ingresskontrollenheten i stället för en anpassad domän. Observera att det här exemplet är för ett Bash-skal.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Installera cert-manager

NGINX-ingresskontrollanten stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS. Den här artikeln visas med hjälp av [cert-manager][cert-manager], som ger automatisk [Kan kryptera][lets-encrypt] certifikatgenerering och hanteringsfunktioner.

Så här installerar du styrenheten för certifikathanterare:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
  jetstack/cert-manager
```

Mer information om cert-manager-konfiguration finns i [cert-manager-projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Skapa en certifikatutfärdare för kluster

Innan certifikat kan utfärdas kräver cert-manager en [utfärdare][cert-manager-issuer] eller [clusterissuer-resurs.][cert-manager-cluster-issuer] Dessa Kubernetes-resurser är identiska `Issuer` i funktionalitet, men `ClusterIssuer` fungerar i ett enda namnområde och fungerar över alla namnområden. Mer information finns i dokumentationen [för cert-manager-utfärdaren.][cert-manager-issuer]

Skapa en klusterutfärdare, till exempel `cluster-issuer.yaml`, med hjälp av följande exempelmanifest. Uppdatera e-postadressen med en giltig adress från din organisation:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
```

Använd kommandot `kubectl apply` om du vill skapa utfärdaren.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Kör demoprogram

En ingående styrenhet och en certifikathanteringslösning har konfigurerats. Nu ska vi köra två demoprogram i AKS-klustret. I det här exemplet används Helm för att distribuera två instanser av ett enkelt *Hello world-program.*

Innan du kan installera exempel helm-diagram, lägga till Azure exempel databas i din Helm-miljö.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Skapa ett demoprogram med namnet *aks-helloworld* med hjälp av *azure-samples/aks-helloworld Helm-diagrammet.*

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Skapa en andra instans av demoprogrammet *aks-helloworld-two*. Ange en ny rubrik och ett unikt tjänstnamn så att de två programmen är visuellt distinkta med *--set*.

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Skapa en ingående rutt

Båda programmen körs nu i kubernetes-klustret. Men de är konfigurerade med `ClusterIP` en tjänst av typen och är inte tillgängliga från Internet. Skapa en Kubernetes-ingressresurs för att göra dem tillgängliga för allmänheten. Ingressresursen konfigurerar de regler som dirigerar trafik till ett av de två programmen.

I följande exempel trafik till adressen *hello-world-ingress. MY_CUSTOM_DOMAIN* dirigeras till *aks-helloworld-tjänsten.* Trafik till adressen *hello-world-ingress. MY_CUSTOM_DOMAIN/hello-world-two* dirigeras till *aks-helloworld-two-tjänsten.* Trafik till *hello-world-ingress. MY_CUSTOM_DOMAIN/statisk* dirigeras till tjänsten *aks-helloworld* för statiska tillgångar.

Skapa en `hello-world-ingress.yaml` fil med namnet yaml nedan. Uppdatera *värdarna* och *värden* till det DNS-namn som du skapade i ett tidigare steg.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Skapa ingressresursen `kubectl apply` med kommandot.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Kontrollera att ett certifikatobjekt har skapats

Därefter måste en certifikatresurs skapas. Certifikatresursen definierar önskat X.509-certifikat. Mer information finns i [certifikatutfärdarcertifikat][cert-manager-certificates]. Cert-manager har automatiskt skapat ett certifikatobjekt för dig med ingress-shims, som automatiskt distribueras med cert-manager sedan v0.2.2. Mer information finns [i dokumentationen för ingress-shim][ingress-shim].

Om du vill kontrollera att certifikatet `kubectl get certificate --namespace ingress-basic` har skapats använder du kommandot och *verifierar READY* är *Sant*, vilket kan ta flera minuter.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testa ingresskonfigurationen

Öppna en webbläsare för *hello-world-ingress. MY_CUSTOM_DOMAIN* av din Kubernetes ingress controller. Observera att du omdirigeras för att använda HTTPS och certifikatet är betrott och demoprogrammet visas i webbläsaren. Lägg till *sökvägen /hello-world-two* och lägg märke till det andra demoprogrammet med den anpassade titeln visas.

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln användes Helm för att installera ingående komponenter, certifikat och exempelappar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempelnamnområdet eller de enskilda resurserna.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempelnamnområdet och alla resurser

Om du vill ta bort `kubectl delete` hela exempelnamnområdet använder du kommandot och anger namnområdesnamnet. Alla resurser i namnområdet tas bort.

```console
kubectl delete namespace ingress-basic
```

Ta sedan bort Helm-repon för AKS hello world-appen:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

Alternativt är en mer detaljerad metod att ta bort de enskilda resurser som skapats. Ta först bort klusterutfärdarresurserna:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Lista Helm-utgåvorna `helm list` med kommandot. Leta efter diagram som heter *nginx-ingress* och *aks-helloworld*, som visas i följande exempelutdata:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Ta bort utgåvorna `helm delete` med kommandot. I följande exempel tas NGINX-ingress-distributionen bort och de två exempelen AKS hello world apps.

```
$ helm uninstall aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

Ta sedan bort Helm-repon för AKS hello world-appen:

```console
helm repo remove azure-samples
```

Ta bort den ingående vägen som dirigerade trafik till exempelapparna:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Slutligen kan du ta bort själva namnområdet. Använd `kubectl delete` kommandot och ange namnområdesnamnet:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller några externa komponenter till AKS. Mer information om dessa komponenter finns på följande projektsidor:

- [Helm CLI][helm-cli]
- [NGINX-ingress-styrenhet][nginx-ingress]
- [cert-chef][cert-manager]

Du kan också:

- [Skapa en grundläggande ingress-styrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera http-programroutningstillägget][aks-http-app-routing]
- [Skapa en ingressstyrenhet som använder en intern, privat nätverks- och IP-adress][aks-ingress-internal]
- [Skapa en ingående styrenhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingressstyrenhet som använder Let's Encrypt för att automatiskt generera TLS-certifikat med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
