---
title: Skalbarheten i Azure Service Fabric nät appar | Microsoft Docs
description: Mer information om skalning tjänster i Azure Service Fabric-nät.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 1688cac35ea9de43bac529a4994bd4ea55eb0ab7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339095"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Skala Service Fabric-nät program

En av de största fördelarna med att distribuera program till Service Fabric Mesh är att du enkelt kan skala in eller ut dina tjänster. Detta hjälper dig att hantera varierande belastningsstorlekar på dina tjänster eller att förbättra tillgängligheten. Du kan manuellt skala ditt services in eller ut eller principer för automatisk skalning av installationsprogrammet.

## <a name="manual-scaling-instances"></a>Manuell skalning instanser

I distributionsmallen för programresursen har varje tjänst en *replicaCount*-egenskap som kan användas för att ange hur många gånger du vill att tjänsten distribueras. Ett program kan bestå av flera tjänster, och varje tjänst kan ha ett unikt *replicaCount*-värde, som distribueras och hanteras tillsammans. Om du vill skala antalet tjänstrepliker ändra du *replicaCount*-värdet för varje tjänst som du vill skala i distributionsmallen eller parameterfilen. Sedan uppgraderar du programmet.

Exempel på skala manuellt services-instanser finns [manuellt skala dina tjänster in eller ut](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Instanser av tjänsten för automatisk skalning
Automatisk skalning är en ytterligare funktion av Service Fabric dynamiskt skala antalet dina service-instanser (horisontell skalning). Automatisk skalning ger bra elasticitet och möjliggör etablering eller borttagning av instanser av tjänsten baserat på användning av CPU eller minne.  Automatisk skalning kan du köra rätt antal instanser av tjänsten för din arbetsbelastning och optimera för kostnaden.

Ett automatiskt skalningsprincip definieras per tjänst i resursfil för tjänsten. Varje skalningsprincip består av två delar:

- En skalning utlösare, som beskriver vid skalning av tjänsten kommer att utföras. Det finns tre faktorer som bestämmer när tjänsten skalas. *Lägre tröskelvärde* är ett värde som anger när tjänsten kommer att skalas i. Om den genomsnittliga belastningen över alla instanser av partitionerna är lägre än det här värdet, kommer tjänsten att skalas i. *Övre tröskelvärdet för inläsning* är ett värde som anger när tjänsten kommer att skalas ut. Om den genomsnittliga belastningen över alla instanser av partitionen är högre än det här värdet, kommer tjänsten att skalas ut. *Skalningsintervall* avgör hur ofta (i sekunder) utlösaren ska kontrolleras. När utlösaren kontrolleras om skalning krävs mekanismen används. Om skalning inte krävs vidtas någon åtgärd. I båda fallen kontrolleras utlösaren inte igen innan skalningsintervall upphör att gälla.

- En skalning mekanism, som beskriver hur skalning ska utföras när den utlöses. *Skala ökningen* avgör hur många instanser ska läggas till eller tas bort när mekanismen utlöses. *Maximalt instansantal* definierar den övre gränsen för skalning. Om antalet instanser når den här gränsen kan sedan skalas tjänsten inte ut oavsett belastningen. *Minsta instansantal* definierar den nedre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen kan sedan skalas tjänsten inte i oavsett belastningen.

Läs hur du ställer in en princip för automatisk skalning för din tjänst, [Autoskala services](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Nästa steg

Information om programmodellen finns [Service Fabric-resurser](service-fabric-mesh-service-fabric-resources.md)
