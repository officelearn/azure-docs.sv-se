---
title: Azure Service Bus Resource Manager-undantag | Microsoft Docs
description: Lista över Service Bus undantag som har inAzure Resource Manager och föreslagna åtgärder.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: 67e95133b9d78823f37ba48f291175ae8e9058d6
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703562"
---
# <a name="service-bus-resource-manager-exceptions"></a>Undantag för Service Bus Resource Manager

Den här artikeln innehåller undantag som genereras när du interagerar med Azure Service Bus som använder Azure Resource Manager via mallar eller direkta anrop.

> [!IMPORTANT]
> Det här dokumentet uppdateras ofta. Kom tillbaka efter uppdateringar.

Nedan visas de olika undantag/fel som finns på Azure Resource Manager.

## <a name="error-bad-request"></a>Fel: Felaktig begäran

"Felaktig begäran" innebär att begäran som mottagits av resurs hanteraren misslyckades med verifieringen.

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Felaktig begäran | 40000 | Subcode = 40000. Egenskapens *egenskaps namn* kan inte anges när du skapar en kö eftersom namn områdets namn områdes *namn* använder Basic-nivån. Den här åtgärden stöds endast i nivån standard eller Premium. | På Azure Service Bus Basic-nivån kan inte nedanstående egenskaper anges eller uppdateras – <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Ämnen </li> </ul> | Överväg att uppgradera från nivån Basic till standard eller Premium för att utnyttja den här funktionen. |
| Felaktig begäran | 40000 | Subcode = 40000. Det går inte att ändra värdet för egenskapen "requiresDuplicateDetection" för en befintlig kö (eller ämne). | Dubblettidentifiering måste aktive ras/inaktive ras när entiteten skapas. När den har skapats går det inte att ändra konfigurations parametern för dubblettidentifiering. | Om du vill aktivera dubblettidentifiering i en tidigare skapad kö/ett ämne kan du skapa en ny kö/ett ämne med dubblettidentifiering och sedan vidarebefordra från den ursprungliga kön till den nya kön/det nya avsnittet. |
| Felaktig begäran | 40000 | Subcode = 40000. Det angivna värdet 16384 är ogiltigt. Egenskapen MaxSizeInMegabytes måste vara något av följande värden: 1024; 2048; 3072; 4096; 5 120. | MaxSizeInMegabytes-värdet är ogiltigt. | Se till att MaxSizeInMegabytes är en av följande: 1024, 2048, 3072, 4096, 5120. |
| Felaktig begäran | 40000 | Subcode = 40000. Det går inte att ändra partitionering för kö/ämne. | Det går inte att ändra partitionering för entiteten. | Skapa en ny entitet (kö eller ämne) och aktivera partitioner. | 
| Felaktig begäran | inga | Namn *områdets namn område* finns inte. | Namn området finns inte i din Azure-prenumeration. | Försök att lösa det här felet <ul> <li> Se till att Azure-prenumerationen är korrekt. </li> <li> Se till att namn området finns. </li> <li> Kontrol lera att namn områdets namn är korrekt (inga stavfel eller null-strängar). </li> </ul> | 
| Felaktig begäran | 40400 | Subcode = 40400. Målentiteten för automatisk vidarebefordran finns inte. | Mål enheten för den vidarebefordrande målentiteten finns inte. | Målentiteten (kö eller ämne) måste finnas innan källan skapas. Försök igen när du har skapat målentiteten. |
| Felaktig begäran | 40000 | Subcode = 40000. Den angivna lås tiden överskrider den tillåtna max längden på 5 minuter. | Den tid som ett meddelande kan låsas upp måste vara mellan 1 minut (minst) och 5 minuter (max). | Se till att den angivna lås tiden är mellan 1 min och 5 minuter. |
| Felaktig begäran | 40000 | Subcode = 40000. Det går inte att aktivera både DelayedPersistence och RequiresDuplicateDetection-egenskapen tillsammans. | Entiteter med dubblettidentifiering aktiverat på dem måste vara permanenta, så persistence kan inte fördröjas. | Läs mer om [dubblettidentifiering](duplicate-detection.md) |
| Felaktig begäran | 40000 | Subcode = 40000. Det går inte att ändra värdet för egenskapen RequiresSession för en befintlig kö. | Stöd för sessioner måste vara aktiverat när entiteten skapas. När du har skapat kan du inte aktivera/inaktivera sessioner på en befintlig entitet (kö eller prenumeration) | Ta bort och återskapa en ny kö (eller prenumeration) med egenskapen "RequiresSession" aktive rad. |
| Felaktig begäran | 40000 | Subcode = 40000. ' URI_PATH ' innehåller tecknen som inte tillåts av Service Bus. Enhets segment får bara innehålla bokstäver, siffror, punkter (.), bindestreck (-) och under streck (_). | Enhets segment får bara innehålla bokstäver, siffror, punkter (.), bindestreck (-) och under streck (_). Andra tecken gör att begäran inte kan utföras. | Se till att det inte finns några ogiltiga tecken i URI-sökvägen. |


## <a name="error-code-429"></a>Felkod: 429

Precis som i HTTP visar "Felkod 429" för många begär Anden. Det innebär att den angivna resursen (namnrymd) begränsas på grund av för många begär Anden (eller på grund av motstridiga åtgärder) på den resursen.

| Felkod | Fel under kod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcode = 50004. Begäran avbröts eftersom namn området *där namn området* begränsas. | Det här fel tillståndet inträffar när antalet inkommande begär Anden överskrider resurs begränsningen. | Vänta några sekunder och försök igen. <br/> <br/> Läs mer om [kvoter](service-bus-quotas.md) och [begränsningar för Azure Resource Manager begär Anden](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | Subcode = 40901. En annan motstridig åtgärd pågår. | En annan motstridig åtgärd pågår på samma resurs/entitet | Vänta tills den pågående pågående åtgärden har slutförts innan du försöker igen. |
| 429 | 40900 | Subcode = 40900. Uppstod. Du begär en åtgärd som inte är tillåten i resursens aktuella tillstånd. | Detta tillstånd kan uppstå när flera begär Anden görs för att utföra åtgärderna på samma entitet (kö, ämne, prenumeration eller regel) på samma gång. | Vänta några sekunder och försök igen |
| 429 | inga | Resurs konflikt uppstod. En annan motstridig åtgärd kanske pågår. Om det här är ett nytt försök för den misslyckade åtgärden, väntar det fortfarande på att rensa i bakgrunden. Försök igen senare. | Detta tillstånd kan uppstå när det finns en väntande åtgärd mot samma entitet. | Vänta tills den föregående åtgärden har slutförts innan du försöker igen. |

