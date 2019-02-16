---
title: Routning tillägg för HTTP-program på Azure Kubernetes Service (AKS)
description: Använd tillägg av routning för HTTP-program på Azure Kubernetes Service (AKS).
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/25/2018
ms.author: laevenso
ms.openlocfilehash: d6e1cc033416c90e27b5caf4bba310400e55b3a5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312295"
---
# <a name="http-application-routing"></a>Routning av HTTP-program

HTTP-Programlösningen routning gör det enkelt att komma åt program som distribueras till ditt kluster i Azure Kubernetes Service (AKS). När lösningen är aktiverad, konfigurerar en Ingress-kontrollanten för AKS-klustret. När program distribueras skapar lösningen även offentliga DNS-namn för slutpunkterna.

När tillägget är aktiverat skapas en DNS-zon i din prenumeration. Läs mer om DNS-kostnaden [DNS priser][dns-pricing].

> [!CAUTION]
> Tillägg till routning för HTTP-program är utformat så att du snabbt skapa en ingress-kontrollanten och kommer åt dina program. Det här tillägget rekommenderas inte för användning i produktion. Produktionsklar ingress-distributioner som omfattar flera repliker och TLS stöder finns i [skapa en HTTPS-ingress-kontrollant](https://docs.microsoft.com/azure/aks/ingress-tls).

## <a name="http-routing-solution-overview"></a>HTTP-routning lösningsöversikt

Tillägget distribuerar två komponenter: en [Kubernetes Ingress-kontrollant] [ ingress] och en [externt DNS] [ external-dns] controller.

- **Ingress-kontrollant**: Ingress-kontrollant exponeras mot internet med hjälp av en Kubernetes-tjänst av typen LoadBalancer. Ingress-kontrollant bevakar och implementerar [Kubernetes Ingress resurser][ingress-resource], vilket skapar vägar till slutpunkterna.
- **Externa DNS-controller**: Söker efter Kubernetes Ingress-resurser och skapar DNS A-poster i DNS-zonen klusterspecifika.

## <a name="deploy-http-routing-cli"></a>Distribuera HTTP-routning: CLI

Tillägg till routning för HTTP-program kan aktiveras med Azure CLI när du distribuerar ett AKS-kluster. Du gör detta genom att använda den [az aks skapa] [ az-aks-create] med den `--enable-addons` argumentet.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Om du vill aktivera flera tillägg kan du ange dem som en kommaavgränsad lista. Till exempel använda formatet om du vill aktivera övervakning och routning av HTTP-program, `--enable-addons http_application_routing,monitoring`.

Du kan också aktivera HTTP-routning på en befintlig AKS-kluster med den [az aks enable-tillägg] [ az-aks-enable-addons] kommando. Om du vill aktivera HTTP-routning i ett befintligt kluster, lägger du till den `--addons` parametern och ange *http_application_routing* som visas i följande exempel:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

När klustret distribueras eller uppdaterats, använda den [az aks show] [ az-aks-show] kommando för att hämta DNS-zonens namn. Det här namnet behövs för att distribuera program till AKS-klustret.

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table

Result
-----------------------------------------------------
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Distribuera HTTP-routning: Portalen

Tillägg till routning för HTTP-program kan aktiveras via Azure portal när du distribuerar ett AKS-kluster.

![Aktivera funktionen http-Routning](media/http-routing/create.png)

När klustret har distribuerats kan bläddra till den automatiskt skapade AKS-resursgruppen och ange DNS-zonen. Anteckna namnet på DNS-zonen. Det här namnet behövs för att distribuera program till AKS-klustret.

![Hämta DNS-zonens namn](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Använd HTTP-Routning

HTTP-Programlösningen routning kan endast utlösas på Ingress-resurser med följande:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Skapa en fil med namnet **exempel-http-program-routing.yaml** och kopiera följande YAML. På raden 43, uppdatera `<CLUSTER_SPECIFIC_DNS_ZONE>` med DNS-zonens namn som samlas in i föregående steg i den här artikeln.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

Använd den [kubectl gäller] [ kubectl-apply] kommando för att skapa resurser.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

Använda cURL eller en webbläsare för att navigera till värdnamn som anges i avsnittet värden i exempel-http-program-routing.yaml-filen. Det kan ta upp till en minut innan den är tillgänglig via internet.

```bash
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="remove-http-routing"></a>Ta bort HTTP-Routning

HTTP-routning lösningen kan tas bort med hjälp av Azure CLI. För att göra det kör du följande kommando, och ersätt dina AKS-kluster och resurs gruppnamn.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

När HTTP-programmet routning tillägget är inaktiverat, kan vissa Kubernetes-resurser kvar i klustret. De här resurserna inkluderar *configMaps* och *hemligheter*, och skapas i den *kube system* namnområde. För att underhålla en ren kluster kan du ta bort dessa resurser.

Leta efter *tillägg http-program-routning* resurser med hjälp av följande [kubectl hämta] [ kubectl-get] kommandon:

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

Följande Exempelutdata visar configMaps som ska tas bort:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Ta bort resurser genom att använda den [kubectl ta bort] [ kubectl-delete] kommando. Ange resurstyp, resursnamn och namnområde. I följande exempel tar bort en av de föregående configmaps:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Upprepa föregående `kubectl delete` steget för alla *tillägg http-program-routning* resurser som finns kvar i klustret.

## <a name="troubleshoot"></a>Felsöka

Använd den [kubectl loggar] [ kubectl-logs] kommando för att visa programloggarna för externa DNS-programmet. Loggarna bekräftar du att en A- och DNS TXT-post har skapats.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Dessa poster visas också på DNS-zonresurs i Azure-portalen.

![Hämta DNS-poster](media/http-routing/clippy.png)

Använd den [kubectl loggar] [ kubectl-logs] kommando för att visa programloggarna för Nginx Ingress-kontrollant. Loggarna ska bekräfta den `CREATE` av en resurs med ingångs- och Läs in igen för kontrollenheten. Alla HTTP-aktivitet loggas.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Rensa

Ta bort de associerade Kubernetes-objekten som skapats i den här artikeln.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>Nästa steg

Information om hur du installerar en HTTPS-skyddad Ingress-kontrollant i AKS finns i [HTTPS ingående på Azure Kubernetes Service (AKS)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
