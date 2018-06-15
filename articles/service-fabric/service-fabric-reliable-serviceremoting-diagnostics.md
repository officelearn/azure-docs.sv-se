---
title: Azure ServiceFabric diagnostik- och övervakning | Microsoft Docs
description: Den här artikeln beskriver övervakningsfunktioner för programprestanda i tillförlitliga ServiceRemoting för Service Fabric-körningsmiljön som prestandaräknare som sänds av den.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: d462ba0955a362c27b786ee6a5670eec20c52a22
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206427"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostik- och prestandaövervakning för tillförlitlig tjänst fjärrkommunikation
Tillförlitliga ServiceRemoting runtime avger [prestandaräknare](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Dessa ger insikter om hur ServiceRemoting fungerar och hjälp med felsökning och övervakning av programprestanda.


## <a name="performance-counters"></a>Prestandaräknare
Tillförlitliga ServiceRemoting runtime definierar följande prestandaräknarkategorier:

| Kategori | Beskrivning |
| --- | --- |
| Service Fabric-tjänst |Räknare som är specifika för fjärrkommunikation med Azure Service Fabric-tjänsten, till exempel Genomsnittlig tid det tar att bearbeta begäran |
| Service Fabric-tjänstmetod |Räknare som är specifika för metoder implementeras av Service Fabric-fjärranslutningstjänsten, till exempel hur ofta en Servicemetoden har anropats |

Var och en av ovanstående kategorier har en eller flera räknare.

Den [Windows Prestandaövervakaren](https://technet.microsoft.com/library/cc749249.aspx) program som är tillgängligt som standard i Windows-operativsystemet kan användas för att samla in och visa information om prestandaräknare. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in prestandaräknardata och överföra den till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Namn på prestandaräknarinstanser
Ett kluster med ett stort antal ServiceRemoting tjänster eller partitioner har ett stort antal instanser för räknaren av prestanda. Namn på prestandaräknarinstanser hjälper dig identifiera specifik partition och metod för tjänst (om tillämpligt) som prestandaräknarinstans är kopplad till.

#### <a name="service-fabric-service-category"></a>Fabric-tjänsten kategori
För kategori `Service Fabric Service`, instansnamn räknaren finns i följande format:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* är strängrepresentation av Service Fabric partitions-ID som prestandaräknarinstans är kopplad till. Partitions-ID är ett GUID och dess strängrepresentation genereras via den [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metod med Formatspecificeraren ”D”.

*ServiceReplicaOrInstanceId* är strängrepresentation av Service Fabric repliken/instansen ID som prestandaräknarinstans är kopplad till.

*ServiceRuntimeInternalID* är strängrepresentation av ett 64-bitars heltal som genereras av Service Fabric runtime för intern användning. Detta ingår i instansen prestandaräknarnamnet att säkerställa dess unika och undvika konflikter med andra namn på prestandaräknarinstanser. Användare försöka inte tolka den här delen av prestandaräknarnamnet för instansen.

Följande är ett exempel på ett räknaren instansnamn för en räknare som tillhör den `Service Fabric Service` kategori:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

I föregående exempel `2740af29-78aa-44bc-a20b-7e60fb783264` är strängrepresentation av Service Fabric partitions-ID `635650083799324046` är strängrepresentation av repliken/InstanceId och `5008379932` är 64-bitars-ID som genereras för körningsmiljön internt använder.

#### <a name="service-fabric-service-method-category"></a>Service Fabric-tjänsten metoden kategori
För kategori `Service Fabric Service Method`, instansnamn räknaren finns i följande format:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* är namnet på metoden tjänsten som prestandaräknarinstans är kopplad till. Formatet för metodnamnet bestäms baserat på viss logik i Fabric Service körningsmiljön som balanserar läsbarhet på namn med begränsningar på den maximala längden på namn på prestandaräknarinstanser i Windows.

*ServiceRuntimeMethodId* är strängrepresentation av en 32-bitars heltal som genereras av Service Fabric runtime för intern användning. Detta ingår i instansen prestandaräknarnamnet att säkerställa dess unika och undvika konflikter med andra namn på prestandaräknarinstanser. Användare försöka inte tolka den här delen av prestandaräknarnamnet för instansen.

*ServiceFabricPartitionID* är strängrepresentation av Service Fabric partitions-ID som prestandaräknarinstans är kopplad till. Partitions-ID är ett GUID och dess strängrepresentation genereras via den [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metod med Formatspecificeraren ”D”.

*ServiceReplicaOrInstanceId* är strängrepresentation av Service Fabric repliken/instansen ID som prestandaräknarinstans är kopplad till.

*ServiceRuntimeInternalID* är strängrepresentation av ett 64-bitars heltal som genereras av Service Fabric runtime för intern användning. Detta ingår i instansen prestandaräknarnamnet att säkerställa dess unika och undvika konflikter med andra namn på prestandaräknarinstanser. Användare försöka inte tolka den här delen av prestandaräknarnamnet för instansen.

Följande är ett exempel på ett räknaren instansnamn för en räknare som tillhör den `Service Fabric Service Method` kategori:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

I föregående exempel `ivoicemailboxservice.leavemessageasync` är metodnamnet på `2` är 32-bitars-ID som genererades för intern användning av den runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` är strängrepresentation av Service Fabric partitions-ID`635650083804480486` är strängrepresentation av Service Fabric-replik-/ instans-ID och `5008380` är 64-bitars-ID som genererades för körningsmiljön internt använder.

## <a name="list-of-performance-counters"></a>Lista över prestandaräknare
### <a name="service-method-performance-counters"></a>Prestandaräknare för tjänsten metod

Tillförlitlig tjänst runtime publicerar följande prestandaräknare som rör körning av tjänstmetoder.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric-tjänstmetod |Anrop/sek |Antalet gånger som Servicemetoden har anropats per sekund |
| Service Fabric-tjänstmetod |Genomsnittligt antal millisekunder per anrop |Åtgången tid för att köra Servicemetoden i millisekunder |
| Service Fabric-tjänstmetod |Undantag/sek |Antal gånger som metoden tjänsten utlöste ett undantag per sekund |

### <a name="service-request-processing-performance-counters"></a>Prestandaräknare för tjänsten begäran bearbetning
När en klient anropar en metod via en proxy webbtjänstobjektet, resulterar det i ett meddelande om begäran som skickas över nätverket till remoting service. Tjänsten bearbetar meddelandet med begäran och skickar tillbaka ett svar till klienten. Tillförlitliga ServiceRemoting runtime publicerar följande Prestandaräknare relaterade till behandling av begäranden för tjänsten.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric-tjänst |Antal väntande förfrågningar |Antalet begäranden som bearbetas i tjänsten |
| Service Fabric-tjänst |Genomsnittlig tid i millisekunder per begäran |Tid (i millisekunder av tjänsten för att bearbeta en begäran) |
| Service Fabric-tjänst |Genomsnittlig tid i millisekunder för deserialiseringsbegäran |Tid (i millisekunder) att avserialisera begärandemeddelandet tjänsten när den tas emot med tjänsten |
| Service Fabric-tjänst |Genomsnittlig tid i millisekunder för serialiseringssvar |Tid (i millisekunder) att serialisera svarsmeddelandet tjänst i tjänsten innan svaret skickas till klienten |

## <a name="next-steps"></a>Nästa steg
* [Exempelkod](https://github.com/Azure/servicefabric-samples)
* [EventSource providrar på förhandsgranskning](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
