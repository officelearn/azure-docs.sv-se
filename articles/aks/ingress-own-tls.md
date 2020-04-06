---
title: Använda dina TLS-certifikat för inträngning
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du installerar och konfigurerar en NGINX-ingående styrenhet som använder dina egna certifikat i ett AKS-kluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: ec6a398b52424c142b3d7ee82625c10c733456ab
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668463"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Skapa en inkommande styrenhet för HTTPS och använd dina egna TLS-certifikat på Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [NGINX-inkommande styrenhet][nginx-ingress] i ett AKS-kluster (Azure Kubernetes Service). Du skapar dina egna certifikat och skapar en Kubernetes-hemlighet som kan användas med ingående vägen. Slutligen körs två program i AKS-klustret, som var och en är tillgänglig via en enda IP-adress.

Du kan också:

- [Skapa en grundläggande ingress-styrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera http-programroutningstillägget][aks-http-app-routing]
- [Skapa en ingressstyrenhet som använder en intern, privat nätverks- och IP-adress][aks-ingress-internal]
- Skapa en ingressstyrenhet som använder Let's Encrypt för att automatiskt generera [TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

I den här artikeln används Helm för att installera NGINX-ingress-styrenheten och en exempelwebbapp. Du måste få Helm initierat i AKS-klustret och använda ett tjänstkonto för Tiller. Se till att du använder den senaste versionen av Helm. Instruktioner för uppgradering finns i [Helm-installationsdokumenten][helm-install]. Mer information om hur du konfigurerar och använder Helm finns i [Installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingående styrenhet

Om du vill skapa `Helm` ingressstyrenheten använder du för att installera *nginx-ingress*. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Om du till fullo kan dra nytta av att köra repliker av ingressstyrenheten kontrollerar du att det finns mer än en nod i AKS-klustret.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i förhandsversion i AKS) bör inte köra ingressstyrenheten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingressresurserna som heter *ingress-basic*. Ange ett namnområde för din egen miljö efter behov. Om AKS-klustret inte är `--set rbac.create=false` RBAC aktiverat lägger du till i Helm-kommandona.

> [!TIP]
> Om du vill aktivera [IP-bevarande][client-source-ip] för klientkälla för `--set controller.service.externalTrafficPolicy=Local` begäranden till behållare i klustret lägger du till i kommandot Helm install. Klientkällans IP lagras i begäranden under *X-Forwarded-For*. När du använder en ingående styrenhet med klientkälla IP-bevarande aktiverat, ssl-vidaregång kommer inte att fungera.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Under installationen skapas en offentlig Azure-IP-adress för inkommande styrenhet. Den här offentliga IP-adressen är statisk för ingressstyrens livslängd. Om du tar bort den ingående styrenheten går den offentliga IP-adresstilldelningen förlorad. Om du sedan skapar ytterligare en ingress-styrenhet tilldelas en ny offentlig IP-adress. Om du vill behålla användningen av den offentliga IP-adressen kan du i stället [skapa en ingress-styrenhet med en statisk offentlig IP-adress][aks-ingress-static-tls].

Använd kommandot om `kubectl get service` du vill hämta den offentliga IP-adressen. Det tar några minuter innan IP-adressen tilldelas tjänsten.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Anteckna den här offentliga IP-adressen, eftersom den används i det sista steget för att testa distributionen.

Inga ingressregler har skapats ännu. Om du bläddrar till den offentliga IP-adressen visas NGINX-ingress-styrenhetens standardsida 404.

## <a name="generate-tls-certificates"></a>Generera TLS-certifikat

I den här artikeln ska vi generera `openssl`ett självsignerat certifikat med . För produktionsanvändning bör du begära ett betrott, signerat certifikat via en provider eller din egen certifikatutfärdare. I nästa steg genererar du en Kubernetes *Secret* med TLS-certifikatet och den privata nyckeln som genereras av OpenSSL.

I följande exempel genereras ett 2048-bitars RSA X509-certifikat som är giltigt i 365 dagar med namnet *aks-ingress-tls.crt*. Den privata nyckelfilen heter *aks-ingress-tls.key*. En Kubernetes TLS hemlighet kräver båda dessa filer.

Den här artikeln fungerar med *det demo.azure.com* vanliga namnet och behöver inte ändras. För produktionsanvändning anger du dina egna `-subj` organisationsvärden för parametern:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Skapa Kubernetes-hemlighet för TLS-certifikat

Om du vill att Kubernetes ska kunna använda TLS-certifikatet och den privata nyckeln för ingående styrenheten skapar och använder du en hemlighet. Hemligheten definieras en gång och använder certifikatet och nyckelfilen som skapades i föregående steg. Du refererar sedan till den här hemligheten när du definierar ingående vägar.

I följande exempel skapas ett hemligt namn *aks-ingress-tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Kör demoprogram

