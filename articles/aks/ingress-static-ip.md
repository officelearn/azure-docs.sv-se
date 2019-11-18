---
title: Skapa en HTTP-ingångs kontroll med en statisk IP-adress i Azure Kubernetes service (AKS)
description: Lär dig hur du installerar och konfigurerar en NGINX ingress Controller med en statisk offentlig IP-adress i ett Azure Kubernetes service-kluster (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 9d53f8eff53a875a7a37d1ee1986e94405a24485
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144961"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Skapa en ingångs kontroll enhet med en statisk offentlig IP-adress i Azure Kubernetes service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [nginx ingress-kontrollanten][nginx-ingress] i ett Azure Kubernetes service-kluster (AKS). Ingångs styrenheten är konfigurerad med en statisk offentlig IP-adress. [Cert Manager-][cert-manager] projektet används för att automatiskt generera och konfigurera att [kryptera][lets-encrypt] certifikat. Slutligen körs två program i AKS-klustret, som var och en är tillgänglig över en enskild IP-adress.

Du kan också:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Den här artikeln använder Helm för att installera NGINX ingress-kontrollant, cert Manager och en webbapp. Du måste ha Helm initierat i ditt AKS-kluster och med ett tjänst konto för till gången. Kontrol lera att du använder den senaste versionen av Helm. Anvisningar om hur du uppgraderar finns i [installations dokument för Helm][helm-install]. Mer information om hur du konfigurerar och använder Helm finns i [installera program med Helm i Azure Kubernetes service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI-version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingångs kontroll enhet

Som standard skapas en NGINX ingångs kontroll med en ny offentlig IP-adresstilldelning. Den här offentliga IP-adressen är bara statisk för livs cykeln för ingångs styrenheten och försvinner om styrenheten tas bort och återskapas. Ett vanligt konfigurations krav är att tillhandahålla NGINX ingress-kontrollanten en befintlig statisk offentlig IP-adress. Den statiska offentliga IP-adressen är kvar om ingångs kontroll enheten tas bort. Med den här metoden kan du använda befintliga DNS-poster och nätverkskonfigurationer på ett konsekvent sätt under hela programmets livs cykel.

Om du behöver skapa en statisk offentlig IP-adress måste du först hämta resurs grupp namnet för AKS-klustret med kommandot [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Skapa sedan en offentlig IP-adress med metoden för *statisk* allokering med kommandot [AZ Network Public-IP Create][az-network-public-ip-create] . I följande exempel skapas en offentlig IP-adress med namnet *myAKSPublicIP* i kluster resurs gruppen AKS som hämtades i föregående steg:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Distribuera nu *nginx-ingress-* diagrammet med Helm. Lägg till parametern `--set controller.service.loadBalancerIP` och ange din egen offentliga IP-adress som skapades i föregående steg. För ytterligare redundans distribueras två repliker av NGINX-ingångs styrenheterna med parametern `--set controller.replicaCount`. Se till att det finns fler än en nod i ditt AKS-kluster för att få full nytta av att köra repliker av ingångs styrenheten.

Ingångs kontroll enheten måste också schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i för hands version i AKS) behöver inte köra ingångs styrenheten. En Node-selektor anges med parametern `--set nodeSelector` för att instruera Kubernetes Scheduler att köra NGINX-ingångs styrenheten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för de ingress-resurser som heter *ingress-Basic*. Ange ett namn område för din egen miljö efter behov. Om ditt AKS-kluster inte är RBAC-aktiverat lägger du till `--set rbac.create=false` i Helm-kommandona.

> [!TIP]
> Om du vill aktivera [IP-konservering för klient källa][client-source-ip] för begär anden till behållare i klustret lägger du till `--set controller.service.externalTrafficPolicy=Local` i Helm install-kommandot. Klientens käll-IP lagras i begär ande huvudet under *X-forwarded – for*. När du använder en ingångs kontroll för att aktivera IP-konservering för klient källa fungerar inte SSL-vidarekoppling.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

När belastnings Utjämnings tjänsten för Kubernetes skapas för NGINX-ingångs styrenheten, tilldelas den statiska IP-adressen, som visas i följande exempel:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Inga ingångs regler har skapats ännu. därför visas sidan NGINX ingress Controller standard 404 om du bläddrar till den offentliga IP-adressen. Ingress-regler konfigureras i följande steg.

## <a name="configure-a-dns-name"></a>Konfigurera ett DNS-namn

För att HTTPS-certifikaten ska fungera korrekt konfigurerar du ett fullständigt domän namn för IP-adressen för ingångs enheten. Uppdatera följande skript med IP-adressen för din ingångs kontroll och ett unikt namn som du vill använda för FQDN:

```azurecli-interactive
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

Ingångs styrenheten är nu tillgänglig via FQDN.

## <a name="install-cert-manager"></a>Installera cert-Manager

NGINX ingångs styrenheten stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS. Den här artikeln visar hur du använder [cert Manager][cert-manager], som [ger automatiska funktioner][lets-encrypt] för att skapa och hantera certifikat.

> [!NOTE]
> I den här artikeln används `staging`s miljön för att kryptera. I produktions distributioner använder du `letsencrypt-prod` och `https://acme-v02.api.letsencrypt.org/directory` i resurs definitionerna och när du installerar Helm-diagrammet.

Om du vill installera certifikat hanterarens kontrollant i ett RBAC-aktiverat kluster, använder du följande `helm install` kommando:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.11/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.11.0 \
  jetstack/cert-manager
```

Mer information om cert Manager-konfiguration finns i [cert-Manager-projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Skapa en certifikat utfärdare för kluster utfärdare

Innan certifikaten kan utfärdas kräver certifikat hanteraren en [utfärdare][cert-manager-issuer] eller en [ClusterIssuer][cert-manager-cluster-issuer] -resurs. Dessa Kubernetes-resurser är identiska i funktioner, men `Issuer` fungerar i ett enda namn område och `ClusterIssuer` fungerar över alla namn områden. Mer information finns i dokumentationen om [cert Manager-utfärdaren][cert-manager-issuer] .

Skapa en kluster utfärdare, till exempel `cluster-issuer.yaml`, med hjälp av följande exempel manifest. Uppdatera e-postadressen med en giltig adress från din organisation:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Skapa utfärdaren genom att använda kommandot `kubectl apply -f cluster-issuer.yaml`.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Köra demo program

En ingångs kontroll och en certifikat hanterings lösning har kon figurer ATS. Nu ska vi köra två demo program i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt "Hello World"-program.

Innan du kan installera exempel Helm-diagrammen lägger du till Azure samples-lagringsplatsen i din Helm-miljö på följande sätt:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Skapa det första demonstrations programmet från ett Helm-diagram med följande kommando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Installera nu en andra instans av demo programmet. För den andra instansen anger du en ny rubrik så att de två programmen är visuellt åtskilda. Du anger också ett unikt tjänst namn:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Skapa en ingress-väg

Båda programmen körs nu på ditt Kubernetes-kluster, men de har kon figurer ATS med en tjänst av typen `ClusterIP`. Därför är programmen inte tillgängliga från Internet. Skapa en Kubernetes ingress-resurs för att göra dem offentligt tillgängliga. I ingress-resursen konfigureras de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras trafik till adress `https://demo-aks-ingress.eastus.cloudapp.azure.com/` till tjänsten med namnet `aks-helloworld`. Trafik till adress `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` dirigeras till `ingress-demo`s tjänsten. Uppdatera *värdarna* och *värden* till det DNS-namn som du skapade i föregående steg.

Skapa en fil med namnet `hello-world-ingress.yaml` och kopiera i följande exempel YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Skapa den inkommande resursen med hjälp av kommandot `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Skapa ett certifikat objekt

Därefter måste du skapa en certifikat resurs. Certifikat resursen definierar det önskade X. 509-certifikatet. Mer information finns i certifikat för certifikat [hanterare][cert-manager-certificates].

CERT Manager har troligt vis skapat ett certifikat objekt automatiskt för dig med hjälp av ingress-shim, som automatiskt distribueras med cert Manager sedan v 0.2.2. Mer information finns i dokumentationen om [ingress-shim][ingress-shim].

Kontrol lera att certifikatet har skapats genom att använda kommandot `kubectl describe certificate tls-secret --namespace ingress-basic`.

Om certifikatet har utfärdats visas utdata som liknar följande:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Om du behöver skapa ytterligare en certifikat resurs kan du göra det med följande exempel manifest. Uppdatera *dnsNames* och *domänerna* till det DNS-namn som du skapade i föregående steg. Om du använder en intern ingångs kontroll anger du det interna DNS-namnet för din tjänst.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
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

Skapa certifikat resursen med kommandot `kubectl apply -f certificates.yaml`.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testa ingress-konfigurationen

Öppna en webbläsare till FQDN för din Kubernetes ingångs kontroll, till exempel *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Som de här exemplen använder `letsencrypt-staging`är det utfärdade SSL-certifikatet inte betrott av webbläsaren. Godkänn varningen om du vill fortsätta till ditt program. Certifikat informationen visar att det *mellanliggande x1* -certifikatet för falska användare utfärdas av vi krypterar. Detta falska certifikat visar `cert-manager` bearbetade begäran korrekt och tog emot ett certifikat från providern:

![Låt oss kryptera mellanlagrings certifikat](media/ingress/staging-certificate.png)

När du ändrar vad som ska krypteras för att använda `prod` i stället för `staging`, används ett betrott certifikat som utfärdats av "kryptera", som du ser i följande exempel:

![Låt oss kryptera certifikat](media/ingress/certificate.png)

Demo programmet visas i webbläsaren:

![Exempel på program ett](media/ingress/app-one.png)

Lägg nu till sökvägen till */Hello-World-Two* i FQDN, till exempel *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* . Det andra demonstrations programmet med den anpassade rubriken visas:

![Program exempel två](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln används Helm för att installera ingångs komponenter, certifikat och exempel appar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. De här resurserna omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempel namnområdet eller enskilda resurser.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempel namn området och alla resurser

Om du vill ta bort hela exempel namnområdet använder du kommandot `kubectl delete` och anger namn på namn området. Alla resurser i namn området tas bort.

```console
kubectl delete namespace ingress-basic
```

Ta sedan bort Helm-lagrings platsen för appen AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

Alternativt är en mer detaljerad metod att ta bort de enskilda resurserna som skapats. Ta först bort certifikat resurserna:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Visar nu Helm-versionerna med kommandot `helm list`. Leta efter diagram med namnet *nginx – ingress*, *cert Manager*och *AKS-HelloWorld*, som du ser i följande exempel utdata:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Wed Mar  6 23:16:00 2019    DEPLOYED    nginx-ingress-1.3.1   0.22.0        kube-system
alliterating-peacock    1           Wed Mar  6 23:17:37 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
mollified-armadillo     1           Wed Mar  6 23:26:04 2019    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Wed Mar  6 23:26:07 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort utgåvorna med kommandot `helm delete`. I följande exempel tar vi bort NGINX ingress Deployment, Certificate Manager och de två exempel AKS Hello World-apparna.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Ta sedan bort Helm-lagrings platsen för appen AKS Hello World:

```console
helm repo remove azure-samples
```

Ta bort ingångs vägen som riktar sig mot trafik till exempel apparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

Ta bort det egna namn området. Använd kommandot `kubectl delete` och ange namn på namn området:

```console
kubectl delete namespace ingress-basic
```

Slutligen tar du bort den statiska offentliga IP-adressen som skapats för ingångs styrenheten. Ange namnet på din *MC_* kluster resurs grupp som hämtades i det första steget i den här artikeln, till exempel *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln ingår några externa komponenter i AKS. Mer information om dessa komponenter finns i följande projekt sidor:

- [Helm CLI][helm-cli]
- [NGINX ingress-styrenhet][nginx-ingress]
- [cert-manager][cert-manager]

Du kan också:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingångs kontroll enhet med en dynamisk offentlig IP-adress och konfigurera låt oss kryptera för att automatiskt generera TLS-certifikat][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

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
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
