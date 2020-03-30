---
title: Definitionsschema för Azure Cloud Services (.csdef-fil) | Microsoft-dokument
description: En tjänstdefinitionsfil (.csdef) definierar en tjänstmodell för ett program som innehåller tillgängliga roller, slutpunkter och konfigurationsvärden för tjänsten.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: tgore03
ms.author: tagore
ms.openlocfilehash: dadb50bd0663f47e6a1bf3d58b5187c8b466964d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528378"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Definitionsschema för Azure Cloud Services (.csdef-fil)
Tjänstdefinitionsfilen definierar tjänstmodellen för ett program. Filen innehåller definitionerna för de roller som är tillgängliga för en molntjänst, anger tjänstslutpunkterna och upprättar konfigurationsinställningar för tjänsten. Konfigurationsinställningsvärden anges i tjänstkonfigurationsfilen, enligt beskrivningen av [konfigurationsschemat för molntjänsten (klassisk)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Som standard installeras konfigurationsschemafilen för Azure `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` Diagnostics i katalogen. Ersätt `<version>` med den installerade versionen av [Azure SDK](https://www.windowsazure.com/develop/downloads/).

Standardtillägget för tjänstdefinitionsfilen är .csdef.

## <a name="basic-service-definition-schema"></a>Schema för grundläggande tjänstdefinition
Tjänstdefinitionsfilen måste `ServiceDefinition` innehålla ett element. Tjänstdefinitionen måste innehålla minst`WebRole` ett `WorkerRole`rollelement ( eller ). Den kan innehålla upp till 25 roller som definierats i en enda definition och du kan blanda rolltyper. Tjänstdefinitionen innehåller också `NetworkTrafficRules` det valfria elementet som begränsar vilka roller som kan kommunicera till angivna interna slutpunkter. Servicedefinitionen innehåller också `LoadBalancerProbes` det valfria elementet som innehåller kunddefinierade hälsoavsökningar av slutpunkter.

Det grundläggande formatet för tjänstdefinitionsfilen är följande.

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

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a>ServiceDefinition Element
Elementet `ServiceDefinition` är elementet på den översta nivån i tjänstdefinitionsfilen.

I följande tabell beskrivs elementets `ServiceDefinition` attribut.

| Attribut               | Beskrivning |
| ----------------------- | ----------- |
| namn                    |Krävs. Namnet på tjänsten. Namnet måste vara unikt i tjänstkontot.|
| topologiByteDiscovery | Valfri. Anger typen av meddelande om att topologin ändras. Möjliga värden:<br /><br /> -   `Blast`- Skickar uppdateringen så snart som möjligt till alla rollinstanser. Om du väljer alternativet bör rollen kunna hantera topologiuppdateringen utan att startas om.<br />-   `UpgradeDomainWalk`– Skickar uppdateringen till varje rollinstans på ett sekventiellt sätt efter att den föregående instansen har accepterat uppdateringen.|
| schemaVersion           | Valfri. Anger versionen av servicedefinitionsschemat. Schemaversionen gör det möjligt för Visual Studio att välja rätt SDK-verktyg som ska användas för schemavalidering om mer än en version av SDK installeras sida vid sida.|
| uppgraderaDomainCount      | Valfri. Anger antalet uppgraderingsdomäner för vilka roller i den här tjänsten allokeras. Rollinstanser allokeras till en uppgraderingsdomän när tjänsten distribueras. Mer information finns i [Uppdatera en molntjänstroll eller distribution](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), Hantera [tillgängligheten för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) och Vad är en [molntjänstmodell](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package).<br /><br /> Du kan ange upp till 20 uppgraderingsdomäner. Om inget anges är standardantalet uppgraderingsdomäner 5.|
