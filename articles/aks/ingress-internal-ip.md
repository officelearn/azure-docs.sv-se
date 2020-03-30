---
title: Skapa en ingress-styrenhet för ett internt nätverk i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och konfigurerar en NGINX-ingress-styrenhet för ett internt, privat nätverk i ett AKS-kluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 8c3eeaf2f9a92f1be9c691091d8e33d09a60b22d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595662"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Skapa en ingress-styrenhet till ett internt virtuellt nätverk i Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [NGINX-inkommande styrenhet][nginx-ingress] i ett AKS-kluster (Azure Kubernetes Service). Den ingående styrenheten är konfigurerad på ett internt, privat virtuellt nätverk och IP-adress. Ingen extern åtkomst är tillåten. Två program körs sedan i AKS-klustret, som var och en är tillgänglig via den enda IP-adressen.

Du kan också:

- [Skapa en grundläggande ingress-styrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera http-programroutningstillägget][aks-http-app-routing]
- [Skapa en ingående styrenhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingressstyrenhet som använder Let's Encrypt för att automatiskt generera [TLS-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

I den här artikeln används Helm för att installera NGINX-ingress-styrenheten, cert-managern och en exempelwebbapp. Du måste få Helm initierat i AKS-klustret och använda ett tjänstkonto för Tiller. Mer information om hur du konfigurerar och använder Helm finns i [Installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingående styrenhet

Som standard skapas en NGINX-ingress-styrenhet med en dynamisk offentlig IP-adresstilldelning. Ett vanligt konfigurationskrav är att använda ett internt, privat nätverk och IP-adress. Med den här metoden kan du begränsa åtkomsten till dina tjänster till interna användare utan extern åtkomst.

Skapa en fil med namnet *internal-ingress.yaml* med hjälp av följande exempelmanifestfil. I det här exemplet tilldelas *10.240.0.42* till *loadBalancerIP-resursen.* Ange din egen interna IP-adress för användning med ingångsstyrenheten. Kontrollera att den här IP-adressen inte redan används i det virtuella nätverket.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Distribuera nu *nginx-ingress-diagrammet* med Helm. Om du vill använda manifestfilen som `-f internal-ingress.yaml` skapades i föregående steg lägger du till parametern. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Om du till fullo kan dra nytta av att köra repliker av ingressstyrenheten kontrollerar du att det finns mer än en nod i AKS-klustret.

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
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

När tjänsten Kubernetes belastningsutjämnare skapas för NGINX-ingressstyrenheten tilldelas din interna IP-adress, vilket visas i följande exempelutdata:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Inga ingressregler har skapats ännu, så NGINX-ingress-styrenhetens standardsida 404 visas om du bläddrar till den interna IP-adressen. Regler för ingående konfigureras i följande steg.

## <a name="run-demo-applications"></a>Kör demoprogram

Om du vill se den ingående styrenheten i aktion ska vi köra två demoprogram i AKS-klustret. I det här exemplet används Helm för att distribuera två instanser av ett enkelt Hello world-program.

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

Båda programmen körs nu i kubernetes-klustret. Om du vill dirigera trafik till varje program skapar du en Kubernetes-ingressresurs. Ingressresursen konfigurerar de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras `http://10.240.0.42/` trafiken till adressen till `aks-helloworld`tjänsten . Trafiken till `http://10.240.0.42/hello-world-two` adressen dirigeras `ingress-demo` till tjänsten.

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

Skapa ingressresursen `kubectl apply -f hello-world-ingress.yaml` med kommandot.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testa ingressstyrenheten

Om du vill testa rutterna för ingående styrenheten bläddrar du till de två programmen med en webbklient. Om det behövs kan du snabbt testa den här interna funktionen från en pod i AKS-klustret. Skapa en testkapsel och bifoga en terminalsession till den:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Installera `curl` i pod `apt-get`med:

```console
apt-get update && apt-get install -y curl
```

Nu tillgång till adressen för din Kubernetes ingress controller med `curl`, till exempel *http://10.240.0.42*. Ange din egen interna IP-adress som angavs när du distribuerade ingressstyrenheten i det första steget i den här artikeln.

```console
curl -L http://10.240.0.42
```

Ingen ytterligare sökväg angavs med adressen, så den */* ingående styrenheten som standard är till rutten. Det första demoprogrammet returneras, vilket visas i följande komprimerade exempelutdata:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Lägg nu till */hello-world-two-sökvägen* till adressen, till exempel *http://10.240.0.42/hello-world-two*. Det andra demoprogrammet med den anpassade titeln returneras, vilket visas i följande komprimerade exempelutdata:

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

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort utgåvorna `helm delete` med kommandot. I följande exempel tas NGINX-ingress-distributionen bort och de två exempelen AKS hello world apps.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
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

- [Skapa en grundläggande ingress-styrenhet med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera http-programroutningstillägget][aks-http-app-routing]
- [Skapa en ingående styrenhet med en dynamisk offentlig IP och konfigurera Let's Encrypt för att automatiskt generera TLS-certifikat][aks-ingress-tls]
- [Skapa en ingående styrenhet med en statisk offentlig IP-adress och konfigurera Let's Encrypt för att automatiskt generera TLS-certifikat][aks-ingress-static-tls]

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