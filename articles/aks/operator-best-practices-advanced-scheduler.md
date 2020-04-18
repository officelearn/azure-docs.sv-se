---
title: Metodtips för schemaläggarfunktioner
titleSuffix: Azure Kubernetes Service
description: Lär dig metodtipsen för klusteroperatören för att använda avancerade schemaläggarfunktioner som taints och tolerationer, nodväljare och tillhörighet, eller tillhörighet och antitillhörighet mellan pod i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: d0d13a699d2559c6b4360c807721e0b748959382
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617520"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Metodtips för avancerade schemaläggarfunktioner i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes Service (AKS) måste du ofta isolera team och arbetsbelastningar. Kubernetes-schemaläggaren innehåller avancerade funktioner som gör att du kan styra vilka poddar som kan schemaläggas på vissa noder eller hur multi-pod-program kan distribueras på lämpligt sätt över klustret. 

Den här artikeln om metodtips fokuserar på avancerade Kubernetes-schemaläggningsfunktioner för klusteroperatorer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Använd färg och tolerationer för att begränsa vad poddar kan schemaläggas på noder
> * Ge företräde åt poddar att köras på vissa noder med nodväljare eller nodtillhörighet
> * Dela isär eller gruppera ihop poddar med tillhörighet mellan pod eller anti-affinitet

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Tillhandahålla dedikerade noder med taints och tolerationer

**Vägledning för bästa praxis** - Begränsa åtkomsten för resursintensiva program, till exempel inkommande styrenheter, till specifika noder. Håll nodresurser tillgängliga för arbetsbelastningar som kräver dem och tillåt inte schemaläggning av andra arbetsbelastningar på noderna.

När du skapar AKS-klustret kan du distribuera noder med GPU-stöd eller ett stort antal kraftfulla processorer. Dessa noder används ofta för stora databehandlingsarbetsbelastningar som maskininlärning (ML) eller artificiell intelligens (AI). Eftersom den här typen av maskinvara vanligtvis är en dyr nodresurs att distribuera, begränsar du arbetsbelastningarna som kan schemaläggas på dessa noder. Du kanske i stället vill dedikera vissa noder i klustret för att köra inträngningstjänster och förhindra andra arbetsbelastningar.

Det här stödet för olika noder tillhandahålls med hjälp av flera nodpooler. Ett AKS-kluster tillhandahåller en eller flera nodpooler.

Kubernetes-schemaläggaren kan använda taints och tolerationer för att begränsa vilka arbetsbelastningar som kan köras på noder.

* En **behäftning** tillämpas på en nod som anger att endast specifika poddar kan schemaläggas på dem.
* En **tolerans** appliceras sedan på en pod som tillåter dem att *tolerera* en nods färg.

När du distribuerar en pod till ett AKS-kluster schemalägger Kubernetes bara poddar på noder där en tolerans är justerad med fläcken. Anta till exempel att du har en nodpool i AKS-klustret för noder med GPU-stöd. Du definierar namn, till exempel *gpu*, sedan ett värde för schemaläggning. Om du anger det här värdet till *NoSchedule*kan Kubernetes-schemaläggaren inte schemalägga poddar på noden om podden inte definierar lämplig tolerans.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Med en befläckning som tillämpas på noder definierar du sedan en tolerans i pod-specifikationen som tillåter schemaläggning på noderna. I följande exempel `sku: gpu` definieras `effect: NoSchedule` och tolererar den färg som tillämpas på noden i föregående steg:

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
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

När den här podden `kubectl apply -f gpu-toleration.yaml`distribueras, till exempel med hjälp av , kan Kubernetes schemalägga podden på noderna med den behäftade. Med den här logiska isoleringen kan du styra åtkomsten till resurser i ett kluster.

När du använder taints kan du arbeta med programutvecklare och ägare så att de kan definiera de tolerationer som krävs i sina distributioner.

Mer information om fläckar och tolerationer finns i [tillämpa fläckar och tolerationer][k8s-taints-tolerations].

Mer information om hur du använder flera nodpooler i AKS finns i [Skapa och hantera flera nodpooler för ett kluster i AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Beteende av fläckar och toleranser i AKS

När du uppgraderar en nodpool i AKS följer taints och tolerationer ett anten mönster när de tillämpas på nya noder:

- **Standardkluster som använder skaluppsättningar för virtuella datorer**
  - Anta att du har ett kluster med två noder - *nod1* och *nod2*. Du uppgraderar nodpoolen.
  - Ytterligare två noder skapas, *nod3* och *nod4*och taints skickas vidare respektive.
  - Den ursprungliga *noden1* och *noden2* tas bort.

- **Kluster utan stöd för skalningsuppsättning för virtuella datorer**
  - Återigen, låt oss anta att du har en tvånod kluster - *nod1* och *nod2*. När du uppgraderar skapas ytterligare en*nod (nod3).*
  - Taintserna från *noden1* tillämpas på *nod3*, sedan tas *nod1* bort.
  - En annan ny nod skapas (namnet *nod1*, sedan den föregående *noden1* togs bort) och *nod2-taints* tillämpas på den nya *noden1*. *Nod2* tas sedan bort.
  - I huvudsak *nod1* blir *nod3*, och *nod2* blir *nod1*.

När du skalar en nodpool i AKS förs inte taints och tolerationer med design.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Schemaläggning av kontrollpodden med nodväljare och tillhörighet

**Vägledning för bästa praxis** - Styr schemaläggningen av poddar på noder med nodväljare, nodtillhörighet eller tillhörighet mellan podden. Med de här inställningarna kan Kubernetes-schemaläggaren logiskt isolera arbetsbelastningar, till exempel genom maskinvara i noden.

Taints och tolerations används för att logiskt isolera resurser med en hård cut-off - om pod inte tolererar en nod är fläck, är det inte schemalagt på noden. En alternativ metod är att använda nodväljare. Du märker noder, till exempel för att ange lokalt ansluten SSD-lagring eller en stor mängd minne, och sedan definiera i pod specifikationen en nodväljare. Kubernetes schemalägger sedan poddar på en matchande nod. Till skillnad från tolerationer kan poddar utan matchande nodväljare schemaläggas på märkta noder. Med det här beteendet kan oanvända resurser på noderna förbrukas, men prioritet för poddar som definierar den matchande nodväljaren.

Låt oss titta på ett exempel på noder med en hög mängd minne. Dessa noder kan ge företräde åt poddar som begär en stor mängd minne. För att se till att resurserna inte sitter inaktiva tillåter de även andra poddar att köras.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

En pod-specifikation `nodeSelector` lägger sedan till egenskapen för att definiera en nodväljare som matchar etiketten som anges på en nod:

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

När du använder dessa schemaschemaalternativ kan du arbeta med programutvecklare och ägare så att de kan definiera sina pod-specifikationer korrekt.

Mer information om hur du använder nodväljare finns i [Tilldela poddar till noder][k8s-node-selector].

### <a name="node-affinity"></a>Nodtillhörighet

En nodväljare är ett grundläggande sätt att tilldela poddar till en viss nod. Mer flexibilitet är tillgänglig med *nodtillhörighet*. Med nodtillhörighet definierar du vad som händer om podden inte kan matchas med en nod. Du kan *kräva* att Kubernetes schemaläggare matchar en pod med en märkt värd. Eller så kan du *föredra* en matchning men låta podden schemaläggas på en annan värd om inte matchning är tillgänglig.

I följande exempel anges nodtillhörigheten till *requiredUnderingSchedulingIgnoredUnderingExecution*. Den här tillhörigheten kräver att Kubernetes schema använder en nod med en matchande etikett. Om ingen nod är tillgänglig måste podden vänta på att schemaläggningen ska fortsätta. Om du vill att podden ska schemaläggas på en annan nod kan du i stället ange värdet till *preferredDuringSchedulingIgnoreUnderingExecution:*

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

Den *IgnoreradeUnderingExecution* delen av inställningen anger att om nodetiketterna ändras, bör pod inte vräkas från noden. Kubernetes-schemaläggaren använder endast de uppdaterade nodetiketterna för nya poddar som schemaläggs, inte poddar som redan schemalagts på noderna.

Mer information finns i [Tillhörighet och antitillhörighet][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Affinitet mellan pod och anti-affinitet

En slutlig metod för Kubernetes schemaläggaren att logiskt isolera arbetsbelastningar är att använda inter-pod tillhörighet eller anti-tillhörighet. Inställningarna definierar att poddar *inte ska* schemaläggas på en nod som har en befintlig matchande pod eller att de *ska* schemaläggas. Som standard försöker Kubernetes-schemaläggaren schemalägga flera poddar i en replikuppsättning över noder. Du kan definiera mer specifika regler kring det här beteendet.

Ett bra exempel är ett webbprogram som också använder en Azure Cache för Redis. Du kan använda pod anti-tillhörighetsregler för att begära att Kubernetes schemaläggaren distribuerar repliker över noder. Du kan sedan använda tillhörighetsregler för att se till att varje webbappkomponent schemaläggs på samma värd som en motsvarande cache. Fördelningen av poddar mellan noder ser ut som följande exempel:

| **Nod 1** | **Nod 2** | **Nod 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Det här exemplet är en mer komplex distribution än användningen av nodväljare eller nodtillhörighet. Distributionen ger dig kontroll över hur Kubernetes schemalägger poddar på noder och logiskt kan isolera resurser. Ett fullständigt exempel på det här webbprogrammet med Azure Cache for Redis,se [Samlokalisera poddar på samma nod][k8s-pod-affinity].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserade på avancerade Kubernetes schemaläggare funktioner. Mer information om klusteråtgärder i AKS finns i följande metodtips:

* [Flera innehavare och isolering av kluster][aks-best-practices-scheduler]
* [Grundläggande kubernetes-schemaläggare][aks-best-practices-scheduler]
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
[use-multiple-node-pools]: use-multiple-node-pools.md
