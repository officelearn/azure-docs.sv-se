---
title: Skapa en HTTP-ingress-kontrollant med statiska IP-adress i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och konfigurerar en ingress-kontrollanten för NGINX med en statisk offentlig IP-adress i ett kluster i Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 8b05a770c58e1d796e2658fe5a3bd5aa1808825c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470011"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Skapa en ingress-kontrollant med en statisk offentlig IP-adress i Azure Kubernetes Service (AKS)

Ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbara trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera de inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollanten och ingående regler, kan en IP-adress användas till att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar den [ingress-kontrollanten för NGINX] [ nginx-ingress] i ett kluster i Azure Kubernetes Service (AKS). Ingress-kontrollanten har konfigurerats med en statisk offentlig IP-adress. Den [certifikathanterare] [ cert-manager] projektet används för att automatiskt generera och konfigurera [vi kryptera] [ lets-encrypt] certifikat. Slutligen körs två program i AKS-kluster som är tillgänglig via en IP-adress.

Du kan också:

- [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder en egen TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder Helm för att installera NGINX ingress-kontrollant, certifikathanterare och en exempelwebbapp. Du måste ha Helm initieras i AKS-klustret och använda ett tjänstkonto för Tiller. Kontrollera att du använder den senaste versionen av Helm. Uppgradera anvisningar finns i den [Helm installera docs][helm-install]. Läs mer om att konfigurera och använda Helm [installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.41 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingress-kontrollant

Som standard skapas en ingress-kontrollanten för NGINX med en ny offentlig IP-adresstilldelning. Den här offentliga IP-adressen är endast statisk för livslängden för ingress-kontrollanten och går förlorad om kontrollanten tas bort och återskapas. Ett vanligt krav för konfiguration är att tillhandahålla ingress-kontrollanten för NGINX en befintlig statisk offentlig IP-adress. Statiska offentliga IP-adressen förblir om ingress-kontrollanten har tagits bort. Den här metoden kan du använda befintliga DNS-poster och nätverkskonfigurationer i ett konsekvent sätt i hela livscykeln för dina program.

Om du vill skapa en statisk offentlig IP-adress först hämta resursgruppens namn för AKS-kluster med den [az aks show] [ az-aks-show] kommando:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Skapa sedan en offentlig IP-adress med det *Statiska* allokering metoden med hjälp av den [az nätverket offentliga ip-skapa] [ az-network-public-ip-create] kommando. I följande exempel skapas en offentlig IP-adress med namnet *myAKSPublicIP* i AKS-kluster resursgrupp som hämtades i föregående steg:

```azurecli
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Nu distribuera den *nginx-ingress* diagram med Helm. Lägg till den `--set controller.service.loadBalancerIP` parametern och ange dina egna offentliga IP-adressen som skapades i föregående steg. För extra redundans två repliker av NGINX ingående kontrollenheterna distribueras med den `--set controller.replicaCount` parametern. Om du vill utnyttja alla fördelar med repliker av ingress-kontrollant, kontrollera att det finns fler än en nod i AKS-klustret.

> [!TIP]
> I följande exempel installerar ingress-kontrollanten och certifikat i den `kube-system` namnområde. Du kan ange ett annat namnområde för din egen miljö om så önskas. Även om AKS-klustret inte RBAC aktiverat lägger du till `--set rbac.create=false` för kommandon.

```console
helm install stable/nginx-ingress \
    --namespace kube-system \
    --set controller.service.loadBalancerIP="40.121.63.72"  \
    --set controller.replicaCount=2
```

När tjänsten för Kubernetes belastningsutjämning har skapats för ingress-kontrollanten för NGINX kan tilldelas den statiska IP-adressen som visas i följande Exempelutdata:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Inga inkommande regler har skapats ännu, så att NGINX ingående controller standard 404 visas om du bläddrar till offentliga IP-adress. Ingående regler har konfigurerats i följande steg.

## <a name="configure-a-dns-name"></a>Konfigurera ett DNS-namn

Konfigurera ett fullständigt domännamn för ingress controller IP-adress för HTTPS-certifikat ska fungera korrekt. Uppdatera följande skript med IP-adressen för ingress-kontrollanten och ett unikt namn som du vill använda för FQDN:

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Ingress-kontrollant är nu tillgänglig via det fullständiga Domännamnet.

## <a name="install-cert-manager"></a>Installera certifikathanterare

Ingress-kontrollanten för NGINX stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS. Den här artikeln visar hur du använder [certifikathanterare][cert-manager], som tillhandahåller automatiska [kan kryptera] [ lets-encrypt] certifikat generation och hanteringsfunktioner.

> [!NOTE]
> Den här artikeln används den `staging` miljö för att kryptera vi. I distributioner av produktion, använda `letsencrypt-prod` och `https://acme-v02.api.letsencrypt.org/directory` i resursdefinitionerna och när du installerar Helm-diagrammet.

Använd följande för att installera certifikathanterare controller i ett kluster med RBAC-aktiverade, `helm install` kommando. Igen, om du vill ändra `--namespace` till något annat än *kube system*:

```console
helm install stable/cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Om klustret inte är aktiverat RBAC, i stället använda följande kommando:

```console
helm install stable/cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Mer information om certifikathanterare konfigurationen finns i den [certifikathanterare projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Skapa en utfärdare för CA-kluster

Innan certifikat kan utfärdas, certifikathanterare kräver en [utfärdare] [ cert-manager-issuer] eller [ClusterIssuer] [ cert-manager-cluster-issuer] resurs. De här resurserna för Kubernetes är identiska i funktionen, men `Issuer` fungerar i ett enda namnområde och `ClusterIssuer` fungerar över alla namnområden. Mer information finns i den [certifikathanterare utfärdare] [ cert-manager-issuer] dokumentation.

Skapa en kluster-utfärdare som `cluster-issuer.yaml`, med hjälp av följande exempel manifestet. Uppdatera den e-postadressen med en giltig adress från din organisation:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Skapa utfärdaren genom att använda den `kubectl apply -f cluster-issuer.yaml` kommando.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>Skapa ett certifikatobjekt

Därefter måste du skapa en resurs för certifikatet. Certifikatresursen definierar det önskade X.509-certifikatet. Mer information finns i [certifikathanterare certifikat][cert-manager-certificates].

Skapa certifikat-resurs, till exempel `certificates.yaml`, med följande exempel manifestet. Uppdatera den *dnsNames* och *domäner* till DNS-namn som du skapade i föregående steg. Om du använder en interna ingress-kontrollant, ange det interna DNS-namnet för din tjänst.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Använd för att skapa certifikatresursen, den `kubectl apply -f certificates.yaml` kommando.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

Kontrollera att certifikatet har skapats genom att använda den `kubectl describe certificate tls-secret` kommando.

Om certifikatet har utfärdats, visas utdata som liknar följande:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

## <a name="run-demo-applications"></a>Köra demo-program

Ingress-kontrollanten och en lösning för hantering av certifikat har konfigurerats. Nu ska vi köra två demonstrera program i AKS-klustret. I det här exemplet används Helm för att distribuera två instanser av en enkel ”Hello world”-program.

Innan du kan installera exempel Helm-diagram, lägger du till lagringsplatsen Azure-exempel i miljön Helm på följande sätt:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Skapa det första demonstrationsprogrammet från ett Helm-diagram med följande kommando:

```console
helm install azure-samples/aks-helloworld
```

Nu installera en andra instans av demoprogrammet. För den andra instansen anger du ett nytt namn så att de två programmen är visuellt åtskilda. Du kan även ange ett unikt namn:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Skapa en inkommande väg

Båda programmen körs nu på ett Kubernetes-kluster, men de är konfigurerade med en tjänst av typen `ClusterIP`. Programmen är därför inte tillgänglig från internet. Skapa en Kubernetes ingress-resurs så att de blir allmänt tillgänglig. Ingress-resursen konfigurerar regler som vidarebefordrar trafik till en av de två programmen.

I följande exempel trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/` dirigeras till tjänsten med namnet `aks-helloworld`. Trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` dirigeras till den `ingress-demo` service. Uppdatera den *värdar* och *värden* till DNS-namn som du skapade i föregående steg.

Skapa en fil med namnet `hello-world-ingress.yaml` och kopiera följande YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Skapa den ingående resursen med hjälp av den `kubectl apply -f hello-world-ingress.yaml` kommando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testa ingress-konfiguration

Öppna en webbläsare till FQDN för din Kubernetes ingress-kontrollant, till exempel *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Eftersom de här exemplen använder `letsencrypt-staging`, utfärdade SSL-certifikatet inte är betrodd av webbläsaren. Godkänn varningen att fortsätta till programmet. Certifikatinformationen visar detta *Fake LE mellanliggande X1* certifikat utfärdas av vi kryptera. Den här falska certifikat anger `cert-manager` bearbetade förfrågan korrekt och tog emot ett certifikat från providern:

![Nu ska vi kryptera mellanlagrings-certifikat](media/ingress/staging-certificate.png)

När du ändrar vi kryptera att använda `prod` snarare än `staging`, ett betrott certifikat som utfärdats av vi kryptera används, enligt följande exempel:

![Nu ska vi kryptera certifikat](media/ingress/certificate.png)

Demoprogrammet visas i webbläsaren:

![Exempel på en](media/ingress/app-one.png)

Lägg nu till den */hello-world-two* sökvägen till det fullständiga Domännamnet, till exempel *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. Andra demoprogrammet med anpassade rubriken visas:

![Exempel på två](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Rensa resurser

Den här artikeln används Helm för att installera komponenter för ingress, certifikat och exempelappar. När du distribuerar ett Helm-diagram, skapas ett antal Kubernetes-resurser. Dessa resurser inkluderar poddar, distributioner och tjänster. Om du vill rensa kan du först ta bort certificate-resurser:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Nu lista Helm-versioner med den `helm list` kommando. Leta efter diagram med namnet *nginx-ingress*, *certifikathanterare*, och *aks-helloworld*, vilket visas i följande Exempelutdata:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Tue Oct 16 17:44:28 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
alliterating-peacock    1           Tue Oct 16 18:03:11 2018    DEPLOYED    cert-manager-v0.3.4     v0.3.2      kube-system
mollified-armadillo     1           Tue Oct 16 18:04:53 2018    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Tue Oct 16 18:04:56 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort versionerna med de `helm delete` kommando. I följande exempel tar bort NGINX ingående distribution och Certifikathanteraren två AKS hello world exempelapparna.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Ta sedan bort Helm-lagringsplatsen för AKS hello world-app:

```console
helm repo remove azure-samples
```

Ta bort den inkommande väg som dirigeras trafiken till exempelapparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

Slutligen ska du ta bort den statiska offentlig IP-adress som har skapats för ingress-kontrollant. Ange din *MC_* kluster resursgruppens namn som hämtades i det första steget i den här artikeln *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln ingår vissa externa komponenter till AKS. Mer information om dessa komponenter finns på följande sidor i projektet:

- [Helm CLI][helm-cli]
- [Ingress-kontrollanten för NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Du kan också:

- [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant som använder en egen TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingress-kontrollant med en dynamisk offentlig IP-adress och konfigurera kryptera vi för att automatiskt generera TLS-certifikat][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
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
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
