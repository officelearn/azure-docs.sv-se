---
title: Skapa en HTTP-ingress-styrenhet med en statisk IP-adress i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och konfigurerar en NGINX-ingress-styrenhet med en statisk offentlig IP-adress i ett AKS-kluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 3e79bbe76a751097acd5c9d3c42dbd4020b6866b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617278"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Skapa en ingress-styrenhet med en statisk offentlig IP-adress i Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [NGINX-inkommande styrenhet][nginx-ingress] i ett AKS-kluster (Azure Kubernetes Service). Den ingående styrenheten är konfigurerad med en statisk offentlig IP-adress. [Cert-manager-projektet][cert-manager] används för att automatiskt generera och konfigurera [Let's][lets-encrypt] Encrypt-certifikat. Slutligen körs två program i AKS-klustret, som var och en är tillgänglig via en enda IP-adress.

Du kan också:

- [Skapa en grundläggande ingress-styrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera http-programroutningstillägget][aks-http-app-routing]
- [Skapa en ingående styrenhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingressstyrenhet som använder Let's Encrypt för att automatiskt generera TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

I den här artikeln används Helm för att installera NGINX-ingress-styrenheten, cert-managern och en exempelwebbapp. Se till att du använder den senaste versionen av Helm. Instruktioner för uppgradering finns i [Helm-installationsdokumenten][helm-install]. Mer information om hur du konfigurerar och använder Helm finns i [Installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingående styrenhet

Som standard skapas en NGINX-ingress-styrenhet med en ny offentlig IP-adresstilldelning. Den här offentliga IP-adressen är endast statisk för ingressstyrens livslängd och går förlorad om styrenheten tas bort och återskapas. Ett vanligt konfigurationskrav är att ge NGINX-ingressstyrenheten en befintlig statisk offentlig IP-adress. Den statiska offentliga IP-adressen kvarstår om ingress-styrenheten tas bort. Med den här metoden kan du använda befintliga DNS-poster och nätverkskonfigurationer på ett konsekvent sätt under programmens hela livscykel.

Om du behöver skapa en statisk offentlig IP-adress hämtar du först resursgruppsnamnet för AKS-klustret med kommandot [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Skapa sedan en offentlig IP-adress med den *statiska* allokeringsmetoden med kommandot [az network public-ip create.][az-network-public-ip-create] I följande exempel skapas en offentlig IP-adress med namnet *myAKSPublicIP* i AKS-klusterresursgruppen som erhölls i föregående steg:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Distribuera nu *nginx-ingress-diagrammet* med Helm. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Om du till fullo kan dra nytta av att köra repliker av ingressstyrenheten kontrollerar du att det finns mer än en nod i AKS-klustret.

Du måste skicka ytterligare två parametrar till Helm-versionen så att ingressstyrenheten blir medveten om både den statiska IP-adressen för den belastningsutjämnare som ska tilldelas tjänsten ingress controller och om dns-namnetiketten som tillämpas på den offentliga IP-adressresursen. För att HTTPS-certifikaten ska fungera korrekt används en DNS-namnetikett för att konfigurera ett FQDN för IP-adressen för ingressstyrenheten.

1. Lägg `--set controller.service.loadBalancerIP` till parametern. Ange din egen offentliga IP-adress som skapades i föregående steg.
1. Lägg `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` till parametern. Ange en DNS-namnetikett som ska användas på den offentliga IP-adress som skapades i föregående steg.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i förhandsversion i AKS) bör inte köra ingressstyrenheten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingressresurserna som heter *ingress-basic*. Ange ett namnområde för din egen miljö efter behov. Om AKS-klustret inte är `--set rbac.create=false` RBAC aktiverat lägger du till i Helm-kommandona.

> [!TIP]
> Om du vill aktivera [IP-bevarande][client-source-ip] för klientkälla för `--set controller.service.externalTrafficPolicy=Local` begäranden till behållare i klustret lägger du till i kommandot Helm install. Klientkällans IP lagras i begäranden under *X-Forwarded-For*. När du använder en ingående styrenhet med klientkälla IP-bevarande aktiverat, ssl-vidaregång kommer inte att fungera.

Uppdatera följande skript med **IP-adressen** för din ingångskontrollant och ett **unikt namn** som du vill använda för FQDN-prefixet:

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="demo-aks-ingress"
```

När tjänsten Kubernetes belastningsutjämnare skapas för NGINX-ingressstyrenheten tilldelas din statiska IP-adress, vilket visas i följande exempelutdata:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Inga ingressregler har skapats ännu, så NGINX-ingress-styrenhetens standardsida 404 visas om du bläddrar till den offentliga IP-adressen. Regler för ingående konfigureras i följande steg.

Du kan kontrollera att DNS-namnetiketten har tillämpats genom att fråga FQDN på den offentliga IP-adressen enligt följande:

```azurecli-interactive
#!/bin/bash
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query $("[?name=='myAKSPublicIP'].[dnsSettings.fqdn]") -o tsv
```

Den ingående styrenheten är nu tillgänglig via IP-adressen eller FQDN.

## <a name="install-cert-manager"></a>Installera cert-manager

NGINX-ingresskontrollanten stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS. Den här artikeln visas med hjälp av [cert-manager][cert-manager], som ger automatisk [Kan kryptera][lets-encrypt] certifikatgenerering och hanteringsfunktioner.

> [!NOTE]
> I den `staging` här artikeln används miljön för Let's Encrypt. I produktionsdistributioner `letsencrypt-prod` använder `https://acme-v02.api.letsencrypt.org/directory` du och i resursdefinitionerna och när helm-diagrammet installeras.

Om du vill installera styrenheten för certifikathanterare i ett `helm install` RBAC-aktiverat kluster använder du följande kommando:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the cert-manager namespace to disable resource validation
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
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
```

Använd kommandot `kubectl apply -f cluster-issuer.yaml` om du vill skapa utfärdaren.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Kör demoprogram

En ingående styrenhet och en certifikathanteringslösning har konfigurerats. Nu ska vi köra två demoprogram i AKS-klustret. I det här exemplet används Helm för att distribuera två instanser av ett enkelt Hello world-program.

Innan du kan installera exempeldiagrammen lägger du till Azure-exempeldatabasen i Helm-miljön på följande sätt:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Skapa det första demoprogrammet från ett Helm-diagram med följande kommando:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Nu installera en andra instans av demoprogrammet. För den andra instansen anger du en ny rubrik så att de två programmen är visuellt distinkta. Du anger också ett unikt tjänstnamn:

```console
helm install aks-helloworld-2 azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Skapa en ingående rutt

Båda programmen körs nu i Kubernetes-klustret, men de `ClusterIP`är konfigurerade med en tjänst av typen . Därför är programmen inte tillgängliga från internet. Skapa en Kubernetes-ingressresurs för att göra dem tillgängliga för allmänheten. Ingressresursen konfigurerar de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras `https://demo-aks-ingress.eastus.cloudapp.azure.com/` trafiken till adressen till `aks-helloworld`tjänsten . Trafiken till `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` adressen dirigeras `ingress-demo` till tjänsten. Uppdatera *värdarna* och *värden* till det DNS-namn som du skapade i ett tidigare steg.

Skapa en `hello-world-ingress.yaml` fil med namnet och kopiera i följande exempel YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
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

Skapa ingressresursen `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic` med kommandot.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Skapa ett certifikatobjekt

Därefter måste en certifikatresurs skapas. Certifikatresursen definierar önskat X.509-certifikat. Mer information finns i [certifikatutfärdarcertifikat][cert-manager-certificates].

Cert-manager har troligen automatiskt skapat ett certifikatobjekt för dig med ingress-shims, som automatiskt distribueras med cert-manager sedan v0.2.2. Mer information finns [i dokumentationen för ingress-shim][ingress-shim].

Om du vill kontrollera att certifikatet `kubectl describe certificate tls-secret --namespace ingress-basic` har skapats använder du kommandot.

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

Om du behöver skapa ytterligare en certifikatresurs kan du göra det med följande exempelmanifest. Uppdatera *dnsNames* och *domäner* till det DNS-namn som du skapade i ett tidigare steg. Om du använder en intern ingress-styrenhet anger du det interna DNS-namnet för tjänsten.

```yaml
apiVersion: cert-manager.io/v1alpha2
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

Använd kommandot om du `kubectl apply -f certificates.yaml` vill skapa certifikatresursen.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testa ingresskonfigurationen

Öppna en webbläsare till FQDN för din Kubernetes-ingress-styrenhet, till exempel *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Eftersom dessa `letsencrypt-staging`exempel använder är det utfärdade SSL-certifikatet inte betrott av webbläsaren. Acceptera varningsmeddelandet för att fortsätta till ditt program. Certifikatinformationen visar detta *Fake LE Intermediate X1-certifikat* som utfärdas av Let's Encrypt. Detta falska certifikat `cert-manager` anger att begäran behandlas korrekt och fått ett certifikat från leverantören:

![Nu ska vi kryptera mellanlagringscertifikatet](media/ingress/staging-certificate.png)

När du ändrar Låt `prod` oss `staging`kryptera för att använda i stället för används ett betrott certifikat utfärdat av Let's Encrypt, vilket visas i följande exempel:

![Låt oss kryptera certifikat](media/ingress/certificate.png)

Demoprogrammet visas i webbläsaren:

![Programexempel ett](media/ingress/app-one.png)

Lägg nu till *sökvägen /hello-world-two* i FQDN, till exempel *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. Det andra demoprogrammet med den anpassade titeln visas:

![Programexempel två](media/ingress/app-two.png)

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

Alternativt är en mer detaljerad metod att ta bort de enskilda resurser som skapats. Ta först bort certifikatresurserna:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Lista nu Helm-utgåvorna `helm list` med kommandot. Leta efter diagram med namnet *nginx-ingress*, *cert-manager*och *aks-helloworld*, som visas i följande exempelutdata:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-11 15:02:21.51172346   deployed        aks-helloworld-0.1.0
aks-helloworld-2        ingress-basic   1               2020-01-11 15:03:10.533465598  deployed        aks-helloworld-0.1.0
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic    1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Ta bort utgåvorna `helm uninstall` med kommandot. I följande exempel tas NGINX-ingress-distributionen, certifikathanteraren och de två exempelapparna AKS hello world.

```
$ helm uninstall aks-helloworld aks-helloworld-2 nginx-ingress cert-manager -n ingress-basic

release "aks-helloworld" deleted
release "aks-helloworld-2" deleted
release "nginx-ingress" deleted
release "cert-manager" deleted
```

Ta sedan bort Helm-repon för AKS hello world-appen:

```console
helm repo remove azure-samples
```

Ta bort själva namnområdet. Använd `kubectl delete` kommandot och ange namnområdesnamnet:

```console
kubectl delete namespace ingress-basic
```

Ta slutligen bort den statiska offentliga IP-adressen som skapats för ingressstyrenheten. Ange ditt *MC_* klusterresursgruppnamn som erhållits i det första steget i den här artikeln, till exempel *MC_myResourceGroup_myAKSCluster_eastus:*

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
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
- [Skapa en ingående styrenhet med en dynamisk offentlig IP och konfigurera Let's Encrypt för att automatiskt generera TLS-certifikat][aks-ingress-tls]

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
