---
title: Anpassa CoreDNS för Azure Kubernetes Service (AKS)
description: Lär dig hur du anpassar CoreDNS om du vill lägga till underdomäner eller utöka anpassade DNS-slutpunkter med Azure Kubernetes Service (AKS)
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jnoller
ms.openlocfilehash: 5ff1ee03b8ac170def03576d3bf99c70957b2a8b
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507947"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Anpassa CoreDNS med Azure Kubernetes Service

Azure Kubernetes Service (AKS) använder den [CoreDNS] [ coredns] projekt för hantering av DNS och matchning med alla *1.12.x* och högre kluster. Tidigare användes kube-dns-projektet. Det här kube-dns-projektet är nu föråldrad. Mer information om CoreDNS anpassning och Kubernetes finns i den [officiella överordnade dokumentationen][corednsk8s].

Eftersom AKS är en hanterad tjänst, du kan inte ändra den huvudsakliga konfigurationen för CoreDNS (en *CoreFile*). Använd i stället en Kubernetes *ConfigMap* åsidosätta standardinställningarna. Om du vill se standard AKS CoreDNS ConfigMaps, använda den `kubectl get configmaps coredns -o yaml` kommando.

Den här artikeln visar hur du använder ConfigMaps för grundläggande anpassningsalternativ för CoreDNS i AKS.

> [!NOTE]
> `kube-dns` erbjuds olika [anpassningsalternativ] [ kubednsblog] via en Kubernetes config karta. CoreDNS är **inte** bakåtkompatibel med kube-dns. Alla eventuella anpassningar som du tidigare använt måste uppdateras för användning med CoreDNS.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med hjälp av Azure-CLI] [aks-Snabbstart-cli] eller [med Azure portal] [aks-Snabbstart-portal].

## <a name="what-is-supportedunsupported"></a>Vad är stöds/stöds inte

Alla inbyggda CoreDNS plugin-program stöds. Inga add-på/tredjeparts-plugin-program stöds.

## <a name="rewrite-dns"></a>Skriv om DNS

Ett scenario som du har är att utföra på direkt DNS-namnet omskrivningar. I följande exempel ersätter `<domain to be written>` med ditt eget fullständigt kvalificerade domännamn. Skapa en fil med namnet `corednsms.json` och klistra in följande exempelkonfiguration:

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
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Skapa ConfigMap med hjälp av den [kubectl gäller configmap] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f corednsms.json
```

För att kontrollera anpassningarna har tillämpats, använder den [kubectl hämta configmaps] [ kubectl-get] och ange din *coredns anpassade* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Nu tvinga CoreDNS att uppdatera ConfigMap. Den [kubectl ta bort pod] [ kubectl delete] kommando inte destruktiva och inte orsakar driftstopp. Den `kube-dns` poddar tas bort och Kubernetes Scheduler återskapar dem sedan. Dessa nya poddarna innehålla ändringen i TTL-värdet.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Kommandot ovan är korrekt. Även om vi ändrar `coredns`, distribueras den **kube-dns** namn.

## <a name="custom-proxy-server"></a>Anpassad proxyserver

Om du vill ange en proxyserver för din nätverkstrafik kan skapa du en ConfigMap om du vill anpassa DNS. I följande exempel uppdaterar de `proxy` namn och adress med värden för din egen miljö. Skapa en fil med namnet `corednsms.json` och klistra in följande exempelkonfiguration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    .:53 {
        proxy foo.com 1.1.1.1
    }
```

Som i föregående exempel skapar ConfigMap med den [kubectl gäller configmap] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest. Sedan kan tvinga CoreDNS läses in på nytt ConfigMap med hjälp av den [kubectl ta bort pod] [ kubectl delete] för Kubernetes-Schemaläggaren för att återskapa dem:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Använd anpassade domäner

Du kanske vill konfigurera anpassade domäner som kan bara lösas internt. Du kan till exempel vill lösa den anpassade domänen *puglife.local*, vilket inte en giltig toppnivådomän. Utan en anpassad domän ConfigMap kan inte AKS-kluster matcha adressen.

Uppdatera anpassad domän och IP-adress i följande exempel visas att dirigera trafik till med värden för din egen miljö. Skapa en fil med namnet `corednsms.json` och klistra in följande exempelkonfiguration:

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
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

Som i föregående exempel skapar ConfigMap med den [kubectl gäller configmap] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest. Sedan kan tvinga CoreDNS läses in på nytt ConfigMap med hjälp av den [kubectl ta bort pod] [ kubectl delete] för Kubernetes-Schemaläggaren för att återskapa dem:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>Stub-domäner

CoreDNS kan också användas för att konfigurera stub-domäner. I följande exempel uppdaterar du anpassade domäner och IP-adresser med värden för din egen miljö. Skapa en fil med namnet `corednsms.json` och klistra in följande exempelkonfiguration:

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
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }

```

Som i föregående exempel skapar ConfigMap med den [kubectl gäller configmap] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest. Sedan kan tvinga CoreDNS läses in på nytt ConfigMap med hjälp av den [kubectl ta bort pod] [ kubectl delete] för Kubernetes-Schemaläggaren för att återskapa dem:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Värdar plugin-programmet

Eftersom alla inbyggda plugin-program stöds detta innebär att CoreDNS [värdar] [ coredns hosts] plugin-programmet är tillgängligt för att anpassa samt:

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

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade några exempelscenarier för CoreDNS anpassning. Information om projektet CoreDNS finns [sidan CoreDNS överordnade projekt][coredns].

Läs mer om grundbegreppen för nätverk i [Network begrepp för program i AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - external -->
[concepts-network]: concepts-network.md
