---
title: Metodtips för tidskrävande åtgärder i Azure Analysis Services | Microsoft-dokument
description: I den här artikeln beskrivs metodtips för tidskrävande åtgärder.
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428113"
---
# <a name="best-practices-for-long-running-operations"></a>Metodtips för tidskrävande åtgärder

I Azure Analysis Services representerar en *nod* en virtuell värddator där en serverresurs körs. Vissa åtgärder, till exempel tidskrävande frågor, uppdateringsåtgärder och frågeskalningssynkronisering, kan misslyckas om en serverresurs flyttas till en annan nod. Vanliga felmeddelanden i det här scenariot är:

- "Ett fel uppstod när en XMLA-begäran som körs körs. Begäran kan ha avbrutits av tjänstuppgraderingen eller omstarten av servern."
- "Jobb med ID'<guid><database>för modell ' ' avbröts på grund av servicefel (inaktivitet) med meddelandet "Avbryta uppdateringsbegäran eftersom den fastnade utan några uppdateringar. Detta är ett internt serviceproblem. Skicka in jobbet eller lämna in en biljett igen för att få hjälp om problemet uppstår upprepade gånger."

Det finns många anledningar till att långvariga operationer kan störas. Till exempel uppdateringar i Azure, till exempel: 
- Korrigeringar av operativsystem 
- Säkerhetsuppdateringar
- Uppdateringar av Azure Analysis Services-tjänsten
- Service Fabric-uppdateringar. Service Fabric är en plattformskomponent som används av ett antal Microsoft-molntjänster, inklusive Azure Analysis Services.

Förutom uppdateringar som sker i tjänsten finns det en naturlig förflyttning av tjänster över noder på grund av belastningsutjämning. Nodrörelser är en förväntad del av en molntjänst. Azure Analysis Services försöker minimera påverkan från nodrörelser, men det är omöjligt att eliminera dem helt. 

Förutom nodrörelser finns det andra fel som kan uppstå. Ett databassystem för datakällan kan till exempel vara offline eller nätverksanslutningen går förlorad. Om en partition under uppdateringen har 10 miljoner rader och ett fel inträffar på den 9 miljonde raden, finns det inget sätt att starta om uppdateringen vid felpunkten. Tjänsten måste börja om från början. 

## <a name="refresh-rest-api"></a>Uppdatera REST API

Avbrott i tjänsten kan vara utmanande för tidskrävande åtgärder som datauppdatering. Azure Analysis Services innehåller ett REST API för att minska negativa effekter från avbrott i tjänsten. Mer information finns i [Asynkron uppdatering med REST API](analysis-services-async-refresh.md).
 
Förutom REST API finns det andra metoder som du kan använda för att minimera potentiella problem under tidskrävande uppdateringsåtgärder. Huvudmålet är att undvika att behöva starta om uppdateringsåtgärden från början och i stället utföra uppdateringar i mindre batchar som kan bekräftas i etapper. 
 
REST API möjliggör en sådan omstart, men det tillåter inte full flexibilitet för partition skapande och radering. Om ett scenario kräver komplexa datahanteringsåtgärder bör din lösning innehålla någon form av batchbearbetning i logiken. Genom att till exempel använda transaktioner för att bearbeta data i flera, tillåter separata batchar att det inte krävs omstart från början, utan i stället från en mellanliggande kontrollpunkt. 
 
## <a name="scale-out-query-replicas"></a>Utskalningsfrågerepliker

Oavsett om du använder REST eller anpassad logik kan klientprogramfrågor fortfarande returnera inkonsekventa eller mellanliggande resultat medan batchar bearbetas. Om konsekventa data som returneras av klientprogramfrågor krävs under bearbetningen och modelldata är i ett mellanliggande tillstånd, kan du använda [utskalning](analysis-services-scale-out.md) med skrivskyddade frågerepliker.

Genom att använda skrivskyddade frågerepliker, medan uppdateringar utförs i batchar, kan klientprogramanvändare fortsätta att fråga den gamla ögonblicksbilden av data på skrivskyddade repliker. När uppdateringarna är klara kan en synkroniseringsåtgärd utföras för att uppdatera skrivskyddade repliker.


## <a name="next-steps"></a>Nästa steg

[Asynkron uppdatering med REST API](analysis-services-async-refresh.md)  
[Utskalning för Azure Analysis Services](analysis-services-scale-out.md)  
[Analysis Services hög tillgänglighet](analysis-services-bcdr.md)  
[Sök vägledning för Azure-tjänster igen](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

