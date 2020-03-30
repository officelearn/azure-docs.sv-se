---
title: Skapa en grundläggande ingress-styrenhet i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och konfigurerar en grundläggande NGINX-ingress-styrenhet i ett AKS-kluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 12/20/2019
ms.openlocfilehash: e37f7aa677be129aa9fe568880c53cc860947e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595645"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Skapa en ingress-styrenhet i Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [NGINX-inkommande styrenhet][nginx-ingress] i ett AKS-kluster (Azure Kubernetes Service). Två program körs sedan i AKS-klustret, som var och en är tillgänglig via den enda IP-adressen.

Du kan också:

- [Aktivera http-programroutningstillägget][aks-http-app-routing]
- [Skapa en ingressstyrenhet som använder en intern, privat nätverks- och IP-adress][aks-ingress-internal]
- [Skapa en ingående styrenhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingressstyrenhet som använder Let's Encrypt för att automatiskt generera [TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

I den här artikeln används Helm för att installera NGINX-ingress-styrenheten och en exempelwebbapp.

Den här artikeln kräver också att du kör Azure CLI version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingående styrenhet

Om du vill skapa ingressstyrenheten använder du Helm för att installera *nginx-ingress*. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Om du till fullo kan dra nytta av att köra repliker av ingressstyrenheten kontrollerar du att det finns mer än en nod i AKS-klustret.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i förhandsversion i AKS) bör inte köra ingressstyrenheten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingressresurserna som heter *ingress-basic*. Ange ett namnområde för din egen miljö efter behov.

> [!TIP]
> Om du vill aktivera [IP-bevarande][client-source-ip] för klientkälla för `--set controller.service.externalTrafficPolicy=Local` begäranden till behållare i klustret lägger du till i kommandot Helm install. Klientkällans IP lagras i begäranden under *X-Forwarded-For*. När du använder en ingående styrenhet med klientkälla IP-bevarande aktiverat, ssl-vidaregång kommer inte att fungera.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

När tjänsten Kubernetes belastningsutjämnare skapas för NGINX-ingressstyrenheten tilldelas en dynamisk offentlig IP-adress, vilket visas i följande exempelutdata:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Inga ingressregler har skapats ännu, så NGINX-ingress-styrenhetens standardsida 404 visas om du bläddrar till den interna IP-adressen. Regler för ingående konfigureras i följande steg.

## <a name="run-demo-applications"></a>Kör demoprogram

Om du vill se den ingående styrenheten i aktion ska vi köra två demoprogram i AKS-klustret. I det här exemplet används Helm för att distribuera två instanser av ett enkelt *Hello world-program.*

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
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Skapa en ingående rutt

Båda programmen körs nu i kubernetes-klustret. Om du vill dirigera trafik till varje program skapar du en Kubernetes-ingressresurs. Ingressresursen konfigurerar de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras trafik till *EXTERNAL_IP* till `aks-helloworld`den tjänst som heter . Trafik till *EXTERNAL_IP/hello-world-two* dirigeras till `aks-helloworld-two` tjänsten. Trafik till *EXTERNAL_IP/statisk* dirigeras till den `aks-helloworld` tjänst som namnges efter statiska resurser.

Skapa en `hello-world-ingress.yaml` fil med namnet och kopiera i följande exempel YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Skapa ingressresursen `kubectl apply -f hello-world-ingress.yaml` med kommandot.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>Testa ingressstyrenheten

Om du vill testa rutterna för ingående styrenheten bläddrar du till de två programmen. Öppna en webbläsare till IP-adressen för NGINX-ingress-styrenheten, till exempel *EXTERNAL_IP*. Det första demoprogrammet visas i webbläsaren, som visas i följande exempel:

![Första appen som körs bakom ingresskontrollen](media/ingress-basic/app-one.png)

Lägg nu till *sökvägen /hello-world-two* till IP-adressen, till exempel *EXTERNAL_IP/hello-world-two*. Det andra demoprogrammet med den anpassade titeln visas:

![Andra appen som körs bakom ingresskontrollen](media/ingress-basic/app-two.png)

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
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-06 19:57:00.131576 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-06 19:57:10.971365 -0600 CST    deployed        aks-helloworld-0.1.0               
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Ta bort utgåvorna `helm delete` med kommandot. I följande exempel tas NGINX-ingress-distributionen bort och de två exempelen AKS hello world apps.

```
$ helm delete aks-helloworld aks-helloworld-two nginx-ingress --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "nginx-ingress" uninstalled
```

Ta sedan bort Helm-repon för AKS hello world-appen:

```console
helm repo remove azure-samples
```

Ta bort den ingående vägen som dirigerade trafik till exempelapparna:

```console
kubectl delete -f hello-world-ingress.yaml
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

- [Aktivera http-programroutningstillägget][aks-http-app-routing]
- [Skapa en ingressstyrenhet som använder en intern, privat nätverks- och IP-adress][aks-ingress-internal]
- [Skapa en ingående styrenhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingressstyrenhet som använder Let's Encrypt för att automatiskt generera [TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

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
[client-source-ip]: concepts-network.md#ingress-controllers
