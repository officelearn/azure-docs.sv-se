---
title: Azure ServiceFabric diagnostik och övervakning
description: I den här artikeln beskrivs prestandaövervakningsfunktionerna i den tillförlitliga servicekörningen för serviceinfrastruktur, till exempel prestandaräknare som den avger.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 31095a619fc4d756fa4ef9c29691d1d511d59ece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282281"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostik och prestandaövervakning för tillförlitlig serviceåtersmärtering
Den tillförlitliga serviceRemoting-körningen avger [prestandaräknare](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Dessa ger insikter om hur ServiceRemoting fungerar och hjälper till med felsökning och prestandaövervakning.


## <a name="performance-counters"></a>Prestandaräknare
Körningen Tillförlitlig ServiceRemoting definierar följande prestandaräknarkategorier:

| Kategori | Beskrivning |
| --- | --- |
| Service fabric-service |Räknare som är specifika för Azure Service Fabric Service Remoting , till exempel genomsnittlig tid för att bearbeta begäran |
| Service fabric-metod för service |Räknare som är specifika för metoder som implementerats av Service Fabric Remoting Service, till exempel hur ofta en tjänstmetod anropas |

Var och en av de föregående kategorierna har en eller flera räknare.

Windows [Performance Monitor-programmet](https://technet.microsoft.com/library/cc749249.aspx) som är tillgängligt som standard i Operativsystemet Windows kan användas för att samla in och visa prestandaräkna data. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in prestandaräknardata och överföra dem till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Namn på prestandaräknareinstans
Ett kluster som har ett stort antal ServiceRemoting-tjänster eller -partitioner har ett stort antal prestandaräknarinstanser. Namn på prestandaräknareinstans kan hjälpa dig att identifiera den specifika partition och tjänstmetod (om tillämpligt) som prestandaräkringsinstansen är associerad med.

#### <a name="service-fabric-service-category"></a>Service Fabric-kategori
För kategorin `Service Fabric Service`är räknarinstansnamnen i följande format:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* är strängrepresentationen av partitions-ID för Tjänst fabric som prestandaräkringsinstansen är associerad med. Partitions-ID är ett GUID och dess [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) strängrepresentation genereras via metoden med formatspecificeraren "D".

*ServiceReplicaOrInstanceId* är strängrepresentationen av service fabric-replik-/instans-ID som prestandaräkringsinstansen är associerad med.

*ServiceRuntimeInternalID* är strängrepresentationen av ett 64-bitars heltal som genereras av Fabric Service-körningen för internt bruk. Detta ingår i prestandaräknarens instansnamn för att säkerställa dess unika och undvika konflikt med andra namn på prestandaräknareinstans. Användare bör inte försöka tolka den här delen av prestandaräknarens förekomstnamn.

Följande är ett exempel på ett räknareinstansnamn `Service Fabric Service` för en räknare som tillhör kategorin:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

I föregående exempel `2740af29-78aa-44bc-a20b-7e60fb783264` är strängrepresentationen av partitions-ID:t för tjänstinfrastruktur, `635650083799324046` är strängrepresentation av Replik/InstanceId och `5008379932` är 64-bitars-ID som genereras för körningens interna användning.

#### <a name="service-fabric-service-method-category"></a>Service Fabric-metod kategori
För kategorin `Service Fabric Service Method`är räknarinstansnamnen i följande format:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* är namnet på den tjänstmetod som prestandaräkringsinstansen är associerad med. Formatet på metodnamnet bestäms baserat på viss logik i Fabric Service-körningen som balanserar namnets läsbarhet med begränsningar för den maximala längden på prestandaräknarinstansnamnen i Windows.

*ServiceRuntimeMethodId* är strängrepresentationen av ett 32-bitars heltal som genereras av Fabric Service-körningen för internt bruk. Detta ingår i prestandaräknarens instansnamn för att säkerställa dess unika och undvika konflikt med andra namn på prestandaräknareinstans. Användare bör inte försöka tolka den här delen av prestandaräknarens förekomstnamn.

*ServiceFabricPartitionID* är strängrepresentationen av partitions-ID för Tjänst fabric som prestandaräkringsinstansen är associerad med. Partitions-ID är ett GUID och dess [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) strängrepresentation genereras via metoden med formatspecificeraren "D".

*ServiceReplicaOrInstanceId* är strängrepresentationen av service fabric-replik-/instans-ID som prestandaräkringsinstansen är associerad med.

*ServiceRuntimeInternalID* är strängrepresentationen av ett 64-bitars heltal som genereras av Fabric Service-körningen för internt bruk. Detta ingår i prestandaräknarens instansnamn för att säkerställa dess unika och undvika konflikt med andra namn på prestandaräknareinstans. Användare bör inte försöka tolka den här delen av prestandaräknarens förekomstnamn.

Följande är ett exempel på ett räknareinstansnamn `Service Fabric Service Method` för en räknare som tillhör kategorin:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

I föregående exempel, `ivoicemailboxservice.leavemessageasync` är `2` metodnamnet, är 32-bitars-ID som genereras för `89383d32-e57e-4a9b-a6ad-57c6792aa521` körningens interna användning, är`635650083804480486` strängrepresentationen av Partitions-ID för `5008380` Service Fabric, är strängrepresentationen av Service Fabric Replica/Instance ID och är 64-bitars-ID som genereras för körningens interna användning.

## <a name="list-of-performance-counters"></a>Lista över prestandaräknare
### <a name="service-method-performance-counters"></a>Prestandaräknare för servicemetod

Körningen tillförlitlig tjänst publicerar följande prestandaräknare som är relaterade till körningen av tjänstmetoder.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service fabric-metod för service |Åkallor/sek |Antal gånger som servicemetoden anropas per sekund |
| Service fabric-metod för service |Genomsnittliga millisekunder per anrop |Tid det tar att utföra servicemetoden i millisekunder |
| Service fabric-metod för service |Undantag som har genererats/Sek |Antal gånger som servicemetoden har kastat ett undantag per sekund |

### <a name="service-request-processing-performance-counters"></a>Prestandaräknare för bearbetning av tjänstbegäran
När en klient anropar en metod via ett tjänstproxyobjekt resulterar det i att ett begärandemeddelande skickas över nätverket till remoting-tjänsten. Tjänsten bearbetar meddelandet för begäran och skickar ett svar tillbaka till klienten. Körningen Tillförlitlig ServiceRemoting publicerar följande prestandaräknare som är relaterade till bearbetning av tjänstbegäran.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service fabric-service |Antal utestående förfrågningar |Antal begäranden som behandlas i tjänsten |
| Service fabric-service |Genomsnittliga millisekunder per begäran |Tid (i millisekunder) av tjänsten för att behandla en begäran |
| Service fabric-service |Genomsnittliga millisekunder för deserialisering av begäran |Tid (i millisekunder) för att avserialisera meddelande om servicebegäran när det tas emot vid tjänsten |
| Service fabric-service |Genomsnittliga millisekunder för svarsserier |Tid (i millisekunder) för att serialisera servicesvarsmeddelandet vid tjänsten innan svaret skickas till klienten |

## <a name="next-steps"></a>Nästa steg
* [Exempelkod](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [EventSource-leverantörer i PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
