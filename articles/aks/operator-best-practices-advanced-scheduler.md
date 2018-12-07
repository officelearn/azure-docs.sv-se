---
title: Metodtips för operatorn – avancerade scheduler funktioner i Azure Kubernetes Services (AKS)
description: Läs kluster operatorn metodtipsen för att använda avancerade scheduler-funktioner som taints och tolerations, noden väljare och tillhörighet, eller mellan pod tillhörighet och anti-tillhörighet i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 395b0cadf3ba3313a9a1304d9244f1fe72a8209c
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016886"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Metodtips för avancerade scheduler funktioner i Azure Kubernetes Service (AKS)

Du hanterar kluster i Azure Kubernetes Service (AKS) bör behöva du ofta isolera team och arbetsbelastningar. Scheduler Kubernetes tillhandahåller avancerade funktioner som låter dig styra vilka poddar kan schemaläggas på vissa noder eller hur flera pod program på rätt sätt kan distribueras i klustret. 

Den här bästa praxis-artikeln fokuserar på avancerade Kubernetes schemaläggningsfunktioner för klusteroperatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Använd taints och tolerations att begränsa vilka poddar kan schemaläggas på noder
> * Prioritera poddar ska köras på vissa noder med noden väljare eller nodtillhörighet
> * Dela upp eller gruppen tillsammans poddar med mellan pod tillhörighet eller anti-tillhörighet

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Ange dedikerade noder med taints och tolerations

**Bästa praxis riktlinjer** – begränsa åtkomsten för resurs-intensiva program, till exempel ingress-styrenheter till specifika noder. Håll nod-resurser som är tillgängliga för arbetsbelastningar som kräver dem och Tillåt inte schemaläggning av andra arbetsbelastningar på noderna.

När du skapar AKS-kluster kan du distribuera noder med GPU-stöd eller ett stort antal kraftfulla processorer. Dessa noder används ofta för bearbetning av stora arbetsbelastningar som machine learning-(ML) eller artificiell intelligens (AI). Eftersom den här typen av maskinvara är vanligtvis en dyr noden resurs att distribuera, begränsa de arbetsbelastningar som kan schemaläggas på noderna. Du kan i stället välja att dedikera vissa noder i klustret för att köra ingress-tjänster och förhindra att andra arbetsbelastningar.

Kubernetes-Schemaläggaren kan använda taints och tolerations för att begränsa vilka arbetsbelastningar kan köras på noder.

* En **förorena** tillämpas på en nod som anger att endast specifika poddar kan schemaläggas på dem.
* En **toleration** sedan appliceras till en pod som låter dem *tolerera* färg för en nod.

När du distribuerar en pod till ett AKS-kluster, schemalägger Kubernetes endast poddar på noder där en toleration justeras med färg. Anta att du har en nodepool i AKS-kluster för noder med GPU stöder till exempel. Du kan definiera namn, till exempel *gpu*, sedan ett värde för schemaläggning. Om det här värdet anges till *NoSchedule*, Kubernetes-Schemaläggaren kan inte schemalägga poddar på noden om poden inte definierar lämpliga toleration.

```console
kubectl taint node aks-nodepool1 gpu:NoSchedule
```

Med en färg som tillämpas på noderna, därefter anger du en toleration i pod-specifikationen som tillåter schemaläggning på noderna. Följande exempel definierar den `key: gpu` och `effect: NoSchedule` ska kunna hanteras den färg som tillämpas på noden i föregående steg:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
  resources:
    requests:
      cpu: 0.5
      memory: 2Gi
    limits:
      cpu: 4.0
      memory: 16Gi
  tolerations:
  - key: "gpu"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
