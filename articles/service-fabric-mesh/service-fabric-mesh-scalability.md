---
title: Skalbarhet för Azure Service Fabric nätappar
description: En av fördelarna med att distribuera program till Service Fabric nät är möjligheten att enkelt skala dina tjänster, antingen manuellt eller med principer för automatisk skalning.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458992"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Skala Service Fabric nätprogram

En av de största fördelarna med att distribuera program till Service Fabric nät är möjligheten att enkelt skala dina tjänster i eller ut. Detta bör användas för att hantera olika belastnings mängder på dina tjänster eller förbättra tillgängligheten. Du kan skala dina tjänster manuellt i eller ut eller konfigurera principer för automatisk skalning.

## <a name="manual-scaling-instances"></a>Manuella skalnings instanser

I distributionsmallen för programresursen har varje tjänst en *replicaCount*-egenskap som kan användas för att ange hur många gånger du vill att tjänsten distribueras. Ett program kan bestå av flera tjänster, och varje tjänst kan ha ett unikt *replicaCount*-värde, som distribueras och hanteras tillsammans. Om du vill skala antalet tjänstrepliker ändra du *replicaCount*-värdet för varje tjänst som du vill skala i distributionsmallen eller parameterfilen. Sedan uppgraderar du programmet.

Exempel på manuell skalning av tjänst instanser finns i [manuellt skala dina tjänster i eller ut](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Tjänst instanser för automatisk skalning
Automatisk skalning är en ytterligare funktion i Service Fabric för att dynamiskt skala antalet tjänst instanser (vågrät skalning). Automatisk skalning ger bra elasticitet och möjliggör etablering eller borttagning av instanser av tjänsten baserat på användning av CPU eller minne.  Med automatisk skalning kan du köra rätt antal tjänst instanser för din arbets belastning och optimera för kostnad.

En princip för automatisk skalning definieras per tjänst i tjänst resurs filen. Varje skalnings princip består av två delar:

- En skalnings utlösare som beskriver när skalningen av tjänsten ska utföras. Det finns tre faktorer som avgör när tjänsten ska skalas. *Lägre tröskelvärde för inläsning* är ett värde som avgör när tjänsten ska skalas i. Om den genomsnittliga belastningen för alla instanser av partitionerna är lägre än det här värdet kommer tjänsten att skalas i. Det *övre tröskelvärdet för inläsning* är ett värde som anger när tjänsten ska skalas ut. Om den genomsnittliga belastningen för alla instanser av partitionen är högre än det här värdet kommer tjänsten att skalas ut. *Skalnings intervallet* avgör hur ofta (i sekunder) utlösaren ska kontrol leras. När utlösaren är markerad kommer mekanismen att tillämpas om skalning krävs. Om skalning inte behövs vidtas ingen åtgärd. I båda fallen kontrol leras inte utlösaren igen innan skalnings intervallet upphör att gälla.

- En skalnings funktion som beskriver hur skalningen ska utföras när den utlöses. Med *skalnings ökningen* bestäms hur många instanser som ska läggas till eller tas bort när mekanismen utlöses. *Maximalt antal instanser* definierar den övre gränsen för skalning. Om antalet instanser når den här gränsen kommer tjänsten inte att skalas ut oavsett belastningen. *Lägsta instans antal* definierar den nedre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen kommer tjänsten inte att skalas i oberoende av belastningen.

Läs [tjänsterna för autoskalning](service-fabric-mesh-howto-auto-scale-services.md)för att lära dig hur du ställer in en princip för autoskalning för tjänsten.

## <a name="next-steps"></a>Nästa steg

Information om program modellen finns [Service Fabric resurser](service-fabric-mesh-service-fabric-resources.md)
