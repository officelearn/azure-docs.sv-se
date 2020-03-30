---
title: Undantag för Azure Service Bus Resource Manager | Microsoft-dokument
description: Lista över undantag för Service Bus som visas av Azure Resource Manager och föreslagna åtgärder.
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
ms.openlocfilehash: 0f328651ac4422226071d2de12e9cbc787ef64be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978263"
---
# <a name="service-bus-resource-manager-exceptions"></a>Undantag för Service Bus Resource Manager

I den här artikeln visas undantag som genereras när du interagerar med Azure Service Bus med Azure Resource Manager – via mallar eller direktsamtal.

> [!IMPORTANT]
> Det här dokumentet uppdateras ofta. Kom tillbaka för uppdateringar.

Nedan visas de olika undantag/fel som visas via Azure Resource Manager.

## <a name="error-bad-request"></a>Fel: Felaktig begäran

"Felaktig begäran" innebär att begäran som togs emot av Resource Manager misslyckades validering.

| Felkod | Felunderkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Felaktig begäran | 40000 | Underkod=40000. Egenskapen *"egenskapsnamn"* kan inte anges när du skapar en kö eftersom *namnområdets namnområdesnamn* använder "Basic"-nivån. Den här åtgärden stöds endast på standard- eller Premium-nivå. | På Azure Service Bus Basic-nivå kan nedanstående egenskaper inte ställas in eller uppdateras - <ul> <li> KräverDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession (KräverSession)</li> <li>DefaultMessageTimeToLive </li> <li> DupliceraDetectionHistoryTimeWindow </li> <li> AktiveraExpress </li> <li> FramåtTill </li> <li> Ämnen </li> </ul> | Överväg att uppgradera från basic till standard- eller premiumnivå för att använda den här funktionen. |
| Felaktig begäran | 40000 | Underkod=40000. Värdet för egenskapen "requiresDuplicateDetection" för en befintlig kö(eller ämne) kan inte ändras. | Dubblettidentifiering måste aktiveras/inaktiveras när entiteten skapas. Konfigurationsparametern för dubblettidentifiering kan inte ändras när den har skapats. | Om du vill aktivera dubblettidentifiering i en tidigare skapad kö/ett ämne kan du skapa en ny kö/ett nytt ämne med dubblettidentifiering och sedan vidarebefordra från den ursprungliga kön till den nya kön/det nya avsnittet. |
| Felaktig begäran | 40000 | Underkod=40000. Det angivna värdet 16384 är ogiltigt. Egenskapen "MaxSizeInMegabytes", måste vara ett av följande värden: 1024;2048;3072;4096;5120. | Värdet MaxSizeInMegabytes är ogiltigt. | Se till att MaxSizeInMegabytes är något av följande - 1024, 2048, 3072, 4096, 5120. |
| Felaktig begäran | 40000 | Underkod=40000. Det går inte att ändra partitioneringen för kö/ämne. | Det går inte att ändra partitioneringen för entiteten. | Skapa en ny entitet (kö eller ämne) och aktivera partitioner. | 
| Felaktig begäran | ingen | Namnområdet *"namnområdesnamn"* finns inte. | Namnområdet finns inte i din Azure-prenumeration. | För att lösa det här felet, försök med nedanstående <ul> <li> Kontrollera att Azure-prenumerationen är korrekt. </li> <li> Kontrollera att namnområdet finns. </li> <li> Kontrollera att namnområdesnamnet är korrekt (inga stavfel eller null-strängar). </li> </ul> | 
| Felaktig begäran | 40400 | Underkod=40400. Målentiteten för automatisk vidarebefordran finns inte. | Målet för målentiteten för automatisk vidarebefordran finns inte. | Målentiteten (kö eller ämne) måste finnas innan källan skapas. Försök igen när du har skapat målentiteten. |
| Felaktig begäran | 40000 | Underkod=40000. Den angivna låstiden överstiger den tillåtna maximala "5" minuter. | Den tid för vilken ett meddelande kan låsas måste vara mellan 1 minut (minimum) och 5 minuter (max). | Se till att den medföljande låstiden är mellan 1 min och 5 minuter. |
| Felaktig begäran | 40000 | Underkod=40000. Det går inte att aktivera egenskapen DelayedPersistence och RequiresDuplicateDetection tillsammans. | Entiteter med dubblettidentifiering aktiverad på dem måste vara beständiga, så persistens kan inte fördröjas. | Läs mer om [dubblettidentifiering](duplicate-detection.md) |
| Felaktig begäran | 40000 | Underkod=40000. Värdet för egenskapen RequiresSession för en befintlig kö kan inte ändras. | Stöd för sessioner bör aktiveras när entiteten skapas. När du har skapat kan du inte aktivera/inaktivera sessioner på en befintlig entitet (kö eller prenumeration) | Ta bort och återskapa en ny kö (eller prenumeration) med egenskapen "RequiresSession" aktiverad. |
| Felaktig begäran | 40000 | Underkod=40000. "URI_PATH" innehåller tecken som inte är tillåtna av Service Bus. Entitetssegment kan bara innehålla bokstäver, siffror, punkter(.), bindestreck(-) och understreck(_). | Entitetssegment kan bara innehålla bokstäver, siffror, punkter(.), bindestreck(-) och understreck(_). Alla andra tecken gör att begäran misslyckas. | Kontrollera att det inte finns några ogiltiga tecken i URI-sökvägen. |


