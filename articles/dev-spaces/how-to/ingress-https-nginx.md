---
title: Använda en anpassad NGINX-ingress-styrenhet och konfigurera HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Lär dig hur du konfigurerar Azure Dev Spaces för att använda en anpassad NGINX-ingress-styrenhet och konfigurera HTTPS med den ingående styrenheten
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s
ms.openlocfilehash: 0fe9fec263b72ac06839b58fdc5b0142a724718c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155455"
---
# <a name="use-a-custom-nginx-ingress-controller-and-configure-https"></a>Använda en anpassad NGINX-ingress-styrenhet och konfigurera HTTPS

Den här artikeln visar hur du konfigurerar Azure Dev Spaces för att använda en anpassad NGINX-ingress-styrenhet. Den här artikeln visar också hur du konfigurerar den anpassade ingressstyrenheten för att använda HTTPS.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon, kan du skapa ett [kostnadsfritt konto][azure-account-create].
* [Azure CLI installerat][az-cli].
* [Aks-kluster (Azure Kubernetes Service) med Azure Dev Spaces aktiverat][qs-cli].
* [kubectl][kubectl] installerat.
* [Helm 3 installerat][helm-installed].
* [En anpassad domän][custom-domain] med en [DNS-zon][dns-zone].  Den här artikeln förutsätter att den anpassade domänen och DNS-zonen finns i samma resursgrupp som AKS-klustret, men det är möjligt att använda en anpassad domän och DNS-zon i en annan resursgrupp.

## <a name="configure-a-custom-nginx-ingress-controller"></a>Konfigurera en anpassad NGINX-ingress-styrenhet

Anslut till klustret med [kubectl][kubectl], Kommandoradsklienten Kubernetes. För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Lägg till den [officiella stabila Helm-arkivet][helm-stable-repo], som innehåller NGINX-ingress-styrenheten Helm-diagrammet.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Skapa ett Kubernetes-namnområde för NGINX-ingressstyrenheten och installera det med `helm`.

```console
kubectl create ns nginx
helm install nginx stable/nginx-ingress --namespace nginx --version 1.27.0
```

> [!NOTE]
> I exemplet ovan skapas en offentlig slutpunkt för inkommande styrenhet. Om du behöver använda en privat slutpunkt för ingångsstyrenheten i stället lägger du till *--set controller.service.annoteringarna." tjänst\\\\.beta\\.kubernetes .io/azure-load-balancer-internal"=true* parameter till kommandot helm *install.* Ett exempel:
> ```console
> helm install nginx stable/nginx-ingress --namespace nginx --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.27.0
> ```
> Den här privata slutpunkten visas i det virtuella nätverket där du AKS-kluster distribueras.

Hämta IP-adressen för tjänsten NGINX-ingressstyrenhet med [kubectl get][kubectl-get].

```console
kubectl get svc -n nginx --watch
```

Exempelutdata visar IP-adresserna för alla tjänster i *nginx-namnutrymmet.*

```console
NAME                                  TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     <pending>        80:31314/TCP,443:30521/TCP   10s
nginx-nginx-ingress-default-backend   ClusterIP      10.0.210.231   <none>           80/TCP                       10s
...
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     MY_EXTERNAL_IP   80:31314/TCP,443:30521/TCP   26s
```

