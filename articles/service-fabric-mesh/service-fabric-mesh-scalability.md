---
title: Skalbarhet för Azure Service Fabric Mesh-appar
description: En av fördelarna med att distribuera program till Service Fabric Mesh är möjligheten att enkelt skala dina tjänster, antingen manuellt eller med principer för automatisk skalning.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259180"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Skalning Service Fabric Mesh-program

En av de största fördelarna med att distribuera program till Service Fabric Mesh är möjligheten för dig att enkelt skala dina tjänster in eller ut. Detta bör användas för att hantera varierande mängd belastning på dina tjänster, eller förbättra tillgängligheten. Du kan skala dina tjänster manuellt in eller ut eller konfigurera principer för automatisk skalning av automatiskt.

## <a name="manual-scaling-instances"></a>Manuell skalningsinstanser

I distributionsmallen för programresursen har varje tjänst en *replicaCount*-egenskap som kan användas för att ange hur många gånger du vill att tjänsten distribueras. Ett program kan bestå av flera tjänster, och varje tjänst kan ha ett unikt *replicaCount*-värde, som distribueras och hanteras tillsammans. Om du vill skala antalet tjänstrepliker ändra du *replicaCount*-värdet för varje tjänst som du vill skala i distributionsmallen eller parameterfilen. Sedan uppgraderar du programmet.

Exempel på manuellt skalningstjänsterinstanser finns [i Skala tjänsterna manuellt in eller ut](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Automatisk skalning av tjänstinstanser
Automatisk skalning är ytterligare en funktion i Service Fabric för att dynamiskt skala antalet tjänstinstanser (vågrät skalning). Automatisk skalning ger stor elasticitet och möjliggör etablering eller borttagning av tjänstinstanser baserat på CPU- eller minnesanvändning.  Med automatisk skalning kan du köra rätt antal tjänstinstanser för din arbetsbelastning och optimera för kostnad.

En princip för automatisk skalning definieras per tjänst i tjänstresursfilen. Varje skalningsprincip består av två delar:

- En skalningsutlösare som beskriver när skalning av tjänsten ska utföras. Det finns tre faktorer som avgör när tjänsten ska skalas. *Lägre inläsningströskel* är ett värde som bestämmer när tjänsten ska skalas in. Om den genomsnittliga belastningen för alla instanser av partitionerna är lägre än det här värdet skalas tjänsten in. *Övre inläsningströskel* är ett värde som bestämmer när tjänsten ska skalas ut. Om den genomsnittliga belastningen för alla instanser av partitionen är högre än det här värdet skalas tjänsten ut. *Skalningsintervallet* avgör hur ofta (i sekunder) utlösaren ska kontrolleras. När utlösaren har markerats, om skalning behövs, kommer mekanismen att tillämpas. Om skalning inte behövs, kommer ingen åtgärd att vidtas. I båda fallen kontrolleras inte utlösaren igen innan skalningsintervallet går ut.

- En skalningsmekanism som beskriver hur skalning ska utföras när den utlöses. *Skala steg* avgör hur många instanser som ska läggas till eller tas bort när mekanismen utlöses. *Maximalt antal instanser* definierar den övre gränsen för skalning. Om antalet instanser når den här gränsen skalas tjänsten inte ut oavsett belastning. *Minsta antal instanser* definierar den nedre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen skalas inte tjänsten in oavsett belastningen.

Om du vill veta hur du anger en princip för automatisk skalning för tjänsten läser du [tjänster för automatisk skalning](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Nästa steg

Information om programmodellen finns i [Service Fabric-resurser](service-fabric-mesh-service-fabric-resources.md)
