---
title: Azure Cloud Services Def. LoadBalancerProbe schemat | Microsoft Docs
ms.custom: 
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: "14"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 31c974c5a4b9dc9cff882ff42b73ee023fc4ad9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Azure Cloud Services Definition LoadBalancerProbe Schema
Belastningsutjämnaravsökningen är en kund definierade hälsa avsökning av UDP-slutpunkter och slutpunkter i rollinstanser. Den `LoadBalancerProbe` är inte ett fristående element; den kombineras med rollen webb eller arbetare i definitionsfilen för tjänsten. En `LoadBalancerProbe` kan användas av fler än en roll.

Standardfilnamnstillägget för tjänstdefinitionsfilen är .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funktionen för en belastningsutjämningsavsökning
Azure belastningsutjämnare ansvarar för inkommande trafik till dina rollinstanser. Belastningsutjämnaren avgör vilka instanser kan ta emot trafik genom avsökning regelbundet varje instans för att fastställa hälsotillståndet för den instansen. Belastningsutjämnaren avsökningar varje instans flera gånger per minut. Det finns två olika alternativ för att tillhandahålla instans hälsa till belastningsutjämnaren – standard-belastningsutjämningsavsökning eller en egen belastningsutjämnare avsökning som tillämpas genom att definiera LoadBalancerProbe i .csdef-filen.

Standard-belastningsutjämningsavsökning använder Gästagenten på den virtuella datorn som lyssnar och svarar med ett HTTP-200 OK svar endast när instansen är i tillståndet redo (t.ex. när instansen inte är i upptagen, återvinning, stoppas, etc. tillstånd). Om Gästagenten inte svarar med HTTP 200 OK, markerar instansen som inte svarar Azure belastningsutjämnare och slutar skickar trafik till den instansen. Azure belastningsutjämnare fortsätter att pinga instansen och om Gästagenten svarar med en HTTP-200, Azure-belastningsutjämnaren skickar trafik till instansen igen. När du använder en webbroll webbplats koden körs normalt i w3wp.exe som inte övervakas av Azure-strukturen eller gästagenten, vilket innebär att fel i w3wp.exe (t.ex. HTTP 500-svar) har inte rapporterats till gästagenten och belastningen belastningsutjämnaren inte vet för att vidta instansen utanför rotation.

Den anpassade belastningsutjämningsavsökning åsidosätter standard gäst agent avsökning och kan du skapa egna anpassade logik för att fastställa hälsotillståndet för instansen. Belastningsutjämnaren regelbundet avsökningar slutpunkten (var 15 sekunder, som standard) och instansen är anses i rotation om den svarar med en ACK TCP eller HTTP 200 inom tidsgränsen (standard 31 sekunder). Detta kan vara användbart att implementera din egen logik för att ta bort instanser från belastningen belastningsutjämnaren rotation, till exempel returnerar status-200 om instansen är över 90% CPU. För webbroller med hjälp av w3wp.exe, detta betyder också att du får automatisk övervakning av din webbplats, eftersom fel i koden webbplats returstatus en icke-200 till belastningsutjämnaravsökningen. Om du inte definierar en LoadBalancerProbe i .csdef-filen, så belastningen belastningsutjämnaren standardbeteendet (enligt beskrivningen ovan) användas.

Om du använder en anpassad belastningsutjämningsavsökning måste du kontrollera att din logik som tar hänsyn till metoden RoleEnvironment.OnStop. När du använder standard belastningsutjämningsavsökning instansen tas ur rotation innan OnStop anropas, men en anpassad belastningsutjämningsavsökning kan fortsätta att returnera en 200 OK under händelsen OnStop. Om du använder OnStop händelsen om du vill rensa cache stoppar tjänsten eller på annat sätt att göra ändringar som kan påverka beteende under körning av din tjänst måste du se till att dina anpassade belastningen belastningsutjämnaren avsökningen logik tar bort instansen från rotation.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Grundläggande service definition schemat för en belastningsutjämningsavsökning
 Det grundläggande formatet för en tjänstdefinitionsfilen som innehåller en belastningsutjämningsavsökning är som följer.

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

##  <a name="LoadBalancerProbes"></a>LoadBalancerProbes Element
Den `LoadBalancerProbes` element beskriver insamling av belastningen belastningsutjämnaren avsökningar. Det här elementet har det överordnade elementet för den [LoadBalancerProbe elementet](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a>LoadBalancerProbe Element
Den `LoadBalancerProbe` elementet definierar hälsoavsökningen för en modell. Du kan definiera flera load balancer avsökningar. 

I följande tabell beskrivs attributen för den `LoadBalancerProbe` element:

|Attribut|Typ|Beskrivning|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Krävs. Namnet på belastningsutjämnaravsökningen. Namnet måste vara unikt.|
| `protocol`          | `string` | Krävs. Anger vilket protokoll för slutpunkten. Möjliga värden är `http` eller `tcp`. Om `tcp` anges mottagna ACK måste anges för avsökning ska lyckas. Om `http` anges 200 OK svar från angiven URI måste anges för avsökning ska lyckas.|
| `path`              | `string` | Den URI som används för att begära hälsostatus från den virtuella datorn. `path`krävs om `protocol` är inställd på `http`. I annat fall tillåtet det inte.<br /><br /> Det finns inget standardvärde.|
| `port`              | `integer` | Valfri. Porten för kommunikation avsökningen. Det här är valfritt för valfri slutpunkt som samma port används sedan för avsökningen. Du kan konfigurera en annan port för sina sökning, samt. Möjliga värden mellan 1 och 65535, inklusive.<br /><br /> Standardvärdet anges av slutpunkten.|
| `intervalInSeconds` | `integer` | Valfri. Intervall i sekunder för hur ofta avsökning slutpunkten för hälsostatus. Intervallet är vanligtvis något mindre än en halv allokerade tidsgränsen (i sekunder) som gör att två fullständiga avsökningar innan du tar instansen utanför rotation.<br /><br /> Standardvärdet är 15, det lägsta värdet är 5.|
| `timeoutInSeconds`  | `integer` | Valfri. Tidsgränsen i sekunder, tillämpas på avsökningen där inget svar resulterar i ytterligare stoppar trafik från skickas till slutpunkten. Detta värde kan slutpunkter som ska vidtas utanför rotation snabbare eller långsammare än vanlig gånger används i Azure (som standard).<br /><br /> Standardvärdet är 31 är det minsta värdet 11.|

## <a name="see-also"></a>Se även
[Cloud Service (klassiskt) Definition av schemat](schema-csdef-file.md)