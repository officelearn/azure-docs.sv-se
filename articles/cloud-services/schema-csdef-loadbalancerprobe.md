---
title: Azure Cloud Services Def. LoadBalancerProbe-Schema | Microsoft Docs
ms.custom: ''
origin.date: 04/14/2015
ms.date: 11/06/2017
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: 14
author: thraka
ms.author: v-yiso
manager: timlt
ms.openlocfilehash: de365de7bf93c0a612f102b3ec2b25c79d1c3d18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613869"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Azure Cloud Services Definition LoadBalancerProbe Schema
Belastningsutjämningsavsökningen är en kund definierade hälsoavsökning slutpunkter i rollinstanser och UDP-slutpunkter. Den `LoadBalancerProbe` är inte ett fristående element; den kombineras med web-roll eller worker-roll i en tjänstdefinitionsfilen. En `LoadBalancerProbe` kan användas av mer än en roll.

Standardfilnamnstillägget för tjänstdefinitionsfilen är .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funktionen för en belastningsutjämnaravsökning
Azure Load Balancer ansvarar för att dirigera inkommande trafik till dina rollinstanser. Belastningsutjämnaren avgör vilka instanser kan ta emot trafik genom sökning regelbundet varje instans för att fastställa hälsotillståndet för den instansen. Load balancer kontrollerar varje instans flera gånger per minut. Det finns två olika alternativ för att tillhandahålla instans hälsa till belastningsutjämnaren – standard belastningsutjämnaravsökning, eller en anpassad belastningsutjämnare avsökning, som tillämpas genom att definiera LoadBalancerProbe i .csdef-filen.

Standard belastningsutjämnaravsökningen använder Gästagenten på den virtuella datorn som lyssnar och svarar med ett HTTP 200 OK-svar endast när instansen är i tillståndet klar (t.ex. när instansen inte är i upptagen, återvinning, stoppa, etc. tillstånd). Om Gästagenten inte kan svara med HTTP 200 OK, markerar instansen som inte svarar Azure Load Balancer och stoppar skickar trafik till den instansen. Azure Load Balancer fortsätter att pinga instansen och om Gästagenten svarar med ett 200 HTTP, Azure Load Balancer skickar trafik till den instansen igen. När du använder en webbroll webbplats koden körs vanligtvis i w3wp.exe som inte övervakas av Azure-infrastrukturen eller gästagenten, vilket innebär att fel i w3wp.exe (t.ex.) HTTP 500-svar) har inte rapporterats till gästagenten och belastningen belastningsutjämnaren inte vet för att ta den instansen bort från roteringen.

Anpassade belastningsutjämnaravsökningen åsidosätter standard gäst agenten avsökningen och kan du skapa en egen anpassad logik för att fastställa hälsotillståndet för rollinstansen. Belastningsutjämnaren regelbundet avsökningar slutpunkten (var 15 sekunder, som standard) och -instansen är anses i rotation om svarar den med ett ACK TCP eller HTTP 200 inom tidsgränsen (standard 31 sekunder). Detta kan vara praktiskt att implementera en egen logik för att ta bort instanser från belastningsutjämnarens rotation, till exempel returnera statusen icke-200 om instansen är över 90% CPU. För webbroller använder w3wp.exe det innebär även att du får automatisk övervakning av din webbplats, eftersom felen i koden webbplats returnera statusen icke-200 till belastningsutjämnaravsökningen. Om du inte definierar en LoadBalancerProbe i .csdef-filen, så standard belastningsutjämnarens beteende (enligt beskrivningen ovan) användas.

Om du använder en anpassad belastningsutjämnaravsökning, måste du kontrollera att logik som tar hänsyn RoleEnvironment.OnStop-metoden. När du använder standard belastningsutjämnaravsökningen instansen tas bort från roteringen innan OnStop som anropas, men en anpassad belastningsutjämnaravsökning kan fortsätta att returnera ett 200 OK under åtgärden OnStop. Om du använder OnStop händelsen om du vill rensa cache stoppar tjänsten eller på annat sätt att göra ändringar som kan påverka beteende under körning av din tjänst, måste du se till att din anpassade load balancer avsökningen logik tas instansen bort från rotationen.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Grundläggande tjänstdefinitionsschemat för en belastningsutjämnaravsökning
 Det grundläggande formatet för en tjänstdefinitionsfilen som innehåller en belastningsutjämnaravsökning är som följer.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schemaelement
Den `LoadBalancerProbes` element i tjänstdefinitionsfilen innehåller följande element:

- [LoadBalancerProbes Element](#LoadBalancerProbes)
- [LoadBalancerProbe Element](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> LoadBalancerProbes Element
Den `LoadBalancerProbes` element beskriver uppsättningen avsökningar av belastningsutjämnare. Det här elementet har det överordnade elementet i den [LoadBalancerProbe elementet](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a> LoadBalancerProbe Element
Den `LoadBalancerProbe` elementet definierar hälsoavsökningen för en modell. Du kan definiera flera avsökningar av belastningsutjämnare. 

I följande tabell beskrivs attributen för den `LoadBalancerProbe` element:

|Attribut|Typ|Beskrivning|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Krävs. Namnet på belastningsutjämnaravsökningen. Namnet måste vara unikt.|
| `protocol`          | `string` | Krävs. Anger vilket protokoll för slutpunkten. Möjliga värden är `http` eller `tcp`. Om `tcp` har angetts en mottagna ACK krävs för avsökningen ska lyckas. Om `http` har angetts ett 200 OK svar från angiven URI krävs för avsökningen ska lyckas.|
| `path`              | `string` | Den URI som används för hälsotillståndsförfrågningar från den virtuella datorn. `path` krävs om `protocol` är inställd på `http`. I annat fall tillåtet det inte.<br /><br /> Det finns inget standardvärde.|
| `port`              | `integer` | Valfri. Porten som används för att kommunicera avsökningen. Detta är valfritt för valfri slutpunkt eftersom samma port används sedan för avsökningen. Du kan konfigurera en annan port för sina, samt. Möjliga värden mellan 1 och 65535, inklusive.<br /><br /> Standardvärdet anges av slutpunkten.|
| `intervalInSeconds` | `integer` | Valfri. Intervall i sekunder för hur ofta avsökning slutpunkten för hälsostatus. Intervallet är vanligtvis något mindre än en halv tilldelade tidsgränsen (i sekunder) som gör två fullständiga avsökningar innan du tar instansen bort från roteringen.<br /><br /> Standardvärdet är 15, det lägsta värdet är 5.|
| `timeoutInSeconds`  | `integer` | Valfri. Tidsgränsen i sekunder, tillämpas på avsökningen där inget svar resulterar i ytterligare stoppar trafik från håller på att skickas till slutpunkten. Det här värdet kan slutpunkter tas bort från roteringen snabbare eller långsammare än de vanliga tider som används i Azure (som är standardinställningarna).<br /><br /> Standardvärdet är 31, är det minsta värdet 11.|

## <a name="see-also"></a>Se även
[Molnet (klassisk) Tjänstdefinitionsschemat](schema-csdef-file.md)