---
title: Azure Service fabric-diagnostik och övervakning | Microsoft Docs
description: Den här artikeln beskriver övervakningsfunktioner för programprestanda i Service Fabric tillförlitliga ServiceRemoting körning, t.ex. prestandaräknare som genereras av den.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: e4c698dc5aa8a07d09835adeac39db6ee35f720c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123186"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostik och övervakning av programprestanda för fjärrstyrd tillförlitlig tjänst
Tillförlitlig ServiceRemoting runtime genererar [prestandaräknare](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Det här ger insikt i hur ServiceRemoting fungerar och hjälpa till med felsökning och övervakning av programprestanda.


## <a name="performance-counters"></a>Prestandaräknare
Tillförlitlig ServiceRemoting runtime definierar följande prestandaräknarkategorier:

| Category | Beskrivning |
| --- | --- |
| Service Fabric-tjänst |Räknare som är specifika för Azure Service Fabric Service Remoting, till exempel Genomsnittlig tid det tar att bearbeta begäran |
| Service Fabric-tjänstmetod |Räknare som är specifika för metoder implementeras av fjärrkommunikation i Service Fabric, till exempel hur ofta en tjänst-metoden har anropats |

Var och en av ovanstående kategorier har en eller flera räknare.

Den [Windows Prestandaövervakaren](https://technet.microsoft.com/library/cc749249.aspx) program som är tillgängligt som standard i Windows-operativsystemet kan användas för att samla in och visa prestandaräknardata. [Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in data från en prestandaräknare och överföra den till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Namn på prestandaräknare instans
Ett kluster som har ett stort antal ServiceRemoting tjänster eller partitioner har ett stort antal instanser för räknaren av prestanda. Instansnamn på prestandaräknare kan identifiera specifik partition och metoden för en tjänst (om tillämpligt) att prestandaräknarinstans är associerad med.

#### <a name="service-fabric-service-category"></a>I Service Fabric-kategori
För kategorin `Service Fabric Service`, instans räknarnamnen är i följande format:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* är den sträng som innehåller Service Fabric partitions-ID som prestandaräknarinstans är associerad med. Partitions-ID är ett GUID och dess strängrepresentation genereras via den [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoden med formatspecifierare ”D”.

*ServiceReplicaOrInstanceId* är den sträng som innehåller Service Fabric repliken/instans-ID som prestandaräknarinstans är associerad med.

*ServiceRuntimeInternalID* är den sträng som innehåller ett 64-bitars heltal som genereras av Service Fabric-körningen för intern användning. Det ingår i instans prestandaräknarnamnet att se till att dess unikhet och undvika konflikt med andra instansnamn på prestandaräknare. Användare bör inte försöka att tolka den här delen av prestandaräknarnamnet för instansen.

Följande är ett exempel på en räknare instansnamn för en räknare som hör till den `Service Fabric Service` kategori:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

I föregående exempel `2740af29-78aa-44bc-a20b-7e60fb783264` är den sträng som innehåller Service Fabric partitions-ID `635650083799324046` är strängrepresentation av repliken/InstanceId och `5008379932` är 64-bitars-ID som genereras för körningen internt använder.

#### <a name="service-fabric-service-method-category"></a>Service Fabric-Tjänstmetod kategori
För kategorin `Service Fabric Service Method`, instans räknarnamnen är i följande format:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* är namnet på metoden tjänsten som prestandaräknarinstans är associerad med. Formatet för metodnamnet bestäms baserat på logiken i Service Fabric runtime som balanserar läsbarhet namnet med begränsningar på den maximala längden på instansnamn på prestandaräknare i Windows.

*ServiceRuntimeMethodId* är den sträng som innehåller ett 32-bitars heltal som genereras av Service Fabric-körningen för intern användning. Det ingår i instans prestandaräknarnamnet att se till att dess unikhet och undvika konflikt med andra instansnamn på prestandaräknare. Användare bör inte försöka att tolka den här delen av prestandaräknarnamnet för instansen.

*ServiceFabricPartitionID* är den sträng som innehåller Service Fabric partitions-ID som prestandaräknarinstans är associerad med. Partitions-ID är ett GUID och dess strängrepresentation genereras via den [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoden med formatspecifierare ”D”.

*ServiceReplicaOrInstanceId* är den sträng som innehåller Service Fabric repliken/instans-ID som prestandaräknarinstans är associerad med.

*ServiceRuntimeInternalID* är den sträng som innehåller ett 64-bitars heltal som genereras av Service Fabric-körningen för intern användning. Det ingår i instans prestandaräknarnamnet att se till att dess unikhet och undvika konflikt med andra instansnamn på prestandaräknare. Användare bör inte försöka att tolka den här delen av prestandaräknarnamnet för instansen.

Följande är ett exempel på en räknare instansnamn för en räknare som hör till den `Service Fabric Service Method` kategori:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

I föregående exempel `ivoicemailboxservice.leavemessageasync` är metodnamnet på `2` är 32-bitars-ID som genereras för en runtime internt bruk, `89383d32-e57e-4a9b-a6ad-57c6792aa521` är den sträng som innehåller Service Fabric partitions-ID`635650083804480486` är den sträng som innehåller Service Fabric-replik/instans-ID och `5008380` är 64-bitars-ID som genereras för körningen internt använder.

## <a name="list-of-performance-counters"></a>Lista över prestandaräknare
### <a name="service-method-performance-counters"></a>Prestandaräknare för metod

Reliable Services-runtime publicerar följande prestandaräknare som rör körning av tjänstmetoder.

| Kategorinamn | Namn på räknare | Beskrivning |
| --- | --- | --- |
| Service Fabric-tjänstmetod |Anrop/sek |Antal gånger som Servicemetoden har anropats per sekund |
| Service Fabric-tjänstmetod |Genomsnittligt antal millisekunder per anrop |Åtgången tid att köra service-metod i millisekunder |
| Service Fabric-tjänstmetod |Undantag/sek |Antal gånger att metoden tjänsten utlöste ett undantag per sekund |

### <a name="service-request-processing-performance-counters"></a>Prestandaräknare för bearbetning av begäran
När en klient anropar en metod som via en proxy serviceobjektet, resulterar det i ett meddelande om begäran som skickas över nätverket till tjänsten för fjärrkommunikation. Tjänsten bearbetar meddelandet med begäran och skickar tillbaka ett svar till klienten. Tillförlitlig ServiceRemoting runtime publicerar följande prestandaräknare som rör bearbetning av begäran av tjänsten.

| Kategorinamn | Namn på räknare | Beskrivning |
| --- | --- | --- |
| Service Fabric-tjänst |Antal väntande förfrågningar |Antalet begäranden som bearbetas i tjänsten |
| Service Fabric-tjänst |Genomsnittlig tid i millisekunder per begäran |Tid (i millisekunder) av tjänsten kan bearbeta en begäran |
| Service Fabric-tjänst |Genomsnittlig tid i millisekunder för deserialiseringsbegäran |Tid (i millisekunder) att deserialisera begärandemeddelandet för tjänsten när den tas emot på tjänsten |
| Service Fabric-tjänst |Genomsnittlig tid i millisekunder för serialiseringssvar |Tid (i millisekunder) att serialisera svarsmeddelandet service på tjänsten innan svaret skickas till klienten |

## <a name="next-steps"></a>Nästa steg
* [Exempelkod](https://azure.microsoft.com/en-us/resources/samples/?service=service-fabric&sort=0)
* [EventSource providers i PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
