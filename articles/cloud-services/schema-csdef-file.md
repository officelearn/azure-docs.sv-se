---
title: Azure Cloud Services Definition Schema (.csdef-fil) | Microsoft Docs
ms.custom: 
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: "42"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: b833fdc06e4193c1b478028733c336feb6d8b9ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Schemat för Azure Cloud Services Definition (.csdef-fil)
Tjänstdefinitionsfilen definierar tjänstmodellen för ett program. Filen innehåller definitionerna för de roller som är tillgängliga för en tjänst i molnet, anger Tjänsteslutpunkter och upprättar konfigurationsinställningarna för tjänsten. Inställningen konfigurationsvärden ställs i tjänstkonfigurationsfilen, enligt beskrivningen av den [molntjänst (klassisk) Konfigurationsschemat](http://msdn.microsoft.com/library/b1ae68cd-cc95-48cb-a4a4-da91dc708a35).

Som standard installeras schemat för Azure-diagnostik konfigurationsfilen till den `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` directory. Ersätt `<version>` med den installerade versionen av den [Azure SDK](http://www.windowsazure.com/develop/downloads/).

Standardfilnamnstillägget för tjänstdefinitionsfilen är .csdef.

## <a name="basic-service-definition-schema"></a>Grundläggande service definition schema
Tjänstdefinitionsfilen måste innehålla ett `ServiceDefinition` element. Tjänstdefinitionen måste innehålla minst en roll (`WebRole` eller `WorkerRole`) element. Det kan innehålla upp till 25 roller som definierats i en enda definition och du kan blanda rolltyper. Tjänstdefinitionen innehåller även den valfria `NetworkTrafficRules` element som begränsar vilka roller kan kommunicera med angivna interna slutpunkter. Tjänstdefinitionen innehåller även den valfria `LoadBalancerProbes` element som innehåller kunden definierats hälsoavsökningar slutpunkter.

Det grundläggande formatet för tjänstdefinitionsfilen är som följer.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Schemadefinitioner
I följande avsnitt beskrivs schemat:

- [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md)
- [WebRole-schema](schema-csdef-webrole.md)
- [WorkerRole-schema](schema-csdef-workerrole.md)
- [NetworkTrafficRules-schema](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a>ServiceDefinition Element
Den `ServiceDefinition` elementet är det översta elementet i tjänstdefinitionsfilen.

I följande tabell beskrivs attributen för den `ServiceDefinition` element.

| Attribut               | Beskrivning |
| ----------------------- | ----------- |
| namn                    |Krävs. Namnet på tjänsten. Namnet måste vara unikt i kontot.|
| topologyChangeDiscovery | Valfri. Anger vilken typ av topologi ändringsmeddelande. Möjliga värden:<br /><br /> -   `Blast`-Skickar uppdateringen så snart som möjligt till alla rollinstanser. Om du väljer alternativet ska rollen kunna hantera topologi uppdateringen utan startas.<br />-   `UpgradeDomainWalk`– Skickar uppdateringen till varje rollinstans i ordning föregående instans har godkänt uppdateringen.|
| schemaVersion           | Valfri. Anger versionen av service definition schemat. Schemaversionen kan Visual Studio för att välja rätt SDK-verktyg ska användas för schemavalidering om mer än en version av SDK är installerat sida-vid-sida.|
| upgradeDomainCount      | Valfri. Anger antalet uppgraderingsdomäner som tilldelas roller i den här tjänsten. Rollinstanser har tilldelats en uppgraderingsdomän när tjänsten har distribuerats. Mer information finns i [uppdaterar en rolltjänst för molnet eller distribution](cloud-services-how-to-manage-portal.md#how-to-update-a-cloud-service-role-or-deployment).<br /><br /> Du kan ange upp till 20 uppgraderingsdomäner. Om inget anges är standardvärdet för antal uppgraderingsdomäner 5.|