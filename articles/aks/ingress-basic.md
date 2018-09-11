---
title: Skapa en grundläggande ingress-kontrollant i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och konfigurerar en grundläggande NGINX ingress-kontrollant i ett kluster i Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 3ae7a3193e0a4bacc64524f477b6c179ead20b6b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357688"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Skapa en ingress-kontrollant i Azure Kubernetes Service (AKS)

Ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbara trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera de inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollanten och ingående regler, kan en IP-adress användas till att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar den [ingress-kontrollanten för NGINX] [ nginx-ingress] i ett kluster i Azure Kubernetes Service (AKS). Två program körs sedan i AKS-kluster som är tillgänglig via den enda IP-adressen.

Du kan också:

- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant med en dynamisk offentlig IP-adress och konfigurera kryptera vi för att automatiskt generera TLS-certifikat][aks-ingress-tls]
- [Skapa en ingress-kontrollant med en statisk offentlig IP-adress och konfigurera kryptera vi för att automatiskt generera TLS-certifikat][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder Helm för att installera NGINX ingress-kontrollant, certifikathanterare och en exempelwebbapp. Du måste ha Helm initieras i AKS-klustret och använda ett tjänstkonto för Tiller. Läs mer om att konfigurera och använda Helm [installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.41 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingress-kontrollant

Du kan skapa ingress-kontrollant med `Helm` installera *nginx-ingress*.

> [!TIP]
> I följande exempel installeras ingress-kontrollanten för i den `kube-system` namnområde. Du kan ange ett annat namnområde för din egen miljö om så önskas. Om AKS-klustret inte RBAC aktiverat lägger du till `--set rbac.create=false` för kommandon.

```console
helm install stable/nginx-ingress --namespace kube-system
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

## <a name="next-steps"></a>Nästa steg

Den här artikeln ingår vissa externa komponenter till AKS. Mer information om dessa komponenter finns på följande sidor i projektet:

- [Azure CLI][helm-cli]
- [Ingress-kontrollanten för NGINX][nginx-ingress]

Du kan också:

- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant med en dynamisk offentlig IP-adress och konfigurera kryptera vi för att automatiskt generera TLS-certifikat][aks-ingress-tls]
- [Skapa en ingress-kontrollant med en statisk offentlig IP-adress och konfigurera kryptera vi för att automatiskt generera TLS-certifikat][aks-ingress-static-tls]

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
