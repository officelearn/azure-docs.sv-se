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
ms.openlocfilehash: df2f7c1bf99c13779e5720e15d8d669aa4f945c0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002757"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Azure Cloud Services-Definitionsschema (.csdef-fil)
Tjänstdefinitionsfilen definierar tjänstmodellen för ett program. Filen innehåller definitionerna för de roller som är tillgängliga för en molntjänst, anger Tjänsteslutpunkter och upprättar konfigurationsinställningarna för tjänsten. Konfigurationsvärden för inställningen är inställda i tjänstkonfigurationsfilen, enligt beskrivningen av den [molntjänst (klassisk) konfigurationsschema](http://msdn.microsoft.com/library/b1ae68cd-cc95-48cb-a4a4-da91dc708a35).

Som standard installeras Azure Diagnostics schema konfigurationsfilen till den `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` directory. Ersätt `<version>` med den installerade versionen av den [Azure SDK](http://www.windowsazure.com/develop/downloads/).

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