---
title: Skapa en grundläggande ingress-kontrollant i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och konfigurerar en grundläggande NGINX ingress-kontrollant i ett kluster i Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/27/2019
ms.author: iainfou
ms.openlocfilehash: fd9695698f90a1efebb71a2b24a196dd8c911081
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680170"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Skapa en ingress-kontrollant i Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar den [ingress-kontrollanten för NGINX] [ nginx-ingress] i ett kluster i Azure Kubernetes Service (AKS). Två program körs sedan i AKS-kluster som är tillgänglig via den enda IP-adressen.

Du kan också:

- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant som använder en egen TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat [med en dynamisk offentlig IP-adress] [ aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder Helm för att installera NGINX ingress-kontrollant, certifikathanterare och en exempelwebbapp. Du måste ha Helm initieras i AKS-klustret och använda ett tjänstkonto för Tiller. Läs mer om att konfigurera och använda Helm [installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.61 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingress-kontrollant

Du kan skapa ingress-kontrollant med `Helm` installera *nginx-ingress*. För extra redundans två repliker av NGINX ingående kontrollenheterna distribueras med den `--set controller.replicaCount` parametern. Om du vill utnyttja alla fördelar med repliker av ingress-kontrollant, kontrollera att det finns fler än en nod i AKS-klustret.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingress-resurser med namnet *ingress-grundläggande*. Ange ett namnområde för din egen miljö efter behov. Om AKS-klustret inte RBAC aktiverat lägger du till `--set rbac.create=false` för Helm-kommandon.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress --namespace ingress-basic --set controller.replicaCount=2
```

När tjänsten för Kubernetes belastningsutjämning har skapats för ingress-kontrollanten för NGINX kan tilldelas en dynamisk offentlig IP-adress som visas i följande Exempelutdata:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
aspiring-labradoodle-nginx-ingress-controller        LoadBalancer   10.0.61.144    40.117.74.8   80:30386/TCP,443:32276/TCP   6m2s
aspiring-labradoodle-nginx-ingress-default-backend   ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
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

I följande exempel trafik till adressen `http://40.117.74.8/` dirigeras till tjänsten med namnet `aks-helloworld`. Trafik till adressen `http://40.117.74.8/hello-world-two` dirigeras till den `ingress-demo` service.

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

Om du vill testa vägarna för ingress-kontrollant, bläddrar du till de två programmen. Öppna en webbläsare till IP-adressen för din NGINX ingress-kontrollant, till exempel *http://40.117.74.8*. Det första demonstrationsprogrammet visas i webbläsaren, som visas i följande exempel:

![Första app som körs bakom ingress-kontrollant](media/ingress-basic/app-one.png)

Lägg nu till den */hello-world-two* sökvägen till IP-adress, som *http://40.117.74.8/hello-world-two*. Det andra demonstrationsprogrammet med anpassade rubriken visas:

![Andra app som körs bakom ingress-kontrollant](media/ingress-basic/app-two.png)

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

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
aspiring-labradoodle    1           Wed Mar 27 19:55:37 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      ingress-basic
esteemed-koala          1           Wed Mar 27 19:59:18 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
wonderful-puma          1           Wed Mar 27 19:59:07 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
```

Ta bort versionerna med de `helm delete` kommando. I följande exempel tar bort NGINX ingående distribution och två AKS hello world exempelapparna.

```
$ helm delete aspiring-labradoodle esteemed-koala wonderful-puma

release "aspiring-labradoodle" deleted
release "esteemed-koala" deleted
release "wonderful-puma" deleted
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

- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant som använder en egen TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat [med en dynamisk offentlig IP-adress] [ aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