Lägg till en *A-post* i DNS-zonen med den externa IP-adressen för NGINX-tjänsten med [az-network dns-post som en tilläggspost][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.nginx \
    --ipv4-address MY_EXTERNAL_IP
```

I exemplet ovan läggs en *A-post* till i *den MY_CUSTOM_DOMAIN* DNS-zonen.

I den här artikeln använder du [exempelprogrammet Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) för att demonstrera med Azure Dev Spaces. Klona programmet från GitHub och gå till programmets katalog:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Öppna [values.yaml][values-yaml] och gör följande uppdateringar:
* Ersätt alla *<-REPLACE_ME_WITH_HOST_SUFFIX>* med *nginx. MY_CUSTOM_DOMAIN* att använda domänen för *MY_CUSTOM_DOMAIN*. 
* Ersätt *kubernetes.io/ingress.class: traefik-azds # Dev Spaces-specifika* med *kubernetes.io/ingress.class: nginx # Anpassad ingress*. 

Nedan följer ett exempel `values.yaml` på en uppdaterad fil:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Spara ändringarna och stäng filen.

Skapa *utvecklingsutrymmet* med exempelprogrammet med `azds space select`.

```console
azds space select -n dev -y
```

Distribuera exempelprogrammet `helm install`med .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

I exemplet ovan distribueras exempelprogrammet till *dev-namnområdet.*

Visa url:erna för att `azds list-uris`komma åt exempelprogrammet med .

```console
azds list-uris
```

Nedanstående utdata visar exempeladresserna från `azds list-uris`.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

Navigera till *bikesharingweb-tjänsten* genom att `azds list-uris` öppna den offentliga webbadressen från kommandot. I exemplet ovan är `http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/`den offentliga URL:en för *bikesharingweb-tjänsten* .

> [!NOTE]
> Om du ser en felsida i stället för *bikesharingweb-tjänsten* kontrollerar du att du har uppdaterat **både** *kubernetes.io/ingress.class-anteckningen* och värden i filen *values.yaml.*

Använd `azds space select` kommandot för att skapa ett underordnat utrymme under *dev* och lista webbadresserna för att komma åt det underordnade utvecklingsutrymmet.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Nedanstående utdata visar exempeladresser från `azds list-uris` för att komma åt exempelprogrammet i *azureuser1-utrymmet* för underordnad utveckling.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

Navigera till *bikesharingweb-tjänsten* i *azureuser1-utrymmet* för underordnad `azds list-uris` utveckling genom att öppna den offentliga URL:en från kommandot. I exemplet ovan är `http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/`den offentliga URL:en för *bikesharingweb-tjänsten* i *azureuser1-underordnade* dev-utrymmet .

## <a name="configure-the-nginx-ingress-controller-to-use-https"></a>Konfigurera NGINX-ingressstyrenheten för att använda HTTPS

Använd [cert-manager][cert-manager] för att automatisera hanteringen av TLS-certifikatet när du konfigurerar NGINX-ingressstyrenheten för att använda HTTPS. Används `helm` för att installera *certmanager-diagrammet.*

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace nginx
kubectl label namespace nginx certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace nginx --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Skapa `letsencrypt-clusterissuer.yaml` en fil och uppdatera e-postfältet med din e-postadress.

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

> [!NOTE]
> För testning finns det också en [mellanlagringsserver][letsencrypt-staging-issuer] som du kan använda för *din ClusterIssuer*.

Används `kubectl` för `letsencrypt-clusterissuer.yaml`att tillämpa .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace nginx
```

Uppdatera [values.yaml][values-yaml] för att inkludera information om hur du använder *cert-manager* och HTTPS. Nedan följer ett exempel `values.yaml` på en uppdaterad fil:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Uppgradera exempelprogrammet `helm`med :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Navigera till exempelprogrammet i det underordnade utrymmet *dev/azureuser1* och lägg märke till att du omdirigeras för att använda HTTPS. Observera också att sidan läses in, men webbläsaren visar några fel. När du öppnar webbläsarkonsolen visas felet som relaterar till en HTTPS-sida som försöker läsa in HTTP-resurser. Ett exempel:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Lös det här felet genom att uppdatera [BikeSharingWeb/azds.yaml][azds-yaml] som liknar nedanstående:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: nginx
        cert-manager.io/cluster-issuer: letsencrypt
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      tls:
      - hosts:
        - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
        secretName: dev-bikesharingweb-secret
...
```

Uppdatera [BikeSharingWeb/package.json][package-json] med ett beroende för *url-paketet.*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Uppdatera *metoden getApiHostAsync* i [BikeSharingWeb/lib/helpers.js][helpers-js] för att använda HTTPS:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Navigera till `BikeSharingWeb` katalogen `azds up` och använd för att köra din uppdaterade *BikeSharingWeb-tjänst.*

```console
cd ../BikeSharingWeb/
azds up
```

Navigera till exempelprogrammet i det underordnade utrymmet *dev/azureuser1* och lägg märke till att du omdirigeras för att använda HTTPS utan några fel.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure Dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbete genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Teamutveckling i Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml