---
title: Operatorn metodtips – grundläggande scheduler funktioner i Azure Kubernetes Services (AKS)
description: Läs kluster operatorn metodtipsen för att använda grundläggande scheduler-funktioner, till exempel resurskvoter och pod avbrott budgetar i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 8233330973946e552e36a85a11bdbbfb06c739f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58178143"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Metodtips för grundläggande scheduler funktioner i Azure Kubernetes Service (AKS)

Du hanterar kluster i Azure Kubernetes Service (AKS) bör behöva du ofta isolera team och arbetsbelastningar. Scheduler Kubernetes tillhandahåller funktioner som låter dig styra distributionen av beräkningsresurser eller begränsa effekten av underhållshändelser.

Den här bästa praxis artikeln handlar om grundläggande Kubernetes schemaläggningsfunktioner för klusteroperatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Använda resurskvoter för att tillhandahålla en fast mängd resurser till team eller arbetsbelastningar
> * Begränsa effekten av schemalagt underhåll med hjälp av pod avbrott budgetar
> * Sök efter saknade pod resursbegäranden och begränsningar med hjälp av den `kube-advisor` verktyget

## <a name="enforce-resource-quotas"></a>Framtvinga resurskvoter

**Bästa praxis riktlinjer** -Plan och tillämpa resurskvoter på namnområdesnivå. Om poddar inte definierar resursbegäranden och begränsningar, avvisa distributionen. Övervaka Resursanvändning och justera kvoter efter behov.

Resursbegäranden och begränsningar är placerade i pod-specifikationen. Dessa gränser som används av scheduler Kubernetes vid tidpunkten för distribution för att hitta en tillgänglig nod i klustret. Dessa begränsningar och begäranden fungerar på enskilda pod-nivå. Mer information om hur du definierar dessa värden finns i [definiera pod resursbegäranden och begränsningar][resource-limits]

För att ge ett sätt att reservera och begränsa resurser över ett utvecklingsteam eller projekt, bör du använda *resurskvoter*. Dessa kvoter har definierats för ett namnområde och kan användas för att ange kvoter för följande:

* **Beräkningsresurser**, till exempel CPU och minne eller GPU: er.
* **Lagringsresurser**, innehåller det totala antalet volymer och hur mycket diskutrymme för en angiven klass.
* **Objektantal**, t.ex högsta tillåtna antalet hemligheter, tjänster eller jobb kan skapas.

Kubernetes överanstränga inte resurser. När totalen antalet resursbegäranden om eller gränser klarar den tilldelade kvoten, har inga ytterligare distributioner lyckats.

När du definierar resurskvoter måste alla poddar som skapats i namnområdet ange gränser eller begäranden i sina pod-specifikationer. Om de inte anger dessa värden kan avvisa du distributionen. I stället kan du [konfigurera standard-begäranden och gränser för ett namnområde][configure-default-quotas].

Följande exempel YAML manifestet med namnet *dev-app-team-quotas.yaml* anger en hård gräns på totalt *10* processorer, *20Gi* minne och *10*poddarna:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Den här resurskvoten kan användas genom att ange namnområdet, som *dev-apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Arbeta med programutvecklare och ägare för att förstå deras behov och tillämpa lämpliga resurskvoter.

Läs mer om tillgängliga resursobjekt, omfång och prioritet, [resurskvoter i Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planera för tillgänglighet med pod avbrott budgetar

**Bästa praxis riktlinjer** – för att upprätthålla tillgängligheten för program, definiera Pod avbrott budgetar (PDB-filer) för att se till att ett minsta antal poddar är tillgängliga i klustret.

Det finns två störande händelser som orsakar poddar som ska tas bort:

* *Antal ofrivilliga avbrott* är händelser utöver vanliga kontrollen av operatorn kluster eller programmets ägare.
  * Dessa ofrivilliga avbrott inkludera ett maskinvarufel på den fysiska datorn, en kernel panic-meddelande eller borttagningen av en nod VM
* *Frivillig avbrott* är händelser som begärs av operatorn kluster eller programmets ägare.
  * Dessa frivillig avbrott vara klusteruppgradering, en uppdaterad Distributionsmall eller av misstag tar bort en pod.

Antal ofrivilliga avbrott kan undvikas genom att använda flera kopior av dina poddar i en distribution. Köra flera noder i AKS-klustret hjälper också med de här ofrivilliga avbrott. För frivillig avbrott, Kubernetes tillhandahåller *pod avbrott budgetar* som gör att klustret operatorn definiera ett minsta tillgängliga eller högsta otillgänglig resursantalet. Dessa pod avbrott budgetar kan du planera för hur distributioner eller replikuppsättningar svarar när en frivillig avbrott inträffar.

Om ett kluster som ska uppgraderas eller en Distributionsmall uppdateras, Kubernetes-scheduler gör att ytterligare är poddar schemalagda på andra noder innan händelserna frivillig avbrott kan fortsätta. Scheduler väntar innan en nod startas om förrän det angivna antalet poddar är schemalagt på andra noder i klustret.

Nu ska vi titta på ett exempel på en replik med fem poddar som kör NGINX. Poddarna i repliken ange som tilldelas etiketten `app: nginx-frontend`. När frivillig avbrott händelser, till exempel en uppgradering av klustret, som du vill kontrollera att minst tre poddar fortsätter att köras. Följande YAML manifest för en *PodDisruptionBudget* objektet definierar dessa krav:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

Du kan också definiera en procentsats, till exempel *60%*, vilket gör att du automatiskt kompensera för repliken ange skala upp antalet poddar.

Du kan definiera ett maximalt antal tillgängliga instanser i en replikuppsättning. Igen, kan också procent för maximal otillgänglig poddarna definieras. Följande pod avbrott budget YAML manifestet definierar att mer än två poddar i replikeringen vara otillgänglig:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

När din pod avbrott budget har definierats kan skapa du den i AKS-klustret precis som med andra Kubernetes-objekt:

```console
kubectl apply -f nginx-pdb.yaml
```

Arbeta med programutvecklare och ägare för att förstå deras behov och tillämpa lämpliga pod avbrott budgetar.

Läs mer om hur du använder pod avbrott budgetar [anger en avbrott budget för ditt program][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Kontrollera regelbundet kluster problem med kube-advisor

**Bästa praxis riktlinjer** -regelbundet kör den senaste versionen av `kube-advisor` verktyg med öppen källkod att identifiera problem i klustret. Om du använder resurskvoter i ett befintligt AKS-kluster, kör `kube-advisor` först för att hitta poddar som inte har resursbegäranden och gränser som definierats.

Den [kube-advisor] [ kube-advisor] verktyget är ett projekt med associerade AKS öppen källkod som genomsöker ett Kubernetes-kluster och rapporter på problem som hittas. En användbar check är att identifiera poddar som inte har resursbegäranden och begränsningar på plats.

I ett AKS-kluster som är värd för flera utvecklingsteam och program, kan det vara svårt att spåra poddar utan dessa resurs-begäranden och begränsar uppsättningen. Som bästa praxis, regelbundet kör `kube-advisor` på dina AKS-kluster, särskilt om du inte tilldelar resurskvoter till namnområden.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på grundläggande funktioner som Kubernetes scheduler. Mer information om kluster i AKS finns i följande metoder:

* [Isolering för flera innehavare och kluster][aks-best-practices-cluster-isolation]
* [Avancerade funktioner för Kubernetes scheduler][aks-best-practices-advanced-scheduler]
* [Autentisering och auktorisering][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
