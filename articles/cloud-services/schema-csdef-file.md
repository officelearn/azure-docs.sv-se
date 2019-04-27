---
title: Azure Cloud Services-Definitionsschema (.csdef-fil) | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: 42
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 4e018af7df64c9ed8050a3c618cf2645d5509cdd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613256"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Azure Cloud Services Definition Schema (.csdef File)
Tjänstdefinitionsfilen definierar tjänstmodellen för ett program. Filen innehåller definitionerna för de roller som är tillgängliga för en molntjänst, anger Tjänsteslutpunkter och upprättar konfigurationsinställningarna för tjänsten. Konfigurationsvärden för inställningen är inställda i tjänstkonfigurationsfilen, enligt beskrivningen av den [molntjänst (klassisk) konfigurationsschema](/previous-versions/azure/reference/ee758710(v=azure.100)).

Som standard installeras Azure Diagnostics schema konfigurationsfilen till den `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` directory. Ersätt `<version>` med den installerade versionen av den [Azure SDK](https://www.windowsazure.com/develop/downloads/).

Standardfilnamnstillägget för tjänstdefinitionsfilen är .csdef.

## <a name="basic-service-definition-schema"></a>Grundläggande tjänstdefinitionsschemat
Tjänstdefinitionsfilen måste innehålla ett `ServiceDefinition` element. Tjänstdefinitionen måste innehålla minst en roll (`WebRole` eller `WorkerRole`) element. Det kan innehålla upp till 25 roller som definierats i en enda definition och du kan blanda rolltyper. Tjänstdefinitionen innehåller även den valfria `NetworkTrafficRules` element som begränsar vilka roller kan kommunicera med angivna interna slutpunkter. Tjänstdefinitionen innehåller även den valfria `LoadBalancerProbes` elementet som innehåller kund definierats hälsoavsökningar-slutpunkter.

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

##  <a name="ServiceDefinition"></a> ServiceDefinition-Element
Den `ServiceDefinition` elementet har det översta elementet i tjänstdefinitionsfilen.

I följande tabell beskrivs attributen för den `ServiceDefinition` element.

| Attribut               | Beskrivning |
| ----------------------- | ----------- |
| namn                    |Krävs. Namnet på tjänsten. Namnet måste vara unikt inom kontot.|
| topologyChangeDiscovery | Valfri. Anger vilken typ av topologi ändringsmeddelande. Möjliga värden:<br /><br /> -   `Blast` -Skickar uppdateringen så snart som möjligt till alla rollinstanser. Om du väljer alternativet ska rollen kunna hantera topologi uppdateringen utan startas.<br />-   `UpgradeDomainWalk` – Skickar uppdateringen till varje rollinstans i ordning föregående instans har godkänt uppdateringen.|
| schemaVersion           | Valfri. Anger vilken version av tjänstdefinitionsschemat. Schemaversion kan Visual Studio för att de rätt SDK-verktyg som ska användas för schemavalideringen om mer än en version av SDK: N installeras sida-vid-sida.|
| upgradeDomainCount      | Valfri. Anger antalet uppgraderingsdomäner som tilldelas roller i den här tjänsten. Rollinstanser har tilldelats en uppgraderingsdomän när tjänsten har distribuerats. Mer information finns i [uppdatera en molntjänstroll eller distribution](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment).<br /><br /> Du kan ange upp till 20 uppgraderingsdomäner. Om inte anges är standardvärdet för antal uppgraderingsdomäner 5.|
