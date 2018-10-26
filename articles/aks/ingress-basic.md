---
title: Skapa en grundläggande ingress-kontrollant i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och konfigurerar en grundläggande NGINX ingress-kontrollant i ett kluster i Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: fdce7c1f5876442a10fbf77b315e082a0a817dfd
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093393"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Skapa en ingress-kontrollant i Azure Kubernetes Service (AKS)

Ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbara trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera de inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollanten och ingående regler, kan en IP-adress användas till att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar den [ingress-kontrollanten för NGINX] [ nginx-ingress] i ett kluster i Azure Kubernetes Service (AKS). Två program körs sedan i AKS-kluster som är tillgänglig via den enda IP-adressen.

Du kan också:

- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant som använder en egen TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat [med en dynamisk offentlig IP-adress] [ aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder Helm för att installera NGINX ingress-kontrollant, certifikathanterare och en exempelwebbapp. Du måste ha Helm initieras i AKS-klustret och använda ett tjänstkonto för Tiller. Läs mer om att konfigurera och använda Helm [installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.41 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingress-kontrollant

Du kan skapa ingress-kontrollant med `Helm` installera *nginx-ingress*. För extra redundans två repliker av NGINX ingående kontrollenheterna distribueras med den `--set controller.replicaCount` parametern. Om du vill utnyttja alla fördelar med repliker av ingress-kontrollant, kontrollera att det finns fler än en nod i AKS-klustret.

> [!TIP]
> I följande exempel installeras ingress-kontrollanten för i den `kube-system` namnområde. Du kan ange ett annat namnområde för din egen miljö om så önskas. Om AKS-klustret inte RBAC aktiverat lägger du till `--set rbac.create=false` för kommandon.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

När tjänsten för Kubernetes belastningsutjämning har skapats för ingress-kontrollanten för NGINX kan tilldelas en dynamisk offentlig IP-adress som visas i följande Exempelutdata:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                         TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
masked-otter-nginx-ingress-controller        LoadBalancer   10.0.92.99    40.117.74.8   80:31077/TCP,443:32592/TCP   7m
masked-otter-nginx-ingress-default-backend   ClusterIP      10.0.46.106   <none>        80/TCP                       7m
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
helm install azure-samples/aks-helloworld
```

Nu installera en andra instans av demoprogrammet. För den andra instansen anger du ett nytt namn så att de två programmen är visuellt åtskilda. Du kan även ange ett unikt namn:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Skapa en inkommande väg

Båda programmen körs nu på ett Kubernetes-kluster. Skapa en Kubernetes ingress-resurs för att dirigera trafik till varje App. Ingress-resursen konfigurerar regler som vidarebefordrar trafik till en av de två programmen.

I följande exempel trafik till adressen `http://40.117.74.8/` dirigeras till tjänsten med namnet `aks-helloworld`. Trafik till adressen `http://40.117.74.8/hello-world-two` dirigeras till den `ingress-demo` service.

Skapa en fil med namnet `hello-world-ingress.yaml` och kopiera följande YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
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

Om du vill testa vägarna för ingress-kontrollant, bläddrar du till de två programmen. Öppna en webbläsare till IP-adressen för din NGINX ingress-kontrollant, till exempel *http://40.117.74.8*. Det första demonstrationsprogrammet visas i webbläsaren, som visas i följande exempel:

![Första app som körs bakom ingress-kontrollant](media/ingress-basic/app-one.png)

Lägg nu till den */hello-world-two* sökvägen till IP-adress, som *http://40.117.74.8/hello-world-two*. Det andra demonstrationsprogrammet med anpassade rubriken visas:

![Andra app som körs bakom ingress-kontrollant](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Rensa resurser

Den här artikeln används Helm för att installera komponenter för ingångshändelser och exempelappar. När du distribuerar ett Helm-diagram, skapas ett antal Kubernetes-resurser. Dessa resurser inkluderar poddar, distributioner och tjänster. Om du vill rensa resurserna först visa Helm-versioner med den `helm list` kommando. Leta efter diagram med namnet *nginx-ingress* och *aks-helloworld*, vilket visas i följande Exempelutdata:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
gilded-duck         1           Tue Oct 16 16:52:25 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
righteous-numbat    1           Tue Oct 16 16:53:53 2018    DEPLOYED    aks-helloworld-0.1.0                default
looming-moth        1           Tue Oct 16 16:53:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort versionerna med de `helm delete` kommando. I följande exempel tar bort NGINX ingående distribution och två AKS hello world exempelapparna.

```
$ helm delete gilded-duck righteous-numbat looming-moth

release "gilded-duck" deleted
release "righteous-numbat" deleted
release "looming-moth" deleted
```

Ta sedan bort Helm-lagringsplatsen för AKS hello world-app:

```console
helm repo remove azure-samples
```

Slutligen tar du bort den inkommande väg som dirigeras trafiken till exempelapparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln ingår vissa externa komponenter till AKS. Mer information om dessa komponenter finns på följande sidor i projektet:

- [Azure CLI][helm-cli]
- [Ingress-kontrollanten för NGINX][nginx-ingress]

Du kan också:

- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant som använder en egen TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat [med en dynamisk offentlig IP-adress] [ aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
