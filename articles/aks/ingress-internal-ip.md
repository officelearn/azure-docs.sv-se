---
title: Skapa en ingångs kontroll för ett internt nätverk i Azure Kubernetes service (AKS)
description: Lär dig hur du installerar och konfigurerar en NGINX ingress Controller för ett internt, privat nätverk i ett Azure Kubernetes service-kluster (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 935b96bd553c9ae73b55086483baa0ea7c4aeaa4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615475"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Skapa en ingångs kontroll för ett internt virtuellt nätverk i Azure Kubernetes service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [nginx ingress][nginx-ingress] -kontrollanten i ett Azure Kubernetes service-kluster (AKS). Ingångs styrenheten är konfigurerad på ett internt, privat virtuellt nätverk och IP-adress. Ingen extern åtkomst tillåts. Två program körs sedan i AKS-klustret, som var och en är tillgänglig över den enskilda IP-adressen.

Du kan också:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS [-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder Helm för att installera NGINX ingress-kontrollant, cert Manager och en webbapp. Du måste ha Helm initierat i ditt AKS-kluster och med ett tjänst konto för till gången. Mer information om hur du konfigurerar och använder Helm finns i [installera program med Helm i Azure Kubernetes service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI-version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingångs kontroll enhet

Som standard skapas en NGINX ingångs kontroll med en dynamisk offentlig IP-adresstilldelning. Ett vanligt konfigurations krav är att använda ett internt, privat nätverk och IP-adress. Med den här metoden kan du begränsa åtkomsten till dina tjänster till interna användare utan någon extern åtkomst.

Skapa en fil med namnet *Internal-ingress. yaml* med hjälp av följande exempel manifest fil. I det här exemplet tilldelas *10.240.0.42* till *loadBalancerIP* -resursen. Ange din egna interna IP-adress för användning med ingångs styrenheten. Kontrol lera att den här IP-adressen inte redan används i det virtuella nätverket.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Distribuera nu *nginx-ingress-* diagrammet med Helm. Om du vill använda manifest filen som skapades i föregående steg lägger du `-f internal-ingress.yaml` till parametern. För ytterligare redundans distribueras två repliker av nginx-ingångs styrenheterna med `--set controller.replicaCount` parametern. Se till att det finns fler än en nod i ditt AKS-kluster för att få full nytta av att köra repliker av ingångs styrenheten.

Ingångs kontroll enheten måste också schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i för hands version i AKS) behöver inte köra ingångs styrenheten. En Node-selektor anges med `--set nodeSelector` parametern för att instruera Kubernetes Scheduler att köra nginx-ingångs styrenheten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för de ingress-resurser som heter *ingress-Basic*. Ange ett namn område för din egen miljö efter behov. Om ditt AKS-kluster inte är RBAC-aktiverat `--set rbac.create=false` lägger du till dem i Helm-kommandona.

> [!TIP]
> Om du vill aktivera [IP-konservering för klient källa][client-source-ip] för förfrågningar till behållare i klustret, lägger `--set controller.service.externalTrafficPolicy=Local` du till det i Helm install-kommandot. Klientens käll-IP lagras i begär ande huvudet under *X-forwarded – for*. När du använder en ingångs kontroll för att aktivera IP-konservering för klient källa fungerar inte SSL-vidarekoppling.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

När belastnings Utjämnings tjänsten för Kubernetes skapas för NGINX-ingångs styrenheten, tilldelas den interna IP-adressen, som visas i följande exempel:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Inga ingångs regler har skapats ännu. därför visas sidan NGINX ingress Controller standard 404 om du bläddrar till den interna IP-adressen. Ingress-regler konfigureras i följande steg.

## <a name="run-demo-applications"></a>Köra demo program

För att se hur ingångs enheten fungerar kan vi köra två demo program i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt "Hello World"-program.

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

Båda programmen körs nu på ditt Kubernetes-kluster. Skapa en Kubernetes ingress-resurs för att dirigera trafik till varje program. I ingress-resursen konfigureras de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras trafik till adressen `http://10.240.0.42/` till tjänsten med namnet. `aks-helloworld` Trafik till adressen `http://10.240.0.42/hello-world-two` dirigeras `ingress-demo` till tjänsten.

Skapa en fil med `hello-world-ingress.yaml` namnet och kopiera i följande exempel yaml.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
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

Skapa den inkommande resursen med hjälp `kubectl apply -f hello-world-ingress.yaml` av kommandot.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testa ingångs styrenheten

Om du vill testa vägarna för ingångs styrenheten bläddrar du till de två programmen med en webb klient. Vid behov kan du snabbt testa den här interna funktionen från en POD i AKS-klustret. Skapa en test-Pod och koppla en terminalsession till den:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Installera `curl` i pod med `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Nu kan du komma åt adressen till din Kubernetes ingångs hanterare `curl`med *http://10.240.0.42* , till exempel. Ange din egna interna IP-adress som anges när du distribuerade ingångs styrenheten i det första steget i den här artikeln.

```console
curl -L http://10.240.0.42
```

Ingen ytterligare sökväg tillhandahölls med adressen, så ingångs styrenhetens standardinställning för */* vägen. Det första demo programmet returneras, som du ser i följande komprimerade exempel utdata:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Lägg nu till */Hello-World-Two* sökväg till adressen, till exempel *http://10.240.0.42/hello-world-two* . Det andra demonstrations programmet med den anpassade rubriken returneras, som du ser i följande komprimerade exempel utdata:

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

I den här artikeln används Helm för att installera ingångs komponenter och exempel appar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. De här resurserna omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempel namnområdet eller enskilda resurser.

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

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort utgåvorna `helm delete` med kommandot. I följande exempel tar vi bort NGINX ingress-distributionen och de två exempel AKS Hello World-apparna.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Ta sedan bort Helm-lagrings platsen för appen AKS Hello World:

```console
helm repo remove azure-samples
```

Ta bort ingångs vägen som riktar sig mot trafik till exempel apparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

Slutligen kan du ta bort själva namn området. `kubectl delete` Använd kommandot och ange namn på namn område:

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
- [Skapa en ingångs kontroll enhet med en dynamisk offentlig IP-adress och konfigurera låt oss kryptera för att automatiskt generera TLS-certifikat][aks-ingress-tls]
- [Skapa en ingångs kontroll enhet med en statisk offentlig IP-adress och konfigurera låt oss kryptera för att automatiskt generera TLS-certifikat][aks-ingress-static-tls]

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
[client-source-ip]: concepts-network.md#ingress-controllers