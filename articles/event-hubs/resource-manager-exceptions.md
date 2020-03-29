---
title: Azure Event Hubs – Undantag för Resource Manager | Microsoft-dokument
description: Lista över undantag för Azure Event Hubs som visas av Azure Resource Manager och föreslagna åtgärder.
services: service-bus-messaging
documentationcenter: na
author: spelluru
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2019
ms.author: spelluru
ms.openlocfilehash: e6ee1137fce97cbe5a64aa5287223f6ba09dcf47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74936092"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs – Undantag för Resource Manager
I den här artikeln visas undantag som genereras när du interagerar med Azure Event Hubs med Azure Resource Manager – via mallar eller direktsamtal.

> [!IMPORTANT]
> Det här dokumentet uppdateras ofta. Kom tillbaka för uppdateringar.

Följande avsnitt innehåller olika undantag/fel som visas via Azure Resource Manager.

## <a name="error-code-conflict"></a>Felkod: Konflikt

| Felkod | Felunderkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Konflikt | 40300 | Det maximala antalet resurser av typen EventHub har nåtts eller överskridits. Faktiska: #, Max tillåtet: # | Namnområdet har nått sin [kvot](event-hubs-quotas.md) för antalet eventhubbar som det kan innehålla. | Ta bort oanvända eller ovidkommande händelsehubbar från namnområdet eller överväg att uppgradera till ett [dedikerat kluster](event-hubs-dedicated-overview.md). |
| Konflikt | ingen | Det går inte att ta bort config-konfiguration (Disaster Recovery) eftersom replikeringen pågår. Växla över eller bryta ihop parning innan du försöker ta bort DR Config. | [GeoDR-replikering](event-hubs-geo-dr.md) pågår, så config kan inte tas bort just nu. | Om du vill ta bort blockeringen av konfigurationen väntar du antingen tills replikeringen har slutförts, utlöser en redundans eller bryter GeoDR-parkopplingen. |
| Konflikt | ingen | Namnområdesuppdateringen misslyckades med konflikt i backend. | En annan åtgärd görs för närvarande på det här namnområdet. | Vänta tills den aktuella åtgärden är klar och försök sedan igen. |

## <a name="error-code-429"></a>Felkod: 429

| Felkod | Felunderkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | ingen | Etablerar namnområde i övergången | En annan åtgärd görs för närvarande på det här namnområdet. | Vänta tills den aktuella åtgärden är klar och försök sedan igen. |
| 429 | ingen | Haveriberedskapsåtgärd pågår. | En [GeoDR-åtgärd](event-hubs-geo-dr.md) utförs för närvarande på det här namnområdet eller ihopkopplingen. | Vänta tills den aktuella GeoDR-åtgärden är klar och försök sedan igen. |

## <a name="error-code-badrequest"></a>Felkod: BadRequest

| Felkod | Felunderkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | PartitionCount kan inte ändras för en händelsehubb. | Grundläggande eller standardnivå för Azure Event Hubs stöder inte ändring av partitioner. | Skapa en ny händelsehubb med önskat antal partitioner i ditt namnområde på grundnivå eller standardnivå. Partitionskalning stöds för [dedikerade kluster](event-hubs-dedicated-overview.md). |
| BadRequest | 40000 | Värdet #' för MessageRetentionInDays är inte giltigt för basic-nivån. värdet får inte överstiga "1"-dagar. | Namnområden för grundläggande nivåhändelsehubbar stöder endast bevarande av meddelanden på upp till 1 dag. | Om mer än en dag av meddelandekvarhållning [önskas skapar du ett standardnamnområde för eventhubbar](event-hubs-create.md). | 
| BadRequest | ingen | Det angivna namnet är inte tillgängligt. | Namnområdesnamn måste vara unika och det angivna namnet har redan tagits. | Om du äger det befintliga namnområdet med det angivna namnet kan du ta bort det, vilket medför dataförlust. Försök sedan igen med samma namn. Om namnområdet inte är säkert att ta bort (eller om du inte är ägare) väljer du ett annat namnområdesnamn. |
| BadRequest | ingen | Den angivna prenumerationen har nått sin kvot för namnområden. | Din prenumeration har nått [kvoten](event-hubs-quotas.md) för det antal namnområden som den kan innehålla. | Överväg att ta bort oanvända namnområden i den här prenumerationen, skapa en annan prenumeration eller uppgradera till ett [dedikerat kluster](event-hubs-dedicated-overview.md). |
| BadRequest | ingen | Det går inte att uppdatera ett namnområde som är sekundärt | Det går inte att uppdatera namnområdet eftersom det är det sekundära namnområdet i en [GeoDR-koppling](event-hubs-geo-dr.md). | Om det är lämpligt gör du ändringen av det primära namnområdet i den här parkopplingen i stället. Annars bryta GeoDR ihopkoppling för att göra ändringen. |
| BadRequest | ingen | Det går inte att ställa in Automatisk uppblåsning i grundläggande SKU | Automatisk uppblåsning kan inte aktiveras på grundläggande nivå Event Hubs namnområden. | Om du vill [aktivera Automatisk uppblåsning](event-hubs-auto-inflate.md) på ett namnområde kontrollerar du att den är av standardnivå. |
| BadRequest | ingen | Det finns inte tillräckligt med kapacitet för att skapa namnområdet. Kontakta administratören för eventhubbar. | Den valda regionen har kapacitet och fler namnområden kan inte skapas. | Välj en annan region för att hysa ditt namnområde. |
| BadRequest | ingen | Åtgärden kan inte utföras på entitetstypen "ConsumerGroup" eftersom namnområdets namnområdesnamn använder basic-nivån.  | Namnområden för grundläggande nivåhändelsehubbar har en [kvot]((event-hubbar-kvoter.md#event-hubs-basic-and-standard---quotas-and-limits) för en konsumentgrupp (standard). Det går inte att skapa fler konsumentgrupper. | Fortsätt att använda standardkonsumentgruppen ($Default) eller om fler behövs kan du överväga att använda ett standardgrupp för eventhubbar på nivå i stället. | 
| BadRequest | ingen | Namnområdets namnområdesnamn finns inte. | Det gick inte att hitta det angivna namnområdet. | Dubbelkolla att namnområdets namn är korrekt och finns i din prenumeration. Om så inte är fallet [skapar du ett namnområde för händelsehubbar](event-hubs-create.md). | 
| BadRequest | ingen | Resursens platsegenskap matchar inte dess namnområde. | Det gick inte att skapa en händelsenav i en viss region eftersom den inte matchade namnområdets region. | Prova att skapa händelsehubben i samma region som namnområdet. | 

## <a name="error-code-internal-server-error"></a>Felkod: Internt serverfel

| Felkod | Felunderkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Internt serverfel | ingen | Internt serverfel. | Tjänsten Event Hubs hade ett internt fel. | Försök igen. Om åtgärden fortsätter att misslyckas kontaktar du supporten. |