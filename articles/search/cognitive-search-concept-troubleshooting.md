---
title: Felsökningstips för kognitiv sökning – Azure Search
description: Tips och felsökning för att konfigurera cognitive Sök pipelines i Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5f21fe3c65e37d3fee4043526762a7fafdea5cc4
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316302"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Felsökningstips för kognitiv sökning

Den här artikeln innehåller en lista med tips och knep för att hålla dig flytta när du kommer igång med funktioner för kognitiv sökning i Azure Search. 

Om du redan inte har gjort det, gå igenom den [självstudien: Lär dig att anropa cognitive search API: er](cognitive-search-quickstart-blob.md) för praxis vid tillämpning av kognitiv sökning enrichments till en blob-datakälla.

## <a name="tip-1-start-with-a-small-dataset"></a>Tips 1: Börja med en liten datamängd
Det bästa sättet att snabbt hitta problem är att öka hastigheten med vilken du kan åtgärda problem. Det bästa sättet att minska tid som indexering är genom att minska antalet dokument som ska indexeras. 

Börja med att skapa en datakälla med bara ett fåtal av dokument/poster. Dokument-exemplet ska vara en god bild av utbud av dokument som indexeras. 

Kör ditt dokument-exempel genom pipelinen för slutpunkt till slutpunkt och kontrollera att resultatet uppfyller dina behov. När du är nöjd med resultaten kan du lägga till fler filer till din datakälla.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tips 2: Kontrollera dina autentiseringsuppgifter för datakälla
Anslutningen till datakällan verifieras inte förrän du har definierat en indexerare som använder den. Om du ser några fel som nämner att indexeraren inte kommer åt data, se till att:
- Anslutningssträngen är korrekt. Särskilt när du skapar SAS-token måste du se till att använda formatet som förväntas av Azure Search. Se [så här anger du autentiseringsuppgifter avsnittet](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) vill veta mer om de olika format som stöds.
- Behållarens namn i indexeraren är korrekt.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tips 3: Se vad som fungerar även om det finns några fel
Ibland stoppar små fel en indexerare i ut sina spår. Det är bra om du planerar att åtgärda problem med en i taget. Men kanske du vill ignorera en viss typ av fel, vilket gör att indexeraren att fortsätta så att du kan se vad flöden faktiskt fungerar.

I så fall kanske du vill berätta för indexeraren att ignorera fel. Göra det genom att ange *maxFailedItems* och *maxFailedItemsPerBatch* som -1 som en del av att indexerarens definition.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tips 4: Titta på Avancerad och dokument under huven 
Avancerad och dokument är tillfälliga strukturer som skapades under berikande och sedan tas bort när bearbetningen är klar.

Om du vill ta en ögonblicksbild av det berikade dokumentet som skapades under indexeringen lägger du till ett fält som heter ```enriched``` i ditt index. Indexeraren placerar automatiskt en strängrepresentation i fältet för alla berikanden för det dokumentet.

Fältet ```enriched``` innehåller en sträng som är en logisk representation av det berikade dokumentet i minnet i JSON.  Fältets värde är emellertid ett giltigt JSON-dokument. Kvoter är undantagna, så du behöver aldrig ersätta `\"` med `"` för att visa dokumenten som formaterad JSON. 

Avancerad och fält är avsett för felsökning, för att hjälpa dig att förstå logiska utformningen av det innehåll som uttryck utvärderas mot. Du bör inte lita på det här fältet för indexering syften.

Lägg till en ```enriched``` fältet som en del av din Indexdefinition för felsökning:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>Tips 5: Förväntat innehåll kan inte visas

Innehåll saknas kan vara resultatet av dokument som avbryts under indexering. Nivåerna kostnadsfri och Basic har låg begränsningar på dokumentstorlek. Alla filer som överstiger gränsen ignoreras under indexering. Du kan söka efter släppta dokument i Azure-portalen. Dubbelklicka på panelen indexerare i instrumentpanelen för search-tjänsten. Granska förhållandet mellan lyckade dokument som indexerats. Om det inte är 100%, klickar du på förhållandet mellan för att få mer information. 

Om problemet är relaterat till filstorlek, kan du se ett fel så här: ”Blob < filnamn >” har storleken på < filstorlek > byte, vilket överskrider den maximala storleken för extrahering av dokumentet för din aktuella tjänstnivå ”. Mer information om indexerare gränser finns [tjänstbegränsningar](search-limits-quotas-capacity.md).

En andra orsak för innehåll som inte visas kan vara relaterade indata/utdata Mappningsfel. Till exempel en utdatanamnet för målet är ”personer” men fältnamnet index är gemena ”användare”. Systemet kan returnera 201 meddelanden om lyckade åtgärder för hela pipelinen så att du tycker att indexering lyckades, när i själva verket ett fält är tom. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tips 6: Utöka bearbetning utöver maximal körtid (24-timmarsperiod)

Bildanalys är beräkningsmässigt-intensiva även enkla fall, så när bilderna är särskilt stora och komplexa, bearbetningstider kan överskrida den maximala tiden som tillåts. 

Maximal körtid varierar beroende på nivån: flera minuter på kostnadsfritt nivån, 24 timmars indexering på faktureringsbar nivå. Om bearbetningen kan inte slutföras inom en 24-timmarsperiod för bearbetning på begäran, växla till ett schema för att låta indexeraren fortsatte bearbetning där den avbröts. 

Indexera återupptas på schema senaste kända dokumentet för schemalagda indexerare. Med hjälp av ett återkommande schema, fungerar indexeraren genom eftersläpningen avbildningen över ett antal timmar eller dagar, tills alla icke bearbetade avbildningar behandlas. Mer information om schema-syntax finns i [steg3: Skapa en indexerare](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer).

För portalbaserad indexering (enligt beskrivningen i snabbstarten), välja ”köras en gång”-indexeraren alternativet gränser bearbetning till 1 timme (`"maxRunTime": "PT1H"`). Du kanske vill utöka fönstret bearbetning till något längre tid.

## <a name="tip-7-increase-indexing-throughput"></a>Tips 7: Öka indexeringsflöde

För [parallella indexering](search-howto-large-index.md), placera dina data i flera behållare eller flera virtuella mappar i samma behållare. Skapa sedan flera och värdepar. Alla indexerare kan använda samma kompetens och skriver till samma mål-search-index, så Sök efter appen inte behöver känna till den här partitionering.
Mer information finns i [indexering stora datauppsättningar](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Se också
+ [Snabbstart: Skapa en pipeline för kognitiv sökning i portalen](cognitive-search-quickstart-blob.md)
+ [Självstudiekurs: Lär dig cognitive search REST API: er](cognitive-search-tutorial-blob.md)
+ [Att ange autentiseringsuppgifter för datakälla](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexering av stora datauppsättningar](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Avancerad och mappning till ett index](cognitive-search-output-field-mapping.md)