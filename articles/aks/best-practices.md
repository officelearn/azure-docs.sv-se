---
title: Metodtips för Azure Kubernetes Service (AKS)
description: Insamling av metodtips för klusteroperatör och utvecklare för att skapa och hantera program i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 627eeffd18a479486e5a682da06bf89cd5f8f0e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596325"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Metodtips för klusteroperatör och utvecklare för att skapa och hantera program på Azure Kubernetes Service (AKS)

För att skapa och köra program som framgångsrikt i Azure Kubernetes Service (AKS) finns det några viktiga överväganden att förstå och implementera. Dessa områden omfattar funktioner för flera innehavare och schemaläggare, kluster- och pod-säkerhet eller kontinuitet i verksamheten och haveriberedskap. Följande metodtips är grupperade för att hjälpa klusteroperatörer och utvecklare att förstå övervägandena för vart och ett av dessa områden och implementera lämpliga funktioner.

Dessa bästa praxis och konceptuella artiklar har skrivits tillsammans med AKS produktgrupp, ingenjörsteam och fältteam, inklusive globala svarta bälten (GBBs).

## <a name="cluster-operator-best-practices"></a>Metodtips för klusteroperatör

Som klusteroperator arbetar du tillsammans med programägare och utvecklare för att förstå deras behov. Du kan sedan använda följande metodtips för att konfigurera AKS-kluster efter behov.

**Flera innehavare**

* [Metodtips för isolering av kluster](operator-best-practices-cluster-isolation.md)
    * Innehåller kärnkomponenter med flera innehavare och logisk isolering med namnområden.
* [Metodtips för grundläggande schemaläggningsfunktioner](operator-best-practices-scheduler.md)
    * Inkluderar användning av resurskvoter och pod avbrott budgetar.
* [Metodtips för avancerade schemaläggningsfunktioner](operator-best-practices-advanced-scheduler.md)
    * Inkluderar användning av färg och tolerationer, nodväljare och tillhörighet samt tillhörighet mellan pod och anti-affinitet.
* [Metodtips för autentisering och auktorisering](operator-best-practices-identity.md)
    * Inkluderar integrering med Azure Active Directory med rollbaserade åtkomstkontroller (RBAC) och pod-identiteter.

**Säkerhet**

* [Metodtips för klustersäkerhet och uppgraderingar](operator-best-practices-cluster-security.md)
    * Inkluderar att skydda åtkomsten till API-servern, begränsa behållaråtkomsten och hantera uppgraderingar och omstarter av nod.
* [Metodtips för hantering och säkerhet för behållaravbildningar](operator-best-practices-container-image-management.md)
    * Inkluderar att skydda avbildningen och körningen och automatiserade versioner av basavbildningsuppdateringar.
* [Metodtips för pod säkerhet](developer-best-practices-pod-security.md)
    * Inkluderar att skydda åtkomsten till resurser, begränsa exponeringen för autentiseringsuppgifter och använda pod-identiteter och digitala nyckelvalv.

**Nätverk och lagring**

* [Metodtips för nätverksanslutning](operator-best-practices-network.md)
    * Innehåller olika nätverksmodeller, med hjälp av ingress- och webbprogramsbrandvägger (WAF) och sã¤tst nod SSH-åtkomst.
* [Metodtips för lagring och säkerhetskopiering](operator-best-practices-storage.md)
    * Inkluderar att välja lämplig lagringstyp och nodstorlek, dynamiskt etableringsvolymer och säkerhetskopiering av data.

**Kör arbetsbelastningar som är redo för företag**

* [Bästa praxis för kontinuitet i verksamheten och katastrofåterställning](operator-best-practices-multi-region.md)
    * Inkluderar användning av regionpar, flera kluster med Azure Traffic Manager och geo-replikering av behållaravbildningar.

## <a name="developer-best-practices"></a>Metodtips för utvecklare

Som utvecklare eller programägare kan du förenkla din utvecklingsupplevelse och definiera behov av programprestanda.

* [Metodtips för programutvecklare för resurshantering](developer-best-practices-resource-management.md)
    * Inkluderar att definiera pod resursbegäranden och gränser, konfigurera utvecklingsverktyg och söka efter programproblem.
* [Metodtips för pod säkerhet](developer-best-practices-pod-security.md)
    * Inkluderar att skydda åtkomsten till resurser, begränsa exponeringen för autentiseringsuppgifter och använda pod-identiteter och digitala nyckelvalv.

## <a name="kubernetes--aks-concepts"></a>Kubernetes / AKS begrepp

För att förstå några av funktionerna och komponenterna i dessa metodtips kan du även se följande konceptuella artiklar för kluster i Azure Kubernetes Service (AKS):

* [Kubernetes kärnkoncept](concepts-clusters-workloads.md)
* [Åtkomst och identitet](concepts-identity.md)
* [Säkerhetskoncept](concepts-security.md)
* [Nätverkskoncept](concepts-network.md)
* [Lagringsalternativ](concepts-storage.md)
* [Skalningsalternativ](concepts-scale.md)

## <a name="next-steps"></a>Nästa steg

Om du behöver komma igång med AKS följer du en av snabbstartarna för att distribuera ett AKS-kluster (Azure Kubernetes Service) med [Azure CLI-](kubernetes-walkthrough.md) eller [Azure-portalen](kubernetes-walkthrough-portal.md).