```

Om den här pod distribueras, till exempel med `kubectl apply -f gpu-toleration.yaml`, Kubernetes har schemalägga pod på noderna med den färg som används. Den här logisk isolering kan du styra åtkomsten till resurser i ett kluster.

När du tillämpar taints arbeta med programutvecklare och ägare och låt dem att definiera nödvändiga tolerations i sina distributioner.

Läs mer om taints och tolerations [tillämpa taints och tolerations][k8s-taints-tolerations].

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Kontrollen pod schemaläggning med hjälp av noden väljare och tillhörighet

**Bästa praxis riktlinjer** – styra schemaläggning av poddar på noder med hjälp av noden väljare, nodtillhörighet, eller kommunikation mellan pod tillhörighet. De här inställningarna gör det möjligt för Kubernetes-Schemaläggaren för att isolera logiskt arbetsbelastningar, till exempel av maskinvara i noden.

Taints och tolerations används för att logiskt isolera resurser med en hård avklippt – om en pod inte tolerera fel på en nod färg, den är inte schemalagd på noden. En annan metod är att använda noden väljare. Du märka noder, som att ange lokalt anslutna SSD-lagring eller en stor mängd minne och sedan definiera i pod-specifikationen en nod-väljare. Kubernetes schemalägger sedan dessa poddar på en matchande nod. Till skillnad från tolerations, kan du schemalägga poddar utan en matchande noden väljare på märkta noder. Detta gör att oanvända resurser på noder för att använda, men ger prioritet till poddar som definierar matchande noden Väljaren.

Nu ska vi titta på ett exempel på noder med en hög mängd minne. Dessa noder kan ge inställning till poddar som begär en hög mängd minne. För att säkerställa att resurserna inte sitta inaktiv, kan de också andra poddar ska köras.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

En pod-specifikation sedan lägger till den `nodeSelector` egenskapen att definiera en nod-väljare som matchar den anges på en nod:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
    nodeSelector:
      hardware: highmem
```

När du använder alternativen scheduler kan arbeta med programutvecklare och ägare och låt dem korrekt definiera sina pod-specifikationer.

Läs mer om hur du använder noden väljare [tilldela Poddar till noder][k8s-node-selector].

### <a name="node-affinity"></a>Nodtillhörighet

En nod-väljare är ett enkelt sätt att tilldela poddar till en viss nod. Mer flexibilitet är tillgängliga med *nodtillhörighet*. Med noden tillhörighet definierar du vad händer om en pod inte kan matchas med en nod. Du kan *kräver* att Kubernetes scheduler matchar en pod med en taggade värd. Du kan också *föredrar* en matchning men Tillåt pod schemaläggas på en annan värd om den inte matchar är tillgänglig.

I följande exempel anger nodtillhörighet till *requiredDuringSchedulingIgnoredDuringExecution*. Den här tillhörighet kräver Kubernetes-schema för att använda en nod med en motsvarande etikett. Om det finns någon nod har poden att vänta tills schemaläggning för att fortsätta. Om du vill tillåta pod schemaläggas på en annan nod, kan du i stället ange värdet *preferredDuringScheduledIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: hardware
          operator: In
          values: highmem
```

Den *IgnoredDuringExecution* en del av inställningen anger att om noden etiketter ändringen, poden inte bör tas bort från noden. Kubernetes-scheduler använder bara de uppdaterade noden etiketterna för nya poddarna schemalagda, inte de poddar som redan har schemalagts på noderna.

Mer information finns i [tillhörighet och anti-tillhörighet][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Kommunikation mellan pod tillhörighet och anti-tillhörighet

En slutlig metod för Kubernetes-Schemaläggaren för att isolera arbetsbelastningar logiskt använder mellan pod tillhörighet eller anti-tillhörighet. Inställningarna definierar de poddarna *bör inte* schemaläggas på en nod som har en befintlig matchande pod eller att de *bör* schemaläggas. Som standard försöker Kubernetes scheduler schemalägger flera poddar i en replikuppsättning mellan noder. Du kan definiera mer specifika regler lösa problemet.

Ett bra exempel är ett webbprogram som använder också en Azure Cache för Redis. Du kan använda pod anti-tillhörighet för regler för att begära att Kubernetes scheduler distribuerar repliker mellan noder. Du kan sedan ise tillhörighet regler för att se till att varje web app-komponenten har schemalagts på samma värddator som en motsvarande cache. Distributionen av poddar över noder ser ut som i följande exempel:

| **Nod 1** | **Nod 2** | **Noden 3** |
|------------|------------|------------|
| WebApp-1   | WebApp-2   | WebApp-3   |
| cache-1    | cache-2    | cache-3    |

Det här exemplet är en mer komplex distribution än användningen av noden väljare eller nodtillhörighet. Distribution ger dig kontroll över hur Kubernetes schemalägger poddar på noder och logiskt kan isolera resurser. En komplett exempel på det här webbprogrammet med Azure Cache för Redis-exempel finns i [samordna poddar på samma nod][k8s-pod-affinity].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på avancerade funktioner för Kubernetes scheduler. Mer information om kluster i AKS finns i följande metoder:

* [Isolering för flera innehavare och kluster][aks-best-practices-scheduler]
* [Grundläggande funktioner som Kubernetes scheduler][aks-best-practices-scheduler]
* [Autentisering och auktorisering][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
