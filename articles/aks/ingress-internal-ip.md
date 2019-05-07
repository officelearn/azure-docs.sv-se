---
title: Skapa en ingress-kontrollanten för ett internt nätverk i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och konfigurerar en NGINX-ingress-kontrollanten för ett privat internt nätverk i ett kluster i Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/27/2019
ms.author: iainfou
ms.openlocfilehash: 4a648bd2704e93abedeefae14aee66ae8bfeecef
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073908"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Skapa en ingress-kontrollanten för att ett internt virtuellt nätverk i Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar den [ingress-kontrollanten för NGINX] [ nginx-ingress] i ett kluster i Azure Kubernetes Service (AKS). Ingress-kontrollanten har konfigurerats på ett internt, privat-nätverk och IP-adress. Ingen extern åtkomst tillåts. Två program körs sedan i AKS-kluster som är tillgänglig via den enda IP-adressen.

Du kan också:

- [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder en egen TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat [med en dynamisk offentlig IP-adress] [ aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder Helm för att installera NGINX ingress-kontrollant, certifikathanterare och en exempelwebbapp. Du måste ha Helm initieras i AKS-klustret och använda ett tjänstkonto för Tiller. Läs mer om att konfigurera och använda Helm [installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.61 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingress-kontrollant

Som standard skapas en ingress-kontrollanten för NGINX med en dynamisk offentlig IP-adresstilldelning. Ett vanligt krav för konfiguration är att använda ett privat internt nätverk och IP-adress. Den här metoden kan du begränsa åtkomsten till dina tjänster för interna användare utan någon extern åtkomst.

Skapa en fil med namnet *interna ingress.yaml* med hjälp av följande exempel manifestfilen. Det här exemplet tilldelar *10.240.0.42* till den *loadBalancerIP* resurs. Ange din egen interna IP-adressen för användning med ingress-kontrollant. Se till att den här IP-adressen inte är redan används i det virtuella nätverket.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Nu distribuera den *nginx-ingress* diagram med Helm. Om du vill använda manifestfilen skapade i föregående steg, lägger du till den `-f internal-ingress.yaml` parametern. För extra redundans två repliker av NGINX ingående kontrollenheterna distribueras med den `--set controller.replicaCount` parametern. Om du vill utnyttja alla fördelar med repliker av ingress-kontrollant, kontrollera att det finns fler än en nod i AKS-klustret.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingress-resurser med namnet *ingress-grundläggande*. Ange ett namnområde för din egen miljö efter behov. Om AKS-klustret inte RBAC aktiverat lägger du till `--set rbac.create=false` för Helm-kommandon.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2
```

När tjänsten för Kubernetes belastningsutjämning har skapats för ingress-kontrollanten för NGINX kan tilldelas din interna IP-adressen som visas i följande Exempelutdata:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Inga inkommande regler har skapats ännu, så att NGINX ingående controller standard 404 visas om du bläddrar till den interna IP-adressen. Ingående regler har konfigurerats i följande steg.

## <a name="run-demo-applications"></a>Köra demo-program

Om du vill se ingress-kontrollanten för i praktiken ska vi kör två demoprogrammen AKS-klustret. I det här exemplet används Helm för att distribuera två instanser av en enkel ”Hello world”-program.

Innan du kan installera exempel Helm-diagram, lägger du till lagringsplatsen Azure-exempel i miljön Helm på följande sätt:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Skapa det första demonstrationsprogrammet från ett Helm-diagram med följande kommando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Nu installera en andra instans av demoprogrammet. För den andra instansen anger du ett nytt namn så att de två programmen är visuellt åtskilda. Du kan även ange ett unikt namn:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Skapa en inkommande väg

Båda programmen körs nu på ett Kubernetes-kluster. Skapa en Kubernetes ingress-resurs för att dirigera trafik till varje App. Ingress-resursen konfigurerar regler som vidarebefordrar trafik till en av de två programmen.

I följande exempel trafik till adressen `http://10.240.0.42/` dirigeras till tjänsten med namnet `aks-helloworld`. Trafik till adressen `http://10.240.0.42/hello-world-two` dirigeras till den `ingress-demo` service.

Skapa en fil med namnet `hello-world-ingress.yaml` och kopiera följande YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
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

## <a name="test-the-ingress-controller"></a>Testa ingress-kontrollant

Bläddra till de två programmen med en webbklient för att testa vägarna för ingress-kontrollant. Du kan snabbt testa den här interna funktioner från en pod på AKS-klustret om det behövs. Skapa en test-pod och bifogas en terminalsession:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Installera `curl` i en pod med hjälp av `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Nu komma åt adressen för din Kubernetes ingress domänkontrollant med hjälp av `curl`, till exempel *http://10.240.0.42*. Ange din egen interna IP-adressen som anges när du distribuerade ingress-kontrollanten för i det första steget i den här artikeln.

```console
curl -L http://10.240.0.42
```

Ingen ytterligare sökväg har tillhandahållits med adressen så ingress-kontrollant som standard den */* väg. Första demoprogrammet returneras enligt följande komprimerade exempel på utdata:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Lägg nu till */hello-world-two* sökvägen till adressen, till exempel *http://10.240.0.42/hello-world-two*. Andra demoprogrammet med anpassade rubriken returneras enligt följande komprimerade exempel på utdata:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Rensa resurser

Den här artikeln används Helm för att installera komponenter för ingångshändelser och exempelappar. När du distribuerar ett Helm-diagram, skapas ett antal Kubernetes-resurser. Dessa resurser inkluderar poddar, distributioner och tjänster. Om du vill rensa resurserna du antingen ta bort hela exemplet namnområde eller enskilda resurser.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort namnområdet exemplet och alla resurser

Ta bort namnområdet hela exemplet genom att använda den `kubectl delete` kommandot och ange namnet på ditt namnområde. Alla resurser i namnområdet tas bort.

```console
kubectl delete namespace ingress-basic
```

Ta sedan bort Helm-lagringsplatsen för AKS hello world-app:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

Du kan också är en mer detaljerad metod att ta bort enskilda resurserna som du skapade. Lista Helm släpper med den `helm list` kommando. Leta efter diagram med namnet *nginx-ingress* och *aks-helloworld*, vilket visas i följande Exempelutdata:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort versionerna med de `helm delete` kommando. I följande exempel tar bort NGINX ingående distribution och två AKS hello world exempelapparna.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Ta sedan bort Helm-lagringsplatsen för AKS hello world-app:

```console
helm repo remove azure-samples
```

Ta bort den inkommande väg som dirigeras trafiken till exempelapparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

Slutligen kan du ta bort de själva namnområde. Använd den `kubectl delete` kommandot och ange namnet på ditt namnområde:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln ingår vissa externa komponenter till AKS. Mer information om dessa komponenter finns på följande sidor i projektet:

- [Helm CLI][helm-cli]
- [Ingress-kontrollanten för NGINX][nginx-ingress]

Du kan också:

- [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant med en dynamisk offentlig IP-adress och konfigurera kryptera vi för att automatiskt generera TLS-certifikat][aks-ingress-tls]
- [Skapa en ingress-kontrollant med en statisk offentlig IP-adress och konfigurera kryptera vi för att automatiskt generera TLS-certifikat][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
