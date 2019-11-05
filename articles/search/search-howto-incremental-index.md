---
title: Konfigurera stegvis indexering av fördefinierad innehålls baserad ändrings spårning
titleSuffix: Azure Cognitive Search
description: Aktivera ändrings spårning och bevara tillstånd för berikat innehåll för kontrollerad bearbetning i en kognitiv färdigheter.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ac082d6ecb6624dc0d5bc0ab927ff8b91ebdabce
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512189"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>Så här konfigurerar du stegvis indexering av berikade dokument i Azure Kognitiv sökning

Den här artikeln visar hur du lägger till tillstånd och cachelagring i dokument som flyttas genom en Azure Kognitiv sökning-rikare pipeline så att du kan stegvis indexera dokument från någon av de data källor som stöds. Som standard är en färdigheter tillstånds lös, och om du ändrar någon del av dess sammansättning krävs en fullständig körning av indexeraren. Med stegvis indexering kan indexeraren avgöra vilka delar av pipelinen som har ändrats, återanvända befintliga anrikninger för oförändrade delar och ändra berikade delar för de steg som ändras. Cachelagrat innehåll placeras i Azure Storage.

Om du inte är bekant med att konfigurera indexerare börjar du med [indexerare översikt](search-indexer-overview.md) och fortsätter sedan till [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md) för att lära dig om anriknings pipelines. Mer bakgrunds information om viktiga begrepp finns i [stegvis indexering](cognitive-search-incremental-indexing-conceptual.md).

Stegvis indexering konfigureras med hjälp av [search REST API-version = 2019-05 -06 – för hands version](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations).

> [!NOTE]
> Den här funktionen är inte tillgänglig ännu i portalen och måste användas program mässigt.
>

## <a name="modify-an-existing-indexer"></a>Ändra en befintlig indexerare

Om du har en befintlig indexerare, följer du dessa steg för att aktivera stegvis indexering.

### <a name="step-1-get-the-indexer"></a>Steg 1: Hämta indexeraren

Börja med en giltig, befintlig indexerare som har den nödvändiga data källan och indexet redan definierat. Indexeraren ska vara körbara. Med hjälp av en API-klient skapar du en [Get-begäran](https://docs.microsoft.com/rest/api/searchservice/get-indexer) för att hämta den aktuella konfigurationen för den indexerare som du vill lägga till stegvis indexering för.

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>Steg 2: Lägg till egenskapen cache

Redigera svaret från GET-begäran för att lägga till egenskapen `cache` till indexeraren. Cacheobjektet kräver bara en enda egenskap och det är anslutnings strängen till ett Azure Storage konto.

```json
    "cache": {
        "storageConnectionString": "[your storage connection string]"
    }
```

### <a name="step-3-reset-the-indexer"></a>Steg 3: återställa indexeraren

> [!NOTE]
> Om du återställer indexeraren kommer alla dokument i data källan att bearbetas igen så att innehållet kan cachelagras. Alla kognitiva anrikninger kommer att köras på nytt i alla dokument.
>

En återställning av indexeraren krävs när du konfigurerar stegvis indexering för befintliga indexerare så att alla dokument är i ett konsekvent tillstånd. Återställ indexeraren med hjälp av [REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>Steg 4: Spara den uppdaterade definitionen

Uppdatera Indexer-definitionen med en Request-begäran ska bröd texten i begäran innehålla den uppdaterade index definitions definitionen.

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

Om du nu utfärdar en annan GET-begäran på indexeraren, kommer svaret från tjänsten att innehålla en `cacheId`-egenskap i objektet cache. `cacheId` är namnet på den behållare som innehåller alla cachelagrade resultat och mellanliggande tillstånd för varje dokument som bearbetas av denna indexerare.

## <a name="enable-incremental-indexing-on-new-indexers"></a>Aktivera stegvis indexering på nya indexerare

Om du vill ställa in stegvis indexering för en ny indexerare, inkluderar du egenskapen `cache` i nytto lasten Indexer definition. Se till att du använder `2019-05-06-Preview`-versionen av API: et.

## <a name="overriding-incremental-indexing"></a>Åsidosätt stegvis indexering

När den är konfigurerad spårar stegvis indexering ändringar i din pipeline för indexering och bevarar dokumenten till eventuell konsekvens över ditt index och prognoser. I vissa fall måste du åsidosätta det här beteendet för att se till att indexeraren inte gör ytterligare arbete som ett resultat av en uppdatering av indexerings pipelinen. Uppdatering av anslutnings strängen för data källan kräver till exempel att en indexerare återställs och indexeras om för alla dokument när data källan har ändrats. Men om du bara uppdaterar anslutnings strängen med en ny nyckel vill du inte att ändringen ska leda till några uppdateringar av befintliga dokument. Däremot kan du vilja indexeraren att ogiltig förklara cachen och utöka dokumenten även om inga ändringar görs i indexerings pipelinen. Till exempel kanske du vill ogiltig förklara indexeraren om du skulle distribuera om en anpassad färdighet med en ny modell och ville att kompetensen körs på alla dina dokument.

### <a name="override-reset-requirement"></a>Åsidosätt återställnings krav

När du gör ändringar i indexerings pipelinen kräver alla ändringar som resulterar i en invaliditet av cacheminnet att en indexerare återställs. Om du gör en ändring i pipelinen för indexeraren och inte vill att den ska göra en ogiltig validering av cacheminnet måste du ange parametern `ignoreResetRequirement` QueryString för att `true` för åtgärder på indexeraren eller data källan.

### <a name="override-change-detection"></a>Åsidosätt ändrings identifiering

När du gör uppdateringar av färdigheter som leder till att dokument flaggas som inkonsekventa, till exempel uppdatering av en anpassad färdighets-URL när kunskapen omdistribueras, anger du parametern `disableCacheReprocessingChangeDetection` frågesträng som ska `true` på färdigheter-uppdateringar.

### <a name="force-change-detection"></a>Framtvinga ändrings identifiering

Instansen när du vill att indexerings pipelinen ska identifiera en ändring i en extern entitet, t. ex. att distribuera en ny version av en anpassad färdighet, måste du uppdatera färdigheter och "touch" den specifika kompetensen genom att redigera kunskaps definitionen, särskilt URL: en för att tvinga ändra identifiering och ogiltig cachelagring av den aktuella kompetensen.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskriver stegvis indexering för indexerare som innehåller färdighetsuppsättningar. Om du vill få mer information om dina kunskaper kan du läsa artiklarna om att indexera om i allmänhet, som är tillämpliga för alla indexerings scenarier i Azure Kognitiv sökning.

+ [Återskapa ett Azure kognitiv sökning-index](search-howto-reindex.md). 
+ [Så här indexerar du stora data mängder i Azure kognitiv sökning](search-howto-large-index.md). 
