---
title: Använd dina TLS-certifikat för ingress
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du installerar och konfigurerar en NGINX ingress-kontrollant som använder dina egna certifikat i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 7cc0cbd3809446d67875abfd2f5508889b381f61
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145403"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Skapa en inkommande styrenhet för HTTPS och använd dina egna TLS-certifikat på Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [nginx ingress-kontrollanten][nginx-ingress] i ett Azure Kubernetes service-kluster (AKS). Du genererar dina egna certifikat och skapar en Kubernetes-hemlighet för användning med ingångs vägen. Slutligen körs två program i AKS-klustret, som var och en är tillgänglig över en enskild IP-adress.

Du kan också:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS [-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder [Helm 3][helm] för att installera nginx ingress-kontrollanten och en webbapp. Du måste ha Helm initierat i ditt AKS-kluster och med ett tjänst konto för till gången. Kontrol lera att du använder den senaste versionen av Helm. Anvisningar om hur du uppgraderar finns i [installations dokument för Helm][helm-install]. Mer information om hur du konfigurerar och använder Helm finns i [installera program med Helm i Azure Kubernetes service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI-version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingångs kontroll enhet

Om du vill skapa en ingångs `Helm` kontroll, använder du för att installera *nginx-ingress*. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Se till att det finns fler än en nod i ditt AKS-kluster för att få full nytta av att köra repliker av ingångs styrenheten.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i för hands version i AKS) behöver inte köra ingångs styrenheten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för de ingress-resurser som heter *ingress-Basic*. Ange ett namn område för din egen miljö efter behov. Om ditt AKS-kluster inte är RBAC-aktiverat `--set rbac.create=false` lägger du till dem i Helm-kommandona.

> [!TIP]
> Om du vill aktivera [IP-konservering för klient källa][client-source-ip] för förfrågningar till behållare i klustret, lägger `--set controller.service.externalTrafficPolicy=Local` du till det i Helm install-kommandot. Klientens käll-IP lagras i begär ande huvudet under *X-forwarded – for*. TLS-vidarekoppling fungerar inte när du använder en ingångs kontroll för att aktivera IP-konservering i klient källan.

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

Under installationen skapas en offentlig Azure-IP-adress för ingångs styrenheten. Den här offentliga IP-adressen är statisk för den ingångs kontrollens livs längd. Om du tar bort ingångs kontrollen går den offentliga IP-adresstilldelning förlorad. Om du sedan skapar ytterligare en ingångs kontroll, tilldelas en ny offentlig IP-adress. Om du vill behålla användningen av den offentliga IP-adressen kan du i stället skapa en ingångs [kontroll enhet med en statisk offentlig IP-adress][aks-ingress-static-tls].

Använd `kubectl get service` kommandot för att hämta den offentliga IP-adressen. Det tar några minuter innan IP-adressen tilldelas till tjänsten.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Anteckna den här offentliga IP-adressen, eftersom den används i det sista steget för att testa distributionen.

Inga ingress-regler har skapats ännu. Om du bläddrar till den offentliga IP-adressen visas sidan NGINX ingångs styrenhetens standard 404.

## <a name="generate-tls-certificates"></a>Generera TLS-certifikat

I den här artikeln ska vi generera ett självsignerat certifikat med `openssl`. För produktions användningen bör du begära ett betrott signerat certifikat via en provider eller din egen certifikat utfärdare (CA). I nästa steg genererar du en Kubernetes- *hemlighet* med TLS-certifikatet och den privata nyckeln som genereras av OpenSSL.

I följande exempel genereras ett 2048-bitars RSA X509-certifikat giltigt i 365 dagar med namnet *AKS-ingress-TLS. CRT*. Den privata nyckel filen heter *AKS-ingress-TLS. Key*. En Kubernetes TLS-hemlighet kräver båda dessa filer.

Den här artikeln fungerar med det *demo.Azure.com* ämnets gemensamma namn och behöver inte ändras. För produktions användning anger du dina egna organisations värden för `-subj` parametern:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Skapa Kubernetes-hemlighet för TLS-certifikat

Om du vill tillåta att Kubernetes använder TLS-certifikatet och den privata nyckeln för ingångs styrenheten skapar du och använder en hemlighet. Hemligheten definieras en gång och använder certifikatet och nyckel filen som skapades i föregående steg. Du refererar sedan till den här hemligheten när du definierar ingress-vägar.

I följande exempel skapas ett hemligt namn *AKS – ingress-TLS*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Köra demo program

En ingångs kontroll och en hemlighet med ditt certifikat har kon figurer ATS. Nu ska vi köra två demo program i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt "Hello World"-program.

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

Båda programmen körs nu på ditt Kubernetes-kluster, men de har kon figurer ATS med en tjänst `ClusterIP`av typen. Därför är programmen inte tillgängliga från Internet. Skapa en Kubernetes ingress-resurs för att göra dem offentligt tillgängliga. I ingress-resursen konfigureras de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras trafik till adressen `https://demo.azure.com/` till tjänsten med namnet. `aks-helloworld` Trafik till adressen `https://demo.azure.com/hello-world-two` dirigeras till `ingress-demo` tjänsten. I den här artikeln behöver du inte ändra namnen på dessa demonstrations värdar. För produktions användning anger du namnen som anges som en del av certifikatbegäran och generations processen.

> [!TIP]
> Om det värdnamn som anges under processen för certifikatbegäran, CN-namnet, inte matchar den angivna värden i din ingångs väg visas ingångs enheten i en Kubernetes ingångs *kontroll enhet falsk certifikat* varning. Kontrol lera att ditt certifikat och att dirigera värdnamn matchar.

Avsnittet *TLS* instruerar ingångs vägen att använda hemligheten med namnet *AKS-ingress-tls* för värden *demo.Azure.com*. För produktions användning anger du din egen värd adress.

Skapa en fil med `hello-world-ingress.yaml` namnet och kopiera i följande exempel yaml.

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

Skapa den inkommande resursen med hjälp av `kubectl apply -f hello-world-ingress.yaml` kommandot.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testa ingress-konfigurationen

Om du vill testa certifikaten med vår falska *demo.Azure.com* - `curl` värd, använder du och anger parametern *--* parameter. Med den här parametern kan du mappa *demo.Azure.com* -namnet till den offentliga IP-adressen för din ingångs kontroll. Ange den offentliga IP-adressen för din egen ingångs kontroll, som du ser i följande exempel:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Ingen ytterligare sökväg tillhandahölls med adressen, så ingångs styrenhetens standardinställning för */* vägen. Det första demo programmet returneras, som du ser i följande komprimerade exempel utdata:

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

Parametern *-v* i vårt `curl` kommando matar utförlig information, inklusive det mottagna TLS-certifikatet. Halvvägs genom dina typografiska utdata kan du kontrol lera att ditt eget TLS-certifikat användes. Parametern *-k* fortsätter att läsa in sidan även om vi använder ett självsignerat certifikat. I följande exempel visas att *utfärdaren: CN = demo. Azure. com; O = AKS-ingångs-TLS-* certifikat användes:

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

Lägg nu till */Hello-World-Two* sökväg till adressen, till exempel `https://demo.azure.com/hello-world-two`. Det andra demonstrations programmet med den anpassade rubriken returneras, som du ser i följande komprimerade exempel utdata:

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

I den här artikeln används Helm för att installera ingångs komponenter och exempel appar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempel namnområdet eller enskilda resurser.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempel namn området och alla resurser

Om du vill ta bort hela exempel namnområdet `kubectl delete` använder du kommandot och anger namn på namn området. Alla resurser i namn området tas bort.

```console
kubectl delete namespace ingress-basic
```

Ta sedan bort Helm-lagrings platsen för appen AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

Alternativt är en mer detaljerad metod att ta bort de enskilda resurserna som skapats. Visar en lista med Helm- `helm list` versioner med kommandot. Leta efter diagram med namnet *nginx – ingress* och *AKS-HelloWorld*, som du ser i följande exempel resultat:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort utgåvorna `helm delete` med kommandot. I följande exempel tar vi bort NGINX ingress-distributionen och de två exempel AKS Hello World-apparna.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Ta sedan bort Helm-lagrings platsen för appen AKS Hello World:

```console
helm repo remove azure-samples
```

Ta bort ingångs vägen som riktar sig mot trafik till exempel apparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

Ta bort certifikat hemligheten:

```console
kubectl delete secret aks-ingress-tls
```

Slutligen kan du ta bort själva namn området. Använd `kubectl delete` kommandot och ange namn på namn område:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln ingår några externa komponenter i AKS. Mer information om dessa komponenter finns i följande projekt sidor:

- [Helm CLI][helm-cli]
- [NGINX ingress-styrenhet][nginx-ingress]

Du kan också:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS [-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
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
