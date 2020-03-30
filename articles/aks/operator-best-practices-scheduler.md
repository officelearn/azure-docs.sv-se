---
title: Metodtips för operatör – grundläggande schemaläggarfunktioner i Azure Kubernetes Services (AKS)
description: Lär dig metodtips för klusteroperatören för att använda grundläggande schemaläggarfunktioner som resurskvoter och pod-avbrottsbudgetar i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cccc476a944b28d24c53a947e434d465c94f94ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126563"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Metodtips för grundläggande schemaläggarfunktioner i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes Service (AKS) måste du ofta isolera team och arbetsbelastningar. Kubernetes schemaläggaren innehåller funktioner som gör att du kan styra fördelningen av beräkningsresurser eller begränsa effekten av underhållshändelser.

Den här artikeln med metodtips fokuserar på grundläggande kubernetes schemaläggningsfunktioner för klusteroperatorer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Använd resurskvoter för att tillhandahålla en fast mängd resurser till team eller arbetsbelastningar
> * Begränsa effekten av planerat underhåll med hjälp av pod avbrott budgetar
> * Sök efter saknade pod resursbegäranden och gränser med hjälp av `kube-advisor` verktyget

## <a name="enforce-resource-quotas"></a>Framtvinga resurskvoter

**Vägledning för bästa praxis** - Planera och tillämpa resurskvoter på namnområdesnivå. Om poddar inte definierar resursbegäranden och begränsningar avvisar du distributionen. Övervaka resursanvändningen och justera kvoter efter behov.

Resursbegäranden och -gränser placeras i pod-specifikationen. Dessa gränser används av Kubernetes-schemaläggaren vid distributionen för att hitta en tillgänglig nod i klustret. Dessa gränser och önskemål fungerar på den individuella pod-nivån. Mer information om hur du definierar dessa värden finns i [Definiera pod resursbegäranden och gränser][resource-limits]

Om du vill reservera och begränsa resurser i ett utvecklingsteam eller ett utvecklingsprojekt bör du använda *resurskvoter*. Dessa kvoter definieras på ett namnområde och kan användas för att ange kvoter på följande grunder:

* **Beräkna resurser**, till exempel CPU och minne eller GPU: er.
* **Lagringsresurser**, inkluderar det totala antalet volymer eller mängden diskutrymme för en viss lagringsklass.
* **Objektantal**, till exempel maximalt antal hemligheter, tjänster eller jobb kan skapas.

Kubernetes överengagerar inte resurser. När den sammanlagda summan av resursbegäranden eller begränsningarna har passerat den tilldelade kvoten lyckas inga ytterligare distributioner.

När du definierar resurskvoter måste alla poddar som skapas i namnområdet ange begränsningar eller begäranden i pod-specifikationerna. Om de inte anger dessa värden kan du avvisa distributionen. I stället kan du [konfigurera standardbegäranden och standardgränser för ett namnområde][configure-default-quotas].

I följande exempel anger *YAML-manifestet dev-app-team-kvoter.yaml* en hård gräns på totalt *10* processorer, *20Gi* minne och *10* poddar:

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

Den här resurskvoten kan användas genom att ange namnområdet, till exempel *dev-apps:*

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Arbeta med programutvecklare och ägare för att förstå deras behov och tillämpa lämpliga resurskvoter.

Mer information om tillgängliga resursobjekt, scope och prioriteringar finns [i Resurskvoter i Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planera för tillgänglighet med hjälp av pod störningar budgetar

**Vägledning för bästa praxis** – Om du vill behålla tillgängligheten för program definierar du POD Disruption Budgets (PDBs) för att se till att ett minsta antal poddar är tillgängliga i klustret.

Det finns två störande händelser som gör att poddar tas bort:

* *Ofrivilliga störningar* är händelser utanför den typiska kontrollen av klusteroperatören eller programägaren.
  * Dessa ofrivilliga störningar inkluderar ett maskinvarufel på den fysiska datorn, en kernel-panik eller borttagning av en virtuell nod-VM
* *Frivilliga störningar* är händelser som begärs av klusteroperatören eller programägaren.
  * Dessa frivilliga avbrott omfattar klusteruppgraderingar, en uppdaterad distributionsmall eller att ta bort en pod av misstag.

De ofrivilliga störningarna kan mildras genom att använda flera repliker av dina poddar i en distribution. Köra flera noder i AKS-klustret hjälper också till med dessa ofrivilliga störningar. För frivilliga avbrott tillhandahåller Kubernetes *pod-avbrottsbudgetar* som gör att klusteroperatorn kan definiera ett minsta tillgängligt eller maximalt otillgängligt resursantal. Med de här pod-avbrottsbudgetarna kan du planera för hur distributioner eller replikuppsättningar svarar när en frivillig störningshändelse inträffar.

Om ett kluster ska uppgraderas eller en distributionsmall uppdateras, ser Kubernetes-schemaläggaren till att ytterligare poddar schemaläggs på andra noder innan de frivilliga avbrottshändelserna kan fortsätta. Schemaläggaren väntar innan en nod startas om tills det definierade antalet poddar har schemalagts på andra noder i klustret.

Låt oss titta på ett exempel på en replikuppsättning med fem poddar som kör NGINX. Poddarna i replikuppsättningen tilldelas `app: nginx-frontend`etiketten . Under en frivillig avbrottshändelse, till exempel en klusteruppgradering, vill du se till att minst tre poddar fortsätter att köras. Följande YAML-manifest för ett *PodDisruptionBudget-objekt* definierar dessa krav:

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

Du kan också definiera en procentsats, till exempel *60 %*, som gör att du automatiskt kan kompensera för replikuppsättningen för att skala upp antalet poddar.

Du kan definiera ett maximalt antal instanser som inte är tillgängliga i en replikuppsättning. Återigen kan en procentsats för de högsta otillgängliga poddar också definieras. Följande YAML-manifest för pod-avbrottsbudget definierar att högst två poddar i replikuppsättningen inte är tillgängliga:

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

När budgeten för avbrott i podden har definierats skapar du den i AKS-klustret som med alla andra Kubernetes-objekt:

```console
kubectl apply -f nginx-pdb.yaml
```

Arbeta med dina programutvecklare och ägare för att förstå deras behov och tillämpa lämpliga pod avbrott budgetar.

Mer information om hur du använder pod störningar budgetar, se [Ange en avbrott budget för ditt program][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Kontrollera regelbundet klusterproblem med kube-advisor

**Vägledning för bästa praxis** – Kör `kube-advisor` regelbundet den senaste versionen av open source-verktyget för att identifiera problem i klustret. Om du använder resurskvoter på ett `kube-advisor` befintligt AKS-kluster kör du först för att hitta poddar som inte har resursbegäranden och begränsningar definierade.

[Kube-advisor-verktyget][kube-advisor] är ett associerat AKS-projekt med öppen källkod som söker igenom ett Kubernetes-kluster och rapporterar om problem som hittas. En användbar kontroll är att identifiera poddar som inte har resursbegäranden och begränsningar på plats.

Verktyget kube-advisor kan rapportera om resursbegäran och gränser som saknas i PodSpecs för Windows-program samt Linux-program, men själva kube-advisor-verktyget måste schemaläggas på en Linux-pod. Du kan schemalägga en pod så att den körs på en nodpool med ett visst operativsystem med hjälp av en [nodväljare][k8s-node-selector] i podns konfiguration.

I ett AKS-kluster som är värd för flera utvecklingsteam och program kan det vara svårt att spåra poddar utan att dessa resursbegäranden och begränsningar har angetts. Det bästa är att `kube-advisor` regelbundet köras på AKS-kluster, särskilt om du inte tilldelar resurskvoter till namnområden.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserade på grundläggande Kubernetes schemaläggare funktioner. Mer information om klusteråtgärder i AKS finns i följande metodtips:

* [Flera innehavare och isolering av kluster][aks-best-practices-cluster-isolation]
* [Avancerade Kubernetes-schemaläggare][aks-best-practices-advanced-scheduler]
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
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
