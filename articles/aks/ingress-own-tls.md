---
title: Använd dina egna TLS-certifikat för ingress med kluster Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och konfigurerar en NGINX ingress-kontrollant som använder dina egna certifikat i ett kluster i Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/23/2018
ms.author: iainfou
ms.openlocfilehash: c109febc90c9dd8d9b17489c9e612f677695bd25
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727100"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Skapa en ingress-kontrollanten för HTTPS och Använd dina egna TLS-certifikat på Azure Kubernetes Service (AKS)

Ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbara trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera de inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollanten och ingående regler, kan en IP-adress användas till att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar den [ingress-kontrollanten för NGINX] [ nginx-ingress] i ett kluster i Azure Kubernetes Service (AKS). Du skapa egna certifikat och skapa ett Kubernetes-hemlighet för användning med inkommande väg. Slutligen körs två program i AKS-kluster som är tillgänglig via en IP-adress.

Du kan också:

- [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat [med en dynamisk offentlig IP-adress] [ aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder Helm för att installera NGINX ingress-kontrollanten och en exempelwebbapp. Du måste ha Helm initieras i AKS-klustret och använda ett tjänstkonto för Tiller. Kontrollera att du använder den senaste versionen av Helm. Uppgradera anvisningar finns i den [Helm installera docs][helm-install]. Läs mer om att konfigurera och använda Helm [installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.41 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingress-kontrollant

Du kan skapa ingress-kontrollant med `Helm` installera *nginx-ingress*. För extra redundans två repliker av NGINX ingående kontrollenheterna distribueras med den `--set controller.replicaCount` parametern. Om du vill utnyttja alla fördelar med repliker av ingress-kontrollant, kontrollera att det finns fler än en nod i AKS-klustret.

> [!TIP]
> I följande exempel installeras ingress-kontrollanten för i den `kube-system` namnområde. Du kan ange ett annat namnområde för din egen miljö om så önskas. Om AKS-klustret inte RBAC aktiverat lägger du till `--set rbac.create=false` för kommandon.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

Under installationen skapas en Azure offentlig IP-adress för ingress-kontrollant. Den här offentliga IP-adressen är statisk för livslängden för ingress-kontrollant. Om du tar bort ingress-kontrollant förloras den offentliga IP-adresstilldelningen. Om du skapar sedan en ytterligare ingress-kontrollant, tilldelas en ny offentlig IP-adress. Om du vill behålla användningen av offentliga IP-adress kan du i stället [skapa en ingress-kontrollant med en statisk offentlig IP-adress][aks-ingress-static-tls].

Hämta den offentliga IP-adressen med den `kubectl get service` kommando. Det tar några minuter för IP-adress som ska tilldelas till tjänsten.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Anteckna den här offentliga IP-adressen, eftersom den används i det sista steget för att testa distributionen.

Inga inkommande regler har skapats ännu. Om du bläddrar till den offentliga IP-adressen visas NGINX ingående controller standard 404-sida.

## <a name="generate-tls-certificates"></a>Generera TLS-certifikat

I den här artikeln ska vi skapa ett självsignerat certifikat med `openssl`. För produktion bör du begära ett betrodda, signerade certifikat via en provider eller en egen certifikatutfärdare (CA). I nästa steg ska du skapa ett Kubernetes *hemlighet* med hjälp av TLS-certifikat och privata nyckeln genereras av OpenSSL.

I följande exempel genererar ett 2048-bitars RSA X 509 certifikat som gäller för 365 dagar med namnet *aks-ingress-tls.crt*. Den privata nyckelfilen heter *aks-ingress-tls.key*. En Kubernetes TLS-hemlighet kräver båda dessa filer.

Den här artikeln fungerar med den *demo.azure.com* vanliga ämnesnamn och behöver inte ändras. För användning i produktion, ange dina egna organisationens värden för den `-subj` parameter:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Skapa Kubernetes-hemlighet för TLS-certifikat

Om du vill tillåta Kubernetes att använda TLS-certifikat och privat nyckel för ingress-kontrollant du skapar och använder en hemlighet. Hemligheten definieras en gång och använder certifikat och nyckelfil som skapades i föregående steg. Du kan sedan referera den här hemligheten när du definierar ingående vägar.

I följande exempel skapas ett hemliga namn *aks-ingress-tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Köra demo-program

Ingress-kontrollanten och en hemlighet med ditt certifikat har konfigurerats. Nu ska vi köra två demonstrera program i AKS-klustret. I det här exemplet används Helm för att distribuera två instanser av en enkel ”Hello world”-program.

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

I följande exempel trafik till adressen `https://demo.azure.com/` dirigeras till tjänsten med namnet `aks-helloworld`. Trafik till adressen `https://demo.azure.com/hello-world-two` dirigeras till den `ingress-demo` service. I den här artikeln behöver du inte ändra dessa demo-värdnamn. Ange namnen på har angetts som en del av processen för begäran och generering av certifikat för produktion.

> [!TIP]
> Om det värdnamn som angavs under beställningsprocessen certifikat, CN-namnet inte matchar värden som definierats i ingående längs vägen, du inkommande controller visar en *falska certifikat som Kubernetes Ingress domänkontrollanten*. Kontrollera att ditt certifikat- och ingångsanspråk väg värd namn matchar.

Den *tls* avsnittet talar om inkommande väg att använda den hemlighet som du namnger *aks-ingress-tls* för värden *demo.azure.com*. Igen, ange din egen värdadress för produktion.

Skapa en fil med namnet `hello-world-ingress.yaml` och kopiera följande YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
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

Så här testar du certifikat med vår falska *demo.azure.com* vara värd för, använder `curl` och ange den *--lösa* parametern. Den här parametern kan du mappa den *demo.azure.com* namn till offentliga IP-adressen för ingress-kontrollant. Ange din egen ingress-styrenhet, offentliga IP-adress som du ser i följande exempel:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Ingen ytterligare sökväg har tillhandahållits med adressen så ingress-kontrollant som standard den */* väg. Första demoprogrammet returneras enligt följande komprimerade exempel på utdata:

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

Den *- v* parameter i vår `curl` kommando visar utförlig information, inklusive TLS-certifikatet som togs emot. Halvvägs via curl-utdata, du kan kontrollera att dina egna TLS-certifikat har använts. Den *-k* parametern fortsätter att läsa in sidan, även om vi använder ett självsignerat certifikat. I följande exempel visar att den *utfärdare: CN=demo.Azure.com; O = aks-ingress-tls* certifikat har använts:

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

Lägg nu till */hello-world-two* sökvägen till adressen, till exempel `https://demo.azure.com/hello-world-two`. Andra demoprogrammet med anpassade rubriken returneras enligt följande komprimerade exempel på utdata:

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

Den här artikeln används Helm för att installera komponenter för ingångshändelser och exempelappar. När du distribuerar ett Helm-diagram, skapas ett antal Kubernetes-resurser. Dessa resurser inkluderar poddar, distributioner och tjänster. Om du vill rensa, först visa Helm-versioner med den `helm list` kommando. Leta efter diagram med namnet *nginx-ingress* och *aks-helloworld*, vilket visas i följande Exempelutdata:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort versionerna med de `helm delete` kommando. I följande exempel tar bort NGINX ingående distribution och två AKS hello world exempelapparna.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Ta sedan bort Helm-lagringsplatsen för AKS hello world-app:

```console
helm repo remove azure-samples
```

Ta bort den inkommande väg som dirigeras trafiken till exempelapparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

Slutligen ska du ta bort certifikatet hemlighet:

```console
kubectl delete secret aks-ingress-tls
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln ingår vissa externa komponenter till AKS. Mer information om dessa komponenter finns på följande sidor i projektet:

- [Helm CLI][helm-cli]
- [Ingress-kontrollanten för NGINX][nginx-ingress]

Du kan också:

- [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat [med en dynamisk offentlig IP-adress] [ aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

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
