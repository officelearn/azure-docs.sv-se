---
title: Metodtips för Azure Kubernetes Service (AKS)
description: Insamling av klustret operatorn och utvecklare metodtipsen för att skapa och hantera program i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 12/07/18
ms.author: iainfou
ms.openlocfilehash: ef7dabc8bf792893380b80458d5fb20aa15cd909
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256476"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Metodtips för kluster-operatorn och utvecklare att bygga och hantera program på Azure Kubernetes Service (AKS)

Om du vill skapa och köra program har i Azure Kubernetes Service (AKS), finns det några viktiga saker att förstå och implementera. Dessa områden är flera innehavare och scheduler funktioner, kluster och pod säkerhet eller affärskontinuitet och katastrofåterställning. Följande metodtips grupperas för att hjälpa operatörer att klustret och utvecklare förstå överväganden för var och en av dessa områden och implementera lämpliga funktioner.

Dessa metodtips och konceptuella artiklar har skrivits tillsammans med AKS-produktgruppen, tekniska team och fältet team, inklusive globala svart bälten (GBBs).

## <a name="cluster-operator-best-practices"></a>Metodtips för kluster-operator

Arbeta tillsammans med programägare och utvecklare för att förstå deras behov som ansvarig för klustret. Du kan sedan använda följande metoder för att konfigurera dina AKS-kluster efter behov.

**Flera innehavare**

* [Metodtips för isolering av kluster](operator-best-practices-cluster-isolation.md)
    * Innehåller flera innehavare kärnkomponenter och logisk isolering med namnområden.
* [Metodtips för grundläggande scheduler-funktioner](operator-best-practices-scheduler.md)
    * Innehåller med resurskvoter och pod avbrott budgetar.
* [Metodtips för avancerade scheduler-funktioner](operator-best-practices-advanced-scheduler.md)
    * Innehåller med taints och tolerations, noden väljare och tillhörighet, och kommunikation mellan pod tillhörighet och anti-tillhörighet.
* [Metodtips för autentisering och auktorisering](operator-best-practices-identity.md)
    * Innefattar integrering med Azure Active Directory, med hjälp av rollbaserad åtkomstkontroll (RBAC) och pod-identiteter.

**Säkerhet**

* [Metodtips för Klustersäkerhet och uppgraderingar](operator-best-practices-cluster-security.md)
    * Innehåller skydda åtkomst till API-servern, begränsa åtkomst till behållare och hantera uppgraderingar och omstarter av noden.
* [Metodtips för hantering av behållare avbildningen och säkerhet](operator-best-practices-container-image-management.md)
    * Innehåller skydda avbildningen och körningar, med betrodda register och automatiserade versioner på basavbildningen uppdateringar...
* [Metodtips för pod-säkerhet](developer-best-practices-pod-security.md)
    * Omfattar att skydda åtkomsten till resurser, begränsa exponering av autentiseringsuppgifter och använder pod identiteter och digital nyckelvalv.

**Nätverk och lagring**

* [Metodtips för nätverksanslutning](operator-best-practices-network.md)
    * Innehåller olika nätverk modeller, med hjälp av in- och web brandväggar för webbprogram (WAF) och skydda noden SSH-åtkomst.
* [Metodtips för lagring och säkerhetskopiering](operator-best-practices-storage.md)
    * Innehåller välja rätt typ och noden lagringsstorleken, dynamiskt etablera volymer och säkerhetskopior.

**Arbetsbelastningar för företag**

* [Metodtips för affärskontinuitet och haveriberedskap](operator-best-practices-multi-region.md)
    * Innehåller med regionpar, flera kluster med Azure Traffic Manager och geo-replikering för behållaravbildningar.

## <a name="developer-best-practices"></a>Metodtips för utvecklare

Som utvecklare eller programmets ägare, du kan förenkla din utvecklingsupplevelse och definiera kräver prestanda programbehov.

* [Metodtips för programutvecklare att hantera resurser](developer-best-practices-resource-management.md)
    * Innehåller definiera pod resursbegäranden och gränser, konfigurerar utvecklingsverktyg och söker efter programfel.
* [Metodtips för pod-säkerhet](developer-best-practices-pod-security.md)
    * Omfattar att skydda åtkomsten till resurser, begränsa exponering av autentiseringsuppgifter och använder pod identiteter och digital nyckelvalv.

## <a name="kubernetes--aks-concepts"></a>Kubernetes / AKS-begrepp

För att förstå några av funktionerna och komponenter i dessa metodtips, kan du också se följande konceptuella artiklar för kluster i Azure Kubernetes Service (AKS):

* [Grundläggande begrepp som Kubernetes](concepts-clusters-workloads.md)
* [Åtkomst och identitet](concepts-identity.md)
* [Säkerhetskoncept](concepts-security.md)
* [Network-koncept](concepts-network.md)
* [Lagringsalternativ](concepts-storage.md)
* [Skalningsalternativ](concepts-scale.md)

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med AKS följer du någon av Snabbstart för att distribuera ett Azure Kubernetes Service (AKS) med den [Azure CLI](kubernetes-walkthrough.md) eller [Azure-portalen](kubernetes-walkthrough-portal.md).