En ingående handkontroll och en hemlighet med ditt certifikat har konfigurerats. Nu ska vi köra två demoprogram i AKS-klustret. I det här exemplet används Helm för att distribuera två instanser av ett enkelt Hello world-program.

Innan du kan installera exempeldiagrammen lägger du till Azure-exempeldatabasen i Helm-miljön på följande sätt:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Skapa det första demoprogrammet från ett Helm-diagram med följande kommando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Nu installera en andra instans av demoprogrammet. För den andra instansen anger du en ny rubrik så att de två programmen är visuellt distinkta. Du anger också ett unikt tjänstnamn:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Skapa en ingående rutt

Båda programmen körs nu i Kubernetes-klustret, men de `ClusterIP`är konfigurerade med en tjänst av typen . Därför är programmen inte tillgängliga från internet. Skapa en Kubernetes-ingressresurs för att göra dem tillgängliga för allmänheten. Ingressresursen konfigurerar de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras `https://demo.azure.com/` trafiken till adressen till `aks-helloworld`tjänsten . Trafiken till `https://demo.azure.com/hello-world-two` adressen dirigeras `ingress-demo` till tjänsten. I den här artikeln behöver du inte ändra dessa demovärdnamn. För produktionsanvändning anger du de namn som anges som en del av certifikatbegäran och genereringsprocessen.

> [!TIP]
> Om värdnamnet som angavs under certifikatbegäraneprocessen, CN-namnet, inte matchar värden som definierats i din ingående väg, visar du en *kubernetes ingress Controller Fake Certificate* varning. Kontrollera att certifikatet och värdnamnen för ingående vägen matchar.

Avsnittet *tls* talar om för ingressvägen att använda secret med namnet *aks-ingress-tls* för värden *demo.azure.com*. Återigen, för produktionsbruk, ange din egen värdadress.

Skapa en `hello-world-ingress.yaml` fil med namnet och kopiera i följande exempel YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
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

Skapa ingressresursen `kubectl apply -f hello-world-ingress.yaml` med kommandot.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testa ingresskonfigurationen

Om du vill testa certifikaten med vår `curl` falska *demo.azure.com* värd använder och anger parametern *--resolve.* Med den här parametern kan du mappa *demo.azure.com* namn till den offentliga IP-adressen för din ingress-styrenhet. Ange den offentliga IP-adressen för din egen ingångskontrollant, som visas i följande exempel:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Ingen ytterligare sökväg angavs med adressen, så den */* ingående styrenheten som standard är till rutten. Det första demoprogrammet returneras, vilket visas i följande komprimerade exempelutdata:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Parametern *-v* `curl` i vårt kommando matar ut utförlig information, inklusive TLS-certifikatet som tas emot. Halvvägs genom din curl utgång, kan du kontrollera att ditt eget TLS-certifikat användes. Parametern *-k* fortsätter att läsa in sidan även om vi använder ett självsignerat certifikat. Följande exempel visar att *utfärdaren: CN=demo.azure.com; O=aks-ingress-tls certifikat* användes:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Lägg nu till */hello-world-two-sökvägen* till adressen, till exempel `https://demo.azure.com/hello-world-two`. Det andra demoprogrammet med den anpassade titeln returneras, vilket visas i följande komprimerade exempelutdata:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln användes Helm för att installera ingående komponenter och exempelappar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempelnamnområdet eller de enskilda resurserna.

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

Alternativt är en mer detaljerad metod att ta bort de enskilda resurser som skapats. Lista Helm-utgåvorna `helm list` med kommandot. Leta efter diagram som heter *nginx-ingress* och *aks-helloworld*, som visas i följande exempelutdata:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort utgåvorna `helm delete` med kommandot. I följande exempel tas NGINX-ingress-distributionen bort och de två exempelen AKS hello world apps.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Ta sedan bort Helm-repon för AKS hello world-appen:

```console
helm repo remove azure-samples
```

Ta bort den ingående vägen som dirigerade trafik till exempelapparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

Ta bort certifikatet Secret:

```console
kubectl delete secret aks-ingress-tls
```

Slutligen kan du ta bort själva namnområdet. Använd `kubectl delete` kommandot och ange namnområdesnamnet:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller några externa komponenter till AKS. Mer information om dessa komponenter finns på följande projektsidor:

- [Helm CLI][helm-cli]
- [NGINX-ingress-styrenhet][nginx-ingress]

Du kan också:

- [Skapa en grundläggande ingress-styrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera http-programroutningstillägget][aks-http-app-routing]
- [Skapa en ingressstyrenhet som använder en intern, privat nätverks- och IP-adress][aks-ingress-internal]
- Skapa en ingressstyrenhet som använder Let's Encrypt för att automatiskt generera [TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
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
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
