---
title: Tips för AI-berikande design
titleSuffix: Azure Cognitive Search
description: Tips och felsökning för att konfigurera AI-anrikningspipelor i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3fef5db90c3ae63a8fa48835646e09f9dfe6f023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245491"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Tips för AI-anrikning i Azure Cognitive Search

Den här artikeln innehåller en lista med tips och knep för att hålla dig i rörelse när du kommer igång med AI-anrikningsfunktioner i Azure Cognitive Search. 

Om du inte redan har gjort det går du igenom [självstudien: Lär dig hur du anropar API:er för AI-anrikning](cognitive-search-quickstart-blob.md) för övning i att tillämpa AI-enrichments på en blob-datakälla.

## <a name="tip-1-start-with-a-small-dataset"></a>Tips 1: Börja med en liten datauppsättning
Det bästa sättet att hitta problem snabbt är att öka hastigheten med vilken du kan åtgärda problem. Det bästa sättet att minska indexeringstiden är att minska antalet dokument som ska indexeras. 

Börja med att skapa en datakälla med bara en handfull dokument/poster. Dokumentexemplet bör vara en bra representation av de olika dokument som ska indexeras. 

Kör dokumentexemplet via pipelinen från slutna till och kontrollera att resultaten uppfyller dina behov. När du är nöjd med resultatet kan du lägga till fler filer i datakällan.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tips 2: Kontrollera att uppgifterna för datakällan är korrekta
Datakällanslutningen valideras inte förrän du definierar en indexerare som använder den. Om du ser några fel som nämner att indexeraren inte kan komma åt data kontrollerar du att:
- Anslutningssträngen är korrekt. Speciellt när du skapar SAS-token, se till att använda det format som förväntas av Azure Cognitive Search. Läs [så här anger du avsnittet Autentiseringsuppgifter](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) om du vill veta mer om de olika format som stöds.
- Behållarnamnet i indexeraren är korrekt.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tips 3: Se vad som fungerar även om det finns vissa fel
Ibland stoppar ett litet fel en indexerare i dess spår. Det är bra om du planerar att åtgärda problem en efter en. Du kanske vill ignorera en viss typ av fel, så att indexeraren kan fortsätta så att du kan se vilka flöden som faktiskt fungerar.

I så fall kanske du vill be indexeraren att ignorera fel. Gör det genom att ställa in *maxFailedItems* och *maxFailedItemsPerBatch* som -1 som en del av indexeringsdefinitionen.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tips 4: Om du tittar på berikade dokument under huven 
Berikade dokument är tillfälliga strukturer som skapas under anrikningen och tas sedan bort när bearbetningen är klar.

Om du vill ta en ögonblicksbild av det berikade dokumentet som skapades under indexeringen lägger du till ett fält som heter ```enriched``` i ditt index. Indexeraren placerar automatiskt en strängrepresentation i fältet för alla berikanden för det dokumentet.

Fältet ```enriched``` innehåller en sträng som är en logisk representation av det berikade dokumentet i minnet i JSON.  Fältets värde är emellertid ett giltigt JSON-dokument. Kvoter är undantagna, så du behöver aldrig ersätta `\"` med `"` för att visa dokumenten som formaterad JSON. 

Det utökade fältet är endast avsett för felsökningsändamål, för att hjälpa dig att förstå den logiska formen för innehållet som uttryck utvärderas mot. Du bör inte vara beroende av det här fältet för indexering.

Lägg ```enriched``` till ett fält som en del av indexdefinitionen för felsökning:

#### <a name="request-body-syntax"></a>Begärandetextsyntax
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>Tips 5: Förväntat innehåll visas inte

Innehåll som saknas kan bero på att dokument tas bort under indexeringen. Kostnadsfria och grundläggande nivåer har låga gränser för dokumentstorlek. Alla filer som överskrider gränsen tas bort under indexeringen. Du kan söka efter ignorerade dokument i Azure-portalen. Dubbelklicka på panelen Indexers på instrumentpanelen för söktjänsten. Granska förhållandet mellan godkända dokument indexerade. Om det inte är 100%, kan du klicka på förhållandet för att få mer information. 

Om problemet är relaterat till filstorleken kan ett fel som \<det här: "Blob-filnamnet \<>" har storleken på filstorleken> byte, vilket överskrider den maximala storleken för dokumentextrahering för den aktuella tjänstnivån." Mer information om indexeringsgränser finns i [Servicegränser](search-limits-quotas-capacity.md).

En annan orsak till att innehåll som inte visas kan vara relaterade indata-/utdatamappningsfel. Ett utdatamålnamn är till exempel "Personer" men indexfältnamnet är gemener "personer". Systemet kan returnera 201 lyckade meddelanden för hela pipelinen så att du tror att indexeringen lyckades, när ett fält i själva verket är tomt. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tips 6: Utöka bearbetningen utöver maximal körtid (24-timmarsfönster)

Bildanalys är beräkningsintensiv för även enkla fall, så när bilderna är särskilt stora eller komplexa kan bearbetningstiderna överskrida den maximala tillåtna tiden. 

Maximal körtid varierar beroende på nivå: flera minuter på den kostnadsfria nivån, 24-timmars indexering på fakturerbara nivåer. Om bearbetningen inte slutförs inom en 24-timmarsperiod för bearbetning på begäran, växla till ett schema för att få indexeraren att fortsätta bearbetas där den slutade. 

För schemalagda indexerare återupptas indexeringen enligt schemat vid det senast fungerande dokumentet. Genom att använda ett återkommande schema kan indexeraren arbeta sig igenom bildeftersläpningen under en serie timmar eller dagar, tills alla obehandlade bilder bearbetas. Mer information om schemasyntax finns i [steg 3: Skapa-en-indexerare](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) eller så [här schemalägger du indexerare för Azure Cognitive Search](search-howto-schedule-indexers.md).

> [!NOTE]
> Om en indexerare är inställd på ett visst schema men upprepade gånger misslyckas på samma dokument om och om igen varje gång den körs, börjar indexeraren köras med ett mindre frekvent intervall (upp till maximalt minst en gång var 24:e timme) tills den lyckas göra framsteg Igen.  Om du tror att du har åtgärdat det problem som orsakade att indexeraren fastnade vid en viss punkt, kan du utföra en on demand-körning av indexeraren, och om det lyckas med framsteg återgår indexeraren till det inställda schemaintervallet igen.

För portalbaserad indexering (enligt beskrivningen i snabbstarten) begränsar alternativet "kör en`"maxRunTime": "PT1H"`gång" bearbetning till 1 timme ( ). Du kanske vill utöka bearbetningsfönstret till något längre.

## <a name="tip-7-increase-indexing-throughput"></a>Tips 7: Öka indexeringsdataflödet

För [parallell indexering placerar](search-howto-large-index.md)du dina data i flera behållare eller flera virtuella mappar i samma behållare. Skapa sedan flera datasource- och indexeringspar. Alla indexare kan använda samma kunskaper och skriva till samma målsökindex, så att sökappen inte behöver vara medveten om den här partitioneringen.
Mer information finns i [Indexera stora datauppsättningar](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Se även
+ [Snabbstart: Skapa en AI-anrikningspipeline i portalen](cognitive-search-quickstart-blob.md)
+ [Självstudiekurs: Lär dig REST-API:er för AI-anrikning](cognitive-search-tutorial-blob.md)
+ [Ange autentiseringsuppgifter för datakälla](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexera stora datamängder](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Så här mappar du utökade fält till ett index](cognitive-search-output-field-mapping.md)
