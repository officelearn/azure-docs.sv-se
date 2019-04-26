---
title: Operatorn metodtips – isolering av kluster i Azure Kubernetes Services (AKS)
description: Läs kluster operatorn metodtipsen för isolering i Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 11/26/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 94aaa72497a8a5f171d6b42f59a3c5b507c71492
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465314"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Metodtips för isolering av kluster i Azure Kubernetes Service (AKS)

Du hanterar kluster i Azure Kubernetes Service (AKS) bör behöva du ofta isolera team och arbetsbelastningar. AKS ger flexibilitet i hur du kör kluster för flera innehavare och isolera resurser. Dessa funktioner för flera innehavare och isolering bör tolkas och implementeras för att maximera din investering i Kubernetes.

Den här bästa praxis-artikeln handlar om isolering för klusteroperatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Planera för flera innehavare kluster och uppdelning av resurser
> * Använd logisk eller fysisk isolering i AKS-kluster

## <a name="design-clusters-for-multi-tenancy"></a>Design-kluster för flera innehavare

Kubernetes tillhandahåller funktioner som gör att du logiskt isolera team och arbetsbelastningar i samma kluster. Målet ska vara att ange minst antal privilegier, begränsad till de resurser som varje team behöver. En [Namespace] [ k8s-namespaces] i Kubernetes skapar du en logisk isoleringsgräns. Ytterligare kubernetes funktioner och överväganden för isolering och flera innehavare innehåller följande områden:

* **Schemaläggning** användning av grundläggande funktioner som resurskvoter och pod avbrott budgetar. Mer information om dessa funktioner finns i [bästa praxis för grundläggande scheduler funktioner i AKS][aks-best-practices-scheduler].
  * Mer avancerade funktioner för scheduler är taints och tolerations, noden väljare, och noden och pod tillhörighet eller anti-tillhörighet. Mer information om dessa funktioner finns i [bästa praxis för avancerade scheduler funktioner i AKS][aks-best-practices-advanced-scheduler].
* **Nätverk** användning av nätverksprinciper för att styra trafikflödet till och från poddar.
* **Autentisering och auktorisering** innehålla användare av rollbaserad åtkomstkontroll (RBAC) och Azure Active Directory (AD)-integrering, pod identiteter och hemligheter i Azure Key Vault. Mer information om dessa funktioner finns i [bästa praxis för autentisering och auktorisering i AKS][aks-best-practices-identity].
* **Behållare** omfattar pod säkerhetsprinciper, pod security kontexter, genomsökning avbildningar och körningar för sårbarheter. Omfattar även använder App Armor eller Seccomp (säker databehandling) för att begränsa åtkomst till behållare till den underliggande noden.

## <a name="logically-isolate-clusters"></a>Logiskt isolera kluster

**Bästa praxis riktlinjer** -använder logisk isolering för att avgränsa team och projekt. Försök att minska antalet fysiska AKS-kluster som du distribuerar till isolera team eller program.

Ett AKS-kluster kan användas för flera arbetsbelastningar, team eller miljöer med logisk isolering. Kubernetes [namnområden] [ k8s-namespaces] utgör en gräns för logisk isolering för arbetsbelastningar och resurser.

![Logisk isolering för ett Kubernetes-kluster i AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logisk uppdelning av kluster ger vanligtvis en högre densitet pod än fysiskt isolerat kluster. Det finns mindre beräkning överkapacitet vilken inaktiv i klustret. När de kombineras med Kubernetes-kluster autoskalningen, kan du skala antalet noder upp eller ned uppfylla kraven. Den här bästa praxis för autoskalning kan du köra endast antalet noder som krävs och minimerar kostnaderna.

Kubernetes-miljöer i AKS eller någon annanstans, är inte helt säker för fientlig användning med flera innehavare. Ytterligare säkerhetsfunktioner som *Pod säkerhetsprincip* och mer detaljerade rollbaserade åtkomstkontroller (RBAC) för noder försvåra kryphål. Men är SANT säkerhet vid körning av fientlig arbetsbelastningar för flera innehavare, ett hypervisor-program endast säkerhetsnivå som du ska lita på. Säkerhetsdomän för Kubernetes blir hela klustret, inte en enskild nod. Du bör använda fysiskt isolerat kluster för dessa typer av fientlig arbetsbelastningar för flera innehavare.

## <a name="physically-isolate-clusters"></a>Isolera fysiskt kluster

**Bästa praxis riktlinjer** – minska användningen av fysisk isolering för varje separat team eller programdistribution. Använd i stället *logiska* isolering, enligt beskrivningen i föregående avsnitt.

Ett vanligt sätt att klustret isolering är att använda fysiskt separata AKS-kluster. I den här isoleringsmodell tilldelas team eller arbetsbelastningar som sina egna AKS-kluster. Den här metoden kan du ofta ser ut som det enklaste sättet att isolera arbetsbelastningar eller team, men lägger till ytterligare hantering och finansiella kostnader. Nu måste hantera dessa flera kluster och måste separat ger åtkomst och tilldela behörigheter. Du faktureras också för de enskilda noderna.

![Fysisk isolering av enskilda Kubernetes-kluster i AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fysiskt separata kluster har vanligtvis ett låga pod densitet. Eftersom varje team eller en arbetsbelastning har sina egna AKS-kluster kan är klustret ofta överetablerade med beräkningsresurser. Ofta, kommer ett litet antal poddar på dessa noder. Outnyttjad kapacitet på noderna kan inte användas för program eller tjänster i utveckling av andra team. Dessa ytterligare resurser bidra till ytterligare kostnader i fysiskt separata kluster.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på klustret isolering. Mer information om kluster i AKS finns i följande metoder:

* [Grundläggande funktioner som Kubernetes scheduler][aks-best-practices-scheduler]
* [Avancerade funktioner för Kubernetes scheduler][aks-best-practices-advanced-scheduler]
* [Autentisering och auktorisering][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md