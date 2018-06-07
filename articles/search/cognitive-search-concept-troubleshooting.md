---
title: Felsökningstips för kognitiva sökning i Azure Search | Microsoft Docs
description: Tips och felsökning för att ställa in kognitiva söka pipelines i Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 3c3f9a0d0dc40de6c62c21dab0f11a501829ef11
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640973"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Felsökningstips för kognitiva sökning

Den här artikeln innehåller en lista med tips och råd så att du flyttar när du kommer igång med kognitiva sökfunktioner i Azure Search. 

Om du redan inte har gjort det, gå igenom den [Självstudier: Lär dig hur du anropar kognitiva sökningen API: er](cognitive-search-quickstart-blob.md) övning vid tillämpning av kognitiva Sök enrichments till en blob-datakälla.

## <a name="tip-1-start-with-a-small-dataset"></a>Tips 1: Starta med en liten datamängd
Det bästa sättet att snabbt hitta problem är att öka hastigheten med vilken du kan lösa problem. Det bästa sättet att minska tid som indexerings är genom att minska antalet dokument som indexeras. 

Börja med att skapa en datakälla med bara ett fåtal av dokument-poster. Ditt dokument exempel ska vara en bra representation av de olika typer av dokument som indexeras. 

Kör ditt dokument exempel via pipeline slutpunkt till slutpunkt och kontrollera att resultatet uppfyller dina behov. När du är nöjd med resultaten kan du lägga till fler filer till datakällan.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tips 2: Kontrollera att autentiseringsuppgifterna för datakällan är korrekta
Anslutningen till datakällan verifieras inte förrän du har definierat en indexerare som använder den. Om det visas fel nämna att indexeraren inte kan komma åt data, kontrollerar du att:
- Anslutningssträngen är korrekt. Särskilt när du skapar SAS-token, se till att använda formatet som förväntas av Azure Search. Se [så här anger du autentiseringsuppgifter avsnittet](
https://docs.microsoft.com/en-us/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) att lära dig om de olika format som stöds.
- Ditt behållarnamn i indexeraren är korrekt.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tips 3: Se vad som fungerar även om det finns några fel
Ibland stoppar ett litet fel en indexerare i dess spår. Det är bra om du planerar att åtgärda problem i taget. Men kanske du vill ignorera en viss typ av fel, vilket gör att indexeraren ska fortsätta så att du kan se vad flöden faktiskt fungerar.

I så fall kanske du vill berätta för indexeraren att ignorera felen. Gör det genom att ange *maxFailedItems* och *maxFailedItemsPerBatch* 1 som en del av definitionen indexeraren.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tips 4: Tittar på utökade dokument under huven 
Utökade dokument är tillfällig strukturer som skapades under berikande och tar bort när bearbetningen är klar.

Om du vill ta en ögonblicksbild av det berikade dokumentet som skapades under indexeringen lägger du till ett fält som heter ```enriched``` i ditt index. Indexeraren placerar automatiskt en strängrepresentation i fältet för alla berikanden för det dokumentet.

Fältet ```enriched``` innehåller en sträng som är en logisk representation av det berikade dokumentet i minnet i JSON.  Fältets värde är emellertid ett giltigt JSON-dokument. Kvoter är undantagna, så du behöver aldrig ersätta `\"` med `"` för att visa dokumenten som formaterad JSON. 

Utökade fält är avsett för felsökning, för att hjälpa dig att förstå det innehåll som uttryck utvärderas mot logiska formen. Du bör inte beror på det här fältet för indexering syften.

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

## <a name="tip-5-expected-content-fails-to-appear"></a>Tips 5: Förväntade innehåll kan inte visas

Saknat innehåll kan vara resultatet av dokument avbryts vid indexering. Kostnadsfria grundläggande nivåer och har låg begränsningar på storlek. Alla filer som överstiger gränsen har släppts under indexeringen. Du kan söka efter släppta dokument i Azure-portalen. Dubbelklicka på panelen indexerare i instrumentpanelen för search-tjänsten. Granska förhållandet mellan lyckade dokument indexeras. Du kan klicka på förhållandet mellan för att få mer information om den inte är 100%. 

Om problemet beror på filstorlek, visas ett fel så här: ”blob < filnamn >” har storleken på < filstorlek > byte, vilket överskrider den maximala storleken för dokumentet extrahering för din aktuella tjänstnivån ”. Mer information om indexeraren begränsar finns [gränser](search-limits-quotas-capacity.md).

En andra orsak för innehåll som inte visas kan vara relaterade av i/o-Mappningsfel. Till exempel en utdatanamnet för målet är ”personer” men fältnamn index är gemena ”användare”. Systemet kunde returnera 201 lyckade meddelanden för hela pipeline så du tror att indexera lyckades, när ett fält är tomt. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tips 6: Utöka bearbetning överskrider maximala körningstiden (24-timmarsformat fönster)

Analys av avbildningen är beräkningsmässigt-intensiva även enkla fall så när avbildningar som är särskilt stor eller avancerade, bearbetningstider kan överskrida den maximala tillåtna tiden. 

Maximal körtid varierar beroende på nivå: flera minuter på kostnadsfria nivån, 24-timmarsformat indexering på fakturerbar nivåerna. Om det inte går att slutföra inom en 24-timmarsperiod för bearbetning på begäran bearbetning, växla till ett schema för att ha indexerare som hämtar bearbetning där den avbröts. 

Schemalagda indexerare indexering i återupptar enligt schema senaste kända dokumentet. Med hjälp av en återkommande schema fungerar indexeraren genom eftersläpningen avbildningen över ett antal timmar eller dagar tills alla icke bearbetade bilder behandlas. Mer information om syntax för att se [steg3: skapa en indexerare](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer).

För portal-baserade indexering (enligt beskrivningen i Snabbstart), om du väljer ”kör en gång” indexeraren alternativet gränser bearbetning till 1 timme (`"maxRunTime": "PT1H"`). Du kanske vill utöka fönstret bearbetning till något längre.

## <a name="tip-7-increase-indexing-throughput"></a>Tips 7: Öka indexeringsflöde

För [parallella indexering](search-howto-large-index.md), placera dina data i flera behållare eller flera virtuella mappar i samma behållare. Skapa sedan flera datasource och indexeraren värdepar. Alla indexerare kan använda samma kunskaper och skriva till målet samma sökindexet så att appen Sök inte behöver vara medveten om den här partitionering.
Mer information finns i [indexering stora datauppsättningar](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Se också
+ [Snabbstart: Skapa en pipeline kognitiva Sök i portalen](cognitive-search-quickstart-blob.md)
+ [: Självstudier kognitiva Sök REST API: er](cognitive-search-tutorial-blob.md)
+ [Ange autentiseringsuppgifterna för datakällan](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexering stora datauppsättningar](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)
+ [Utökade mappning till ett index](cognitive-search-output-field-mapping.md)