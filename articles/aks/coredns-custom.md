---
title: Anpassa CoreDNS för Azure Kubernetes Service (AKS)
description: Lär dig hur du anpassar CoreDNS för att lägga till underdomäner eller utöka anpassade DNS-slutpunkter med Azure Kubernetes Service (AKS)
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595832"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Anpassa CoreDNS med Azure Kubernetes Service

Azure Kubernetes Service (AKS) använder [CoreDNS-projektet][coredns] för kluster-DNS-hantering och -lösning med alla *1.12.x* och högre kluster. Tidigare användes kube-dns-projektet. Det här kube-dns-projektet är nu inaktuellt. Mer information om CoreDNS-anpassning och Kubernetes finns i den [officiella dokumentationen för uppströms][corednsk8s].

Eftersom AKS är en hanterad tjänst kan du inte ändra huvudkonfigurationen för CoreDNS (en *CoreFile).* I stället använder du en Kubernetes *ConfigMap* för att åsidosätta standardinställningarna. Om du vill se standardvärdet för AKS `kubectl get configmaps --namespace=kube-system coredns -o yaml` CoreDNS ConfigMaps använder du kommandot.

Den här artikeln visar hur du använder ConfigMaps för grundläggande anpassningsalternativ för CoreDNS i AKS. Den här metoden skiljer sig från att konfigurera CoreDNS i andra sammanhang, till exempel använda CoreFile. Kontrollera vilken version av CoreDNS du kör eftersom konfigurationsvärdena kan ändras mellan versioner.

> [!NOTE]
> `kube-dns`erbjöd olika [anpassningsalternativ][kubednsblog] via en Kubernetes config-karta. CoreDNS är **inte** bakåtkompatibel med kube-dns. Alla anpassningar som du tidigare har använt måste uppdateras för användning med CoreDNS.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Vad stöds/stöds inte

Alla inbyggda CoreDNS-plugins stöds. Inga plugin-program från tillägg/tredje part stöds.

## <a name="rewrite-dns"></a>Skriva om DNS

Ett scenario du har är att utföra on-the-fly DNS-namn omskrivningar. I följande exempel `<domain to be written>` ersätter du med ditt eget fullständigt kvalificerade domännamn. Skapa en `corednsms.yaml` fil med namnet och klistra in följande exempelkonfiguration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Skapa ConfigMap med kommandot [kubectl apply configmap][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl apply -f corednsms.yaml
```

Om du vill kontrollera att anpassningarna har tillämpats använder du [kubectl get configmaps][kubectl-get] och anger din *coredns-custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Nu tvinga CoreDNS att ladda om ConfigMap. Kommandot [kubectl delete pod][kubectl delete] är inte destruktivt och orsakar inte nedtid. Poddarna `kube-dns` tas bort och Kubernetes Scheduler återskapar dem sedan. Dessa nya poddar innehåller förändringen i TTL-värde.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Kommandot ovan är korrekt. När vi ändrar `coredns`är distributionen under **kube-dns-namnet.**

## <a name="custom-forward-server"></a>Anpassad server framåt

Om du behöver ange en framåtserver för nätverkstrafiken kan du skapa en ConfigMap för att anpassa DNS. I följande exempel uppdaterar `forward` du namn och adress med värdena för din egen miljö. Skapa en `corednsms.yaml` fil med namnet och klistra in följande exempelkonfiguration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Precis som i föregående exempel skapar du ConfigMap med kommandot [kubectl apply configmap][kubectl-apply] och anger namnet på YAML-manifestet. Tvinga sedan CoreDNS att läsa in ConfigMap igen med [kubectl-borttagningspodden][kubectl delete] för Kubernetes Scheduler för att återskapa dem:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Använda anpassade domäner

Du kanske vill konfigurera anpassade domäner som bara kan matchas internt. Du kanske till exempel vill lösa den anpassade domänen *puglife.local*, som inte är en giltig toppdomän. Utan en anpassad domän ConfigMap kan AKS-klustret inte matcha adressen.

I följande exempel uppdaterar du den anpassade domänen och IP-adressen för att dirigera trafik till med värdena för din egen miljö. Skapa en `corednsms.yaml` fil med namnet och klistra in följande exempelkonfiguration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Precis som i föregående exempel skapar du ConfigMap med kommandot [kubectl apply configmap][kubectl-apply] och anger namnet på YAML-manifestet. Tvinga sedan CoreDNS att läsa in ConfigMap igen med [kubectl-borttagningspodden][kubectl delete] för Kubernetes Scheduler för att återskapa dem:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Stub-domäner

CoreDNS kan också användas för att konfigurera stub-domäner. I följande exempel uppdaterar du anpassade domäner och IP-adresser med värdena för din egen miljö. Skapa en `corednsms.yaml` fil med namnet och klistra in följande exempelkonfiguration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Precis som i föregående exempel skapar du ConfigMap med kommandot [kubectl apply configmap][kubectl-apply] och anger namnet på YAML-manifestet. Tvinga sedan CoreDNS att läsa in ConfigMap igen med [kubectl-borttagningspodden][kubectl delete] för Kubernetes Scheduler för att återskapa dem:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Värdar plugin

Eftersom alla inbyggda plugins stöds innebär detta att CoreDNS [Hosts][coredns hosts] plugin är tillgänglig för att anpassa också:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Aktivera loggning för DNS-felsökning av dns-frågor 

Om du vill aktivera DNS-frågeloggning använder du följande konfiguration i din coredns-anpassade ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade några exempel scenarier för CoreDNS anpassning. Information om CoreDNS-projektet finns [på projektsidan CoreDNS uppströms][coredns].

Mer information om grundläggande nätverkskoncept finns [i Nätverkskoncept för program i AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
