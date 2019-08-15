---
title: Använd en anpassad traefik ingångs kontroll och konfigurera HTTPS
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: Lär dig hur du konfigurerar Azure dev Spaces för att använda en anpassad traefik ingångs kontroll och konfigurera HTTPS med den här ingångs styrenheten
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
ms.openlocfilehash: 50908bde65b69cb475391cd30bca758dd571f114
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036948"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Använd en anpassad traefik ingångs kontroll och konfigurera HTTPS

Den här artikeln visar hur du konfigurerar Azure dev Spaces för att använda en anpassad traefik ingångs kontroll. Den här artikeln visar också hur du konfigurerar den anpassade ingångs styrenheten att använda HTTPS.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon, kan du skapa ett [kostnadsfritt konto][azure-account-create].
* [Azure CLI installerat][az-cli].
* [Azure Kubernetes service-kluster (AKS) med Azure dev Spaces aktiverat][qs-cli].
* [kubectl][kubectl] installerad.
* [Helm 2,13 eller senare installerad][helm-installed].
* [En anpassad domän][custom-domain] med en [DNS-zon][dns-zone] i samma resurs grupp som ditt AKS-kluster.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Konfigurera en anpassad traefik-kontrollant

Anslut till klustret med [kubectl][kubectl], Kubernetes kommando rads klient. För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Skapa ett Kubernetes-namnområde för traefik ingress-kontrollanten och installera den `helm`med hjälp av.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

Hämta IP-adressen för tjänsten traefik ingångs kontroll med hjälp av [kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

Exempel resultatet visar IP-adresserna för alla tjänster i *traefik* namn området.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Lägg till en *A* -post i DNS-zonen med den externa IP-adressen för traefik-tjänsten med [AZ Network DNS Record-set A Add-Record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

Exemplet ovan lägger till en *A* -post i DNS-zonen *MY_CUSTOM_DOMAIN* .

I den här artikeln använder du [exempel programmet Azure dev Spaces Bike sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) för att demonstrera användningen av Azure dev Spaces. Klona programmet från GitHub och gå till programmets katalog:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Öppna [Values. yaml][values-yaml] och Ersätt alla instanser av *< REPLACE_ME_WITH_HOST_SUFFIX >* med *traefik. MY_CUSTOM_DOMAIN* med din domän för *MY_CUSTOM_DOMAIN*. Ersätt också *Kubernetes.io/ingress.Class: traefik-azds # dev Spaces-Specific* med *Kubernetes.io/ingress.Class: Traefik # Custom ingress*. Nedan visas ett exempel på en uppdaterad `values.yaml` fil:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Spara ändringarna och Stäng filen.

Distribuera exempel programmet med `helm install`.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

Exemplet ovan distribuerar exempel programmet till *dev* -namnområdet.

Välj ett *dev* -utrymme med ditt exempel program `azds space select` med hjälp av och Visa URL: er för att `azds list-uris`komma åt exempel programmet med.

```console
azds space select -n dev
azds list-uris
```

I nedanstående utdata visas exempel-URL: `azds list-uris`er från.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navigera till *bikesharingweb* -tjänsten genom att öppna den offentliga URL: `azds list-uris` en från kommandot. I exemplet ovan är `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`den offentliga URL: en för *bikesharingweb* -tjänsten.

Använd kommandot för att skapa ett underordnat utrymme under dev och listar URL: erna för att få åtkomst till det underordnade dev-utrymmet. `azds space select`

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

I nedanstående utdata visas exempel-URL: `azds list-uris` er från för att komma åt exempel programmet i *azureuser1* -underordnat dev-utrymme.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navigera till *bikesharingweb* -tjänsten i det *azureuser1* underordnade dev-området genom att öppna den offentliga `azds list-uris` URL: en från kommandot. I exemplet ovan är `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`den offentliga URL: en för *bikesharingweb* -tjänsten i det *azureuser1* underordnade dev-utrymmet.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Konfigurera traefik ingress Controller för att använda HTTPS

Skapa en `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` fil som liknar exemplet nedan. Uppdatera *e-* postvärdet med din egen e-postadress som används för att generera certifikatet med hjälp av kryptera.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

Uppdatera *traefik* -tjänsten med `helm repo update` hjälp av och inklusive *traefik-Values. yaml-* filen som du skapade.

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

Kommandot ovan kör en ny version av traefik-tjänsten med hjälp av värdena från *traefik-Values. yaml* och tar bort den tidigare tjänsten. Traefik-tjänsten skapar också ett TLS-certifikat med hjälp av att kryptera och börja omdirigera webb trafik för att använda HTTPS.

> [!NOTE]
> Det kan ta några minuter innan den nya versionen av traefik-tjänsten har startats. Du kan kontrol lera förloppet `kubectl get pods --namespace traefik --watch`med.

Navigera till exempel programmet i det underordnade området *dev/azureuser1* och Observera att du omdirigeras till att använda https. Observera också att sidan läses in, men webbläsaren visar vissa fel. Om du öppnar webb läsar konsolen visas felet relaterar till en HTTPS-sida vid försök att läsa in HTTP-resurser. Exempel:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Åtgärda felet genom att uppdatera [BikeSharingWeb/azds. yaml][azds-yaml] för att använda *traefik* för *Kubernetes.io/ingress.class* och din anpassade domän för *$ (hostSuffix)* . Exempel:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Uppdatera [BikeSharingWeb/Package. JSON][package-json] med ett beroende för *URL* -paketet.

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Uppdatera *getApiHostAsync* -metoden i [BikeSharingWeb/Pages/helpers. js][helpers-js] för att använda https:

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

Navigera till `BikeSharingWeb` katalogen och Använd `azds up` för att köra din uppdaterade BikeSharingWeb-tjänst.

```console
cd BikeSharingWeb/
azds up
```

Navigera till exempel programmet i det underordnade området *dev/azureuser1* och Observera att du omdirigeras till att använda https utan fel.

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbets utveckling genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Grupp utveckling i Azure dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://github.com/helm/helm/blob/master/docs/install.md
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml