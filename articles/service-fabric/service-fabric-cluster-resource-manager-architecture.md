---
title: Arkitektur för Resurshanteraren
description: En översikt över och arkitektonisk information om Azure Service Fabric Cluster Resource Manager-tjänsten.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 94ed906533d108081d620e9b183ecfee249d85ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551700"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Arkitekturöversikt för klusterresurshanteraren
Service Fabric Cluster Resource Manager är en central tjänst som körs i klustret. Den hanterar önskat tillstånd för tjänsterna i klustret, särskilt när det gäller resursförbrukning och eventuella placeringsregler. 

Om du vill hantera resurserna i klustret måste Service Fabric Cluster Resource Manager ha flera informationsdelar:

- Vilka tjänster finns för närvarande
- Varje tjänsts aktuella (eller standard) resursförbrukning 
- Återstående klusterkapacitet 
- Kapaciteten för noderna i klustret 
- Mängden resurser som förbrukas på varje nod

Resursförbrukningen för en viss tjänst kan ändras med tiden och tjänster bryr sig vanligtvis om mer än en typ av resurs. Mellan olika tjänster kan det finnas både verkliga fysiska och fysiska resurser som mäts. Tjänster kan spåra fysiska mått som minne och diskförbrukning. Vanligare kan tjänster bry sig om logiska mått - saker som "WorkQueueDepth" eller "TotalRequests". Både logiska och fysiska mått kan användas i samma kluster. Mått kan delas mellan många tjänster eller vara specifika för en viss tjänst.

## <a name="other-considerations"></a>Andra överväganden
Ägare och operatörer av klustret kan skilja sig från tjänsten och författare ansökan, eller åtminstone är samma personer som bär olika hattar. När du utvecklar ditt program vet du några saker om vad som krävs. Du har en uppskattning av de resurser som den kommer att förbruka och hur olika tjänster ska distribueras. Webbnivån måste till exempel köras på noder som är exponerade för Internet, medan databastjänsterna inte bör. Som ett annat exempel begränsas förmodligen webbtjänsterna av CPU och nätverk, medan datanivåtjänsterna bryr sig mer om minne och diskförbrukning. Den person som hanterar en live-site-incident för den tjänsten i produktion, eller som hanterar en uppgradering till tjänsten har dock ett annat jobb att utföra och kräver olika verktyg. 

Både klustret och tjänsterna är dynamiska:

- Antalet noder i klustret kan växa och krympa
- Noder av olika storlekar och typer kan komma och gå
- Tjänster kan skapas, tas bort och ändra sina önskade resursallokeringar och placeringsregler
- Uppgraderingar eller andra hanteringsåtgärder kan rulla genom klustret på programmet på infrastrukturnivåer
- Fel kan inträffa när som helst.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Komponenter och dataflöde för klusterresurshanteraren
Klusterresurshanteraren måste spåra kraven för varje tjänst och förbrukningen av resurser av varje tjänstobjekt inom dessa tjänster. Klusterresurshanteraren har två begreppsmässiga delar: agenter som körs på varje nod och en feltolerant tjänst. Agenterna på varje nodspårsbelastningsrapporter från tjänster, aggregera dem och regelbundet rapportera dem. Tjänsten Cluster Resource Manager sammanställer all information från de lokala agenterna och reagerar baserat på dess aktuella konfiguration.

Låt oss titta på följande diagram:

<center>

![Resursbalanserararkitektur][Image1]
</center>

Under körningen finns det många förändringar som kan hända. Anta till exempel hur mycket resurser som vissa tjänster förbrukar ändringar, vissa tjänster misslyckas och vissa noder ansluter och lämnar klustret. Alla ändringar på en nod aggregeras och skickas regelbundet till tjänsten Cluster Resource Manager (1,2) där de aggregeras igen, analyseras och lagras. Med några sekunders mellanrum tittar tjänsten på ändringarna och avgör om några åtgärder är nödvändiga (3). Det kan till exempel märkas att vissa tomma noder har lagts till i klustret. Som ett resultat bestämmer den sig för att flytta vissa tjänster till dessa noder. Klusterresurshanteraren kan också märka att en viss nod är överbelastad eller att vissa tjänster har misslyckats eller tagits bort, vilket frigör resurser någon annanstans.

Låt oss titta på följande diagram och se vad som händer härnäst. Anta att Klusterresurshanteraren fastställer att ändringar är nödvändiga. Den samordnar med andra systemtjänster (särskilt Redundanshanteraren) för att göra nödvändiga ändringar. Därefter skickas de nödvändiga kommandona till lämpliga noder (4). Anta till exempel att Resurshanteraren märkte att Node5 var överbelastad och därför bestämde sig för att flytta tjänst B från nod5 till nod4. I slutet av omkonfigureringen (5) ser klustret ut så här:

<center>

![Resursbalanserararkitektur][Image2]
</center>

## <a name="next-steps"></a>Nästa steg
- Klusterresurshanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om dem kan du läsa den här artikeln om [hur du beskriver ett Service Fabric-kluster](./service-fabric-cluster-resource-manager-cluster-description.md)
- Klusterresurshanterarens primära uppgifter är att ombalansera klustret och tillämpa placeringsregler. Mer information om hur du konfigurerar dessa beteenden finns i [balansera ditt Service Fabric-kluster](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
