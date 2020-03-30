---
title: Azure Cloud Services Def. LoadBalancerProbe-schema | Microsoft-dokument
description: Kunden definierade LoadBalancerProbe är en hälsoavsökning av slutpunkter i rollinstanser. Den kombineras med webb- eller arbetarroller i en tjänstdefinitionsfil.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: tagore
ms.openlocfilehash: 6d0e84b6724d9df4162d4be3e06a9952087a53a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537354"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Azure Cloud Services Definition LoadBalancerProbe-schema
Belastningsutjämnaravsökningen är en kunddefinierad hälsoavsökning av UDP-slutpunkter och slutpunkter i rollinstanser. Det `LoadBalancerProbe` är inte ett fristående element; Det kombineras med webbrollen eller arbetarrollen i en tjänstdefinitionsfil. A `LoadBalancerProbe` kan användas av mer än en roll.

Standardtillägget för tjänstdefinitionsfilen är .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funktionen hos en belastningsutjämnad sond
Azure Load Balancer ansvarar för att dirigera inkommande trafik till dina rollinstanser. Belastningsutjämnaren avgör vilka instanser som kan ta emot trafik genom att regelbundet sondera varje instans för att fastställa hälsotillståndet för den instansen. Belastningsutjämnaren avsöker varje instans flera gånger per minut. Det finns två olika alternativ för att ge instanshälsa till belastningsutjämnaren – standardbelastningsutjämnaden eller en anpassad belastningsutjämnadavsökning, som implementeras genom att definiera LoadBalancerProbe i CSDEF-filen.

Standardbelastningsutjämnaravsökningen använder gästagenten inuti den virtuella datorn, som lyssnar och svarar med ett HTTP 200 OK-svar endast när instansen är i redoläge (som när instansen inte är upptagen, återvinning, stopp osv. Om gästagenten inte svarar med HTTP 200 OK markerar Azure Load Balancer instansen som svarar inte och slutar skicka trafik till den instansen. Azure Load Balancer fortsätter att pinga instansen, och om gästagenten svarar med en HTTP 200 skickar Azure Load Balancer trafik till den instansen igen. När du använder en webbroll körs din webbplatskod vanligtvis i w3wp.exe som inte övervakas av Azure-infrastrukturen eller gästagenten, vilket innebär fel i w3wp.exe (t.ex. HTTP 500-svar) rapporteras inte till gästagenten och belastningsutjämnaren vet inte att instansen ska vara ur rotation.

Den anpassade belastningsutjämnaravsökningen åsidosätter standardprogramagentavsökningen och låter dig skapa din egen anpassade logik för att avgöra hälsotillståndet för rollinstansen. Belastningsutjämnaren avsöker regelbundet din slutpunkt (var 15:e sekund, som standard) och instansen beaktas i rotation om den svarar med en TCP ACK eller HTTP 200 inom tidsgränsen (som standard 31 sekunder). Detta kan vara användbart för att implementera din egen logik för att ta bort instanser från belastningsutjämnad rotation, till exempel returnera en icke-200 status om instansen är över 90% CPU. För webbroller som använder w3wp.exe innebär det också att du får automatisk övervakning av din webbplats, eftersom fel i webbplatskoden returnerar en icke-200-status till belastningsutjämnaravsökningen. Om du inte definierar en LoadBalancerProbe i CSDEF-filen används standardfunktion för belastningsutjämnare (som tidigare beskrivits).

Om du använder en anpassad belastningsutjämnareavsökning måste du se till att logiken tar hänsyn till metoden RoleEnvironment.OnStop. När du använder standardavsökningen för belastningsutjämnaren tas instansen ur rotation innan OnStop anropas, men en anpassad belastningsutjämnadavsökning kan fortsätta att returnera en 200 OK under OnStop-händelsen. Om du använder OnStop-händelsen för att rensa cacheminnet, stoppa tjänsten eller på annat sätt göra ändringar som kan påverka tjänstens körningsbeteende måste du se till att din anpassade belastningsjämningsavsökningslogik tar bort instansen från rotationen.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Grundläggande tjänstdefinitionsschema för en belastningsutjämnad avsökning
 Det grundläggande formatet för en tjänstdefinitionsfil som innehåller en belastningsutjämnad avsökning är följande.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schemaelement
Elementet `LoadBalancerProbes` i tjänstdefinitionsfilen innehåller följande element:

- [LoadBalancerProbes Element](#LoadBalancerProbes)
- [LoadBalancerProbe-element](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a>LoadBalancerProbes Element
Elementet `LoadBalancerProbes` beskriver insamlingen av belastningsutjämnareavsökningar. Det här elementet är det överordnade elementet i [LoadBalancerProbe Element](#LoadBalancerProbe). 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a>LoadBalancerProbe-element
Elementet `LoadBalancerProbe` definierar hälsoavsökningen för en modell. Du kan definiera flera belastningsutjämnareavsökningar. 

I följande tabell beskrivs elementets `LoadBalancerProbe` attribut:

|Attribut|Typ|Beskrivning|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Krävs. Namnet på belastningsutjämnaren. Namnet måste vara unikt.|
| `protocol`          | `string` | Krävs. Anger slutpunktens protokoll. Möjliga värden är `http` eller `tcp`. Om `tcp` anges krävs en mottagen ACK för att avsökningen ska lyckas. Om `http` anges krävs ett 200 OK-svar från den angivna URI:n för att avsökningen ska lyckas.|
| `path`              | `string` | Uri som används för att begära hälsostatus från den virtuella datorn. `path`krävs om `protocol` den `http`är inställd på . Annars är det inte tillåtet.<br /><br /> Det finns inget standardvärde.|
| `port`              | `integer` | Valfri. Porten för att kommunicera sonden. Detta är valfritt för alla slutpunkter, eftersom samma port sedan kommer att användas för avsökningen. Du kan också konfigurera en annan port för deras sondering. Möjliga värden varierar från 1 till 65535, inklusive.<br /><br /> Standardvärdet anges av slutpunkten.|
| `intervalInSeconds` | `integer` | Valfri. Intervallet, i sekunder, för hur ofta slutpunkten ska avsökas för hälsostatus. Vanligtvis är intervallet något mindre än hälften av den tilldelade timeout-perioden (i sekunder) vilket tillåter två fullständiga avsökningar innan instansen tas ur rotation.<br /><br /> Standardvärdet är 15, minimivärdet är 5.|
| `timeoutInSeconds`  | `integer` | Valfri. Tidsgränsen, i sekunder, tillämpas på avsökningen där inget svar kommer att resultera i att stoppa ytterligare trafik från att levereras till slutpunkten. Med det här värdet kan slutpunkter tas ur rotation snabbare eller långsammare än de typiska tider som används i Azure (som är standardvärden).<br /><br /> Standardvärdet är 31, minimivärdet är 11.|

## <a name="see-also"></a>Se även
[Molntjänst (klassiskt) definitionsschema](schema-csdef-file.md)