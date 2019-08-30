---
title: Fel söknings tips för kognitiv sökning – Azure Search
description: Tips och fel sökning för att konfigurera kognitiva Sök pipeliner i Azure Search.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.workload: search
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: luisca
ms.openlocfilehash: bc83a7c06c52ee26246329d6ca3177bce71c9de8
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186421"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Fel söknings tips för kognitiv sökning

Den här artikeln innehåller en lista med tips och knep som du kan använda för att komma igång med kognitiva Sök funktioner i Azure Search. 

Om du inte redan har gjort det kan du gå igenom [självstudien: Lär dig hur du anropar kognitiv search](cognitive-search-quickstart-blob.md) -API: er för att använda kognitiva sökmetoder i en BLOB-datakälla.

## <a name="tip-1-start-with-a-small-dataset"></a>Tips 1: Börja med en liten data mängd
Det bästa sättet att hitta problem är att snabbt öka den hastighet som du kan åtgärda problem med. Det bästa sättet att minska indexerings tiden är genom att minska antalet dokument som ska indexeras. 

Börja med att skapa en data källa med bara en fåtal av dokument/poster. Ditt dokument exempel bör vara en bättre representation av de olika dokument som kommer att indexeras. 

Kör ditt dokument exempel via pipeline från slut punkt till slut punkt och kontrol lera att resultaten uppfyller dina behov. När du är nöjd med resultaten kan du lägga till fler filer till data källan.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tips 2: Kontrol lera att autentiseringsuppgifterna för data källan är korrekta
Anslutningen till data källan verifieras inte förrän du definierar en indexerare som använder den. Om du ser några fel som nämner att indexeraren inte kan komma åt data, kontrollerar du att:
- Anslutnings strängen är korrekt. Särskilt när du skapar SAS-token, se till att använda det format som förväntas av Azure Search. Se [avsnittet](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) ange autentiseringsuppgifter för att lära dig om de olika format som stöds.
- Behållar namnet i indexeraren är korrekt.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tips 3: Se vad som fungerar även om det finns några problem
Ibland stoppar ett litet avbrott en indexerare i sina spår. Det är bra om du planerar att åtgärda problem en i taget. Men du kanske vill ignorera en viss typ av fel, så att indexeraren kan fortsätta så att du kan se vilka flöden som faktiskt fungerar.

I så fall kanske du vill be indexeraren att ignorera fel. Det gör du genom att ange *maxFailedItems* och *maxFailedItemsPerBatch* som-1 som en del av index definitions definitionen.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tips 4: Titta på berikade dokument under huven 
Omfattande dokument är temporära strukturer som skapats under anrikning och sedan tas bort när bearbetningen är klar.

Om du vill ta en ögonblicksbild av det berikade dokumentet som skapades under indexeringen lägger du till ett fält som heter ```enriched``` i ditt index. Indexeraren placerar automatiskt en strängrepresentation i fältet för alla berikanden för det dokumentet.

Fältet ```enriched``` innehåller en sträng som är en logisk representation av det berikade dokumentet i minnet i JSON.  Fältets värde är emellertid ett giltigt JSON-dokument. Kvoter är undantagna, så du behöver aldrig ersätta `\"` med `"` för att visa dokumenten som formaterad JSON. 

Det omfattande fältet är avsett för fel sökning för att hjälpa dig att förstå den logiska formen på det innehåll som uttryck utvärderas mot. Du bör inte vara beroende av det här fältet för indexerings syfte.

Lägg till ```enriched``` ett fält som en del av index definitionen för fel söknings syfte:

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

Innehåll som saknas kan vara resultatet av dokument som tas bort under indexeringen. Kostnads fria och grundläggande nivåer har låg gräns för dokument storlek. Alla filer som överskrider gränsen bryts vid indexering. Du kan söka efter borttagna dokument i Azure Portal. I instrument panelen för Sök tjänsten dubbelklickar du på panelen indexerare. Granska förhållandet mellan lyckade dokument indexerade. Om den inte är 100% kan du klicka på kvoten för att få mer information. 

Om problemet är relaterat till fil storlek kan du se ett fel meddelande som liknar detta: "BLOB \<-filens namn >" har storleken på \<fil storleken > byte, vilket överskrider den maximala storleken för dokument extrahering för den aktuella tjänst nivån. " Mer information om gränser för indexerare finns i [tjänst begränsningar](search-limits-quotas-capacity.md).

En andra orsak till att innehållet som Miss söker visas kan vara relaterade till fel vid indata/utdata-mappning. Till exempel är ett utmatnings mål namn "personer", men index fält namnet är "människor". Systemet kan returnera 201 slutförda meddelanden för hela pipelinen så att du tror att indexeringen är klar, när fältet i själva verket är tomt. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tips 6: Utöka bearbetning bortom den maximala körnings tiden (24-timmarsformat)

Bild analys är i beräknings intensiva för ännu enkla fall, så när avbildningar är särskilt stora eller komplexa kan bearbetnings tiderna överskrida den högsta tillåtna tiden. 

Maximal kör tid varierar beroende på nivå: flera minuter på den kostnads fria nivån, 24-timmars indexering på fakturerbara nivåer. Om bearbetningen inte kan slutföras inom en 24-timmarsperiod för bearbetning på begäran, byter du till ett schema så att indexeraren kan hämta bearbetningen där den slutade. 

Indexering av schemalagda indexerare återupptas enligt schema vid det senast fungerande dokumentet. Genom att använda ett återkommande schema kan indexeraren arbeta på ett sätt genom att vänta på en serie timmar eller dagar, tills alla icke bearbetade bilder bearbetas. Mer information om schema-syntax finns i [steg 3: Skapa – en-indexerare](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) eller se [hur du schemalägger indexerare för Azure Search](search-howto-schedule-indexers.md).

> [!NOTE]
> Om en indexerare har angetts till ett visst schema men upprepade gånger Miss lyckas i samma dokument över och över igen varje gång den körs, kommer indexeraren att köras på ett mindre frekvent intervall (upp till max minst en gång var 24: e timme) tills det har gjort en förloppet AGA för.  Om du tror att du har åtgärdat problemet som gjorde att indexeraren har fastnat vid en viss tidpunkt, kan du utföra en körning av indexeraren på begäran, och om detta sker fortsätter indexeraren med angivet schema intervall igen.

För portalbaserade indexeringar (enligt beskrivningen i snabb starten) väljer du alternativet "kör en gång"-alternativ begränsar bearbetningen till 1 timme (`"maxRunTime": "PT1H"`). Du kanske vill utöka bearbetnings fönstret till något längre.

## <a name="tip-7-increase-indexing-throughput"></a>Tips 7: Öka indexering av data flöde

För [parallell indexering](search-howto-large-index.md)ska du placera dina data i flera behållare eller flera virtuella mappar i samma behållare. Skapa sedan flera DataSource-och Indexer-par. Alla indexerare kan använda samma färdigheter och skriva till samma mål Sök index, så din Sökapp behöver inte vara medveten om denna partitionering.
Mer information finns i [Indexera stora data uppsättningar](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Se också
+ [Snabbstart: Skapa en kognitiv Sök-pipeline i portalen](cognitive-search-quickstart-blob.md)
+ [Självstudier: Läs om REST-API: er för kognitiv sökning](cognitive-search-tutorial-blob.md)
+ [Ange autentiseringsuppgifter för data Källa](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexera stora data uppsättningar](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Så här mappar du omfattande fält till ett index](cognitive-search-output-field-mapping.md)