## <a name="error-code-429"></a>Felkod: 429

Precis som i HTTP, "Felkod 429" anger "för många förfrågningar". Det innebär att den specifika resursen (namnområdet) begränsas på grund av för många begäranden (eller på grund av motstridiga åtgärder) på den resursen.

| Felkod | Felunderkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Underkod=50004. Begäran avbröts eftersom namnområdet *som namnområdet* begränsas. | Det här feltillståndet uppstår när antalet inkommande begäranden överskrider resursens begränsning. | Vänta några sekunder och försök igen. <br/> <br/> Läs mer om begränsningarna för [kvoter](service-bus-quotas.md) och [Azure Resource Manager-begäran](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | Underkod=40901. En annan konfliktåtgärd pågår. | En annan åtgärd i konflikt pågår för samma resurs/entitet | Vänta tills den aktuella pågående åtgärden har slutförts innan du försöker igen. |
| 429 | 40900 | Underkod=40900. Konflikt. Du begär en åtgärd som inte är tillåten i resursens aktuella tillstånd. | Det här villkoret kan uppstå när flera begäranden görs för att utföra åtgärderna på samma entitet (kö, ämne, prenumeration eller regel) samtidigt. | Vänta några sekunder och försök igen |
| 429 | 40901 | Begäran om *entitetsnamn i* konflikt med en annan begäran | En annan åtgärd i konflikt pågår för samma resurs/entitet | Vänta tills den föregående åtgärden har slutförts innan du försöker igen |
| 429 | 40901 | En annan uppdateringsbegäran pågår för entitetens *entitetsnamn.* | En annan åtgärd i konflikt pågår för samma resurs/entitet | Vänta tills den föregående åtgärden har slutförts innan du försöker igen |
| 429 | ingen | Resurskonflikten inträffade. En annan konfliktåtgärd kan vara på gång. Om detta är ett nytt försök för misslyckade åtgärder väntar fortfarande rensning i bakgrunden. Försök igen senare. | Det här villkoret kan uppstå när det finns en väntande åtgärd mot samma entitet. | Vänta tills den föregående åtgärden har slutförts innan du försöker igen. |


## <a name="error-code-not-found"></a>Felkod: Hittades inte

Den här felklassen anger att resursen inte hittades.

| Felkod | Felunderkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Hittades inte | ingen | Det gick inte att hitta *entitetens entitetsnamn.* | Den enhet som åtgärden inte hittades mot. | Kontrollera om entiteten finns och försök igen. |
| Hittades inte | ingen | Hittades inte. Operationen existerar inte. | Åtgärden som du försöker utföra finns inte. | Kontrollera åtgärden och försök igen. |
| Hittades inte | ingen | Den inkommande begäran känns inte igen som en namnområdesprincipsförstämd begäran. | Den inkommande begärandetexten är null och kan därför inte köras som en put-begäran. | Kontrollera begäran kroppen att se till att det inte är null. | 
| Hittades inte | ingen | Det gick inte att hitta *meddelandeentitetens entitetsnamn.* | Det gick inte att hitta entiteten som du försöker köra åtgärden mot. | Kontrollera om entiteten finns och försök igen. |

## <a name="error-code-internal-server-error"></a>Felkod: Internt serverfel

Den här felklassen indikerar att det uppstod ett internt serverfel

| Felkod | Felunderkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Internt serverfel | 50000 | Underkod=50000. Internt serverfel| Kan hända av olika skäl. Några av symptomen är - <ul> <li> Klientbegäran/brödtext är skadad och leder till ett fel. </li> <li> Klientbegäran har åtgärdats på grund av bearbetningsproblem på tjänsten. </li> </ul> | För att lösa detta <ul> <li> Kontrollera att parametrarna för begäranden inte är null- eller felformaterade. </li> <li> Försök igen. </li> </ul> |

## <a name="error-code-unauthorized"></a>Felkod: Obehörig

Den här felklassen anger att det inte finns någon behörighet att köra kommandot.

| Felkod | Felunderkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Behörighet saknas | ingen | Ogiltig åtgärd på det sekundära namnområdet. Sekundärt namnområde är skrivskyddat. | Åtgärden utfördes mot det sekundära namnområdet, som är inställt som ett läsnamnområde. | Försök igen mot det primära namnområdet. Läs mer om [sekundärt namnområde](service-bus-geo-dr.md) |
| Behörighet saknas | ingen | SaknadToken: Auktoriseringshuvudet hittades inte. | Det här felet uppstår när auktoriseringen har null eller felaktiga värden. | Kontrollera att tokenvärdet som nämns i auktoriseringshuvudet är korrekt och inte null. |