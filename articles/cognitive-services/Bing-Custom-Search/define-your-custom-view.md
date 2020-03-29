---
title: Konfigurera din anpassade bing-sökning | Microsoft-dokument
titleSuffix: Azure Cognitive Services
description: Med portalen kan du skapa en sökinstans som anger segmenten på webben. domäner, undersidor och webbsidor.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: c14376cc80373371ec5fcb8f22a00584a6b2f714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220219"
---
# <a name="configure-your-bing-custom-search-experience"></a>Konfigurera din anpassade bing-sökningsupplevelse

Med en anpassad sökinstans kan du anpassa sökupplevelsen så att den endast innehåller innehåll från webbplatser som användarna bryr sig om. I stället för att göra en webbomfattande sökning söker Bing bara igenom de segment på webbplatsen som intresserar dig. Du skapar en anpassad vy av webben med hjälp av [portalen](https://customsearch.ai) för anpassad sökning i Bing.

Med portalen kan du skapa en sökinstans som anger segmenten på webbplatsen: domäner, undersidor och webbsidor, som du vill att Bing ska söka efter och de som du inte vill att den ska söka efter. Portalen kan också föreslå innehåll som du kanske vill inkludera.

Använd följande när du definierar segmenten på webben:

| Segmentnamn | Beskrivning                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | En domänsegment innehåller allt innehåll som finns i en Internet-domän. Till exempel `www.microsoft.com`. Om du `www.` utelämnar kan Bing även söka i domänens underdomäner. Om du till `microsoft.com`exempel anger returnerar `support.microsoft.com` Bing också resultat från eller `technet.microsoft.com`. |
| Delsida    | Ett undersidessegment innehåller allt innehåll som finns i undersidan och sökvägar under den. Du kan ange högst två undersidor i sökvägen. Till exempel, `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Webbsida    | En webbsida segment kan bara inkludera den webbsidan i en anpassad sökning. Du kan också ange om undersidor ska inkluderas.                                                                                                                                                                                  |

> [!IMPORTANT]
> Alla domäner, undersidor och webbsidor som du anger måste vara offentliga och indexeras av Bing. Om du äger en offentlig webbplats som du vill inkludera i sökningen och Bing inte har indexerat den läser du dokumentationen till [Bing-webbansvariga](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) för mer information om hur du får Bing att indexera den. Mer information om hur du uppdaterar crawled-webbplatsen finns i dokumentationen till webbansvariga.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Lägga till segment på webben i din anpassade sökinstans

När du skapar din anpassade sökinstans kan du ange segmenten på webbplatsen: domäner, undersidor och webbsidor som du vill ska ha inkluderat eller blockerat från sökresultaten. 

Om du känner till de segment som du vill inkludera i den anpassade sökinstansen lägger du till dem i instansens **aktiva** lista. 

Om du inte är säker på vilka segment som ska inkluderas kan du skicka sökfrågor till Bing i **förhandsgranskningsfönstret** och markera de segment som du vill använda. Gör så här: 

1. välj "Bing" i listrutan i förhandsgranskningsfönstret och ange en sökfråga

2. Klicka på Lägg till **webbplats** bredvid det resultat som du vill inkludera. Klicka sedan på OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Anpassa din sökupplevelse med aktiva listor och blockerade listor 

Du kan komma åt listan över aktiva och blockerade segment genom att klicka på flikarna **Aktiv** och **Blockerad** i din anpassade sökinstans. Segment som läggs till i den aktiva listan inkluderas i din anpassade sökning. Blockerade segment genomsöks inte och visas inte i sökresultaten.

Om du vill ange segmenten på den webbplats som du vill att Bing ska söka på klickar du på fliken **Aktiv** och lägger till en eller flera webbadresser. Om du vill redigera eller ta bort webbadresser använder du alternativen under kolumnen **Kontroller.** 

När du lägger till webbadresser i listan **Aktiv** kan du lägga till enstaka webbadresser eller flera webbadresser samtidigt genom att ladda upp en textfil med hjälp av uppladdningsikonen.

![Fliken Aktiv för anpassad sökning i Bing](media/file-upload-icon.png)

Om du vill ladda upp en fil skapar du en textfil och anger en enskild domän, undersida eller webbsida per rad. Filen avvisas om den inte är korrekt formaterad.

> [!NOTE]
> * Du kan bara ladda upp en fil till listan **Aktiv.** Du kan inte använda den för att lägga till segment i listan **Blockerad.**  
> * Om listan **Blockerad** innehåller en domän, undersida eller webbsida som du har angett i överföringsfilen tas den bort från listan **Blockerad** och läggs till i listan **Aktiv.**
> * Dubblettposter i uppladdningsfilen ignoreras av anpassad Bing-sökning. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Få förslag på webbplatsen för din sökupplevelse

När du har lagt till webbsegment i listan **Aktiv** genereras webbplats- och undersidasförslag längst ned på fliken. Det här är segment som Bing Custom Search tror att du kanske vill inkludera. Klicka på **Uppdatera** om du vill få uppdaterade förslag när du har uppdaterat inställningarna för den anpassade sökinstansen. Det här avsnittet visas bara om förslag är tillgängliga.

## <a name="search-for-images-and-videos"></a>Sök efter bilder och videoklipp

Du kan söka efter bilder och videor på samma sätt som webbinnehåll med hjälp av [API:et för anpassad bildsökning bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) eller [API:et för anpassad videosökning.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) Du kan visa dessa resultat med det [värdbaserade användargränssnittet](hosted-ui.md)eller API:erna. 

Dessa API:er liknar de icke-anpassade API:erna för [Bing-bildsökning](../Bing-Image-Search/overview.md) och [Bing-videosökning,](../Bing-Video-Search/search-the-web.md) men söker på hela webben och kräver inte `customConfig` frågeparametern. Mer information om hur du arbetar med bilder och videoklipp finns i de här dokumentationsuppsättningarna. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testa sökinstansen med förhandsgranskningsfönstret

Du kan testa sökinstansen genom att använda förhandsgranskningsfönstret på portalens högra sida för att skicka sökfrågor och visa resultaten. 

1. Under sökrutan väljer du **Min instans**. Du kan jämföra resultaten från din sökupplevelse med Bing genom att välja **Bing**. 
2. Välj ett säkert sökfilter och vilken marknad som ska sökas (se [Frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Ange en fråga och tryck på Retur eller klicka på sökikonen för att visa resultaten från den aktuella konfigurationen. Du kan ändra söktypen du utför genom att klicka på **Webb,** **Bild**eller **Video** för att få motsvarande resultat. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Justera rangordningen för specifika sökresultat

Med portalen kan du justera sökrankningen för innehåll från specifika domäner, undersidor och webbsidor. När du har skickat en sökfråga i förhandsgranskningsfönstret innehåller varje sökresultat en lista med justeringar som du kan göra för den:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Blockera      | Flyttar domänen, undersidan eller webbsidan till listan Blockerad. Bing utesluter innehåll från den valda webbplatsen från att visas i sökresultaten.                    |
| Öka      | Ökar innehållet från domänen eller undersidan så att det blir högre i sökresultaten.                                                                                        |
| Degradering     | Nedgraderar innehåll från domänen eller undersidan lägre i sökresultaten. Du väljer om du vill nedgradera innehåll från den domän eller undersida som webbsidan tillhör. |
| Fäst överst | Flyttar domänen, undersidan eller webbsidan till listan **Fäst.** Detta tvingar webbsidan att visas som det bästa sökresultatet för en viss sökfråga.                   |

Det går inte att justera rangordningen för bild- eller videosökningar.

### <a name="boosting-and-demoting-search-results"></a>Öka och nedgradera sökresultat

Du kan öka, marknadsföra eller nedgradera alla domäner eller undersidor i listan **Aktiv.** Som standard läggs alla segment till utan rangordningsjusteringar. Segment av webben som är Super-marknadsförda eller Marknadsförda rankas högre i sökresultaten (med super boost ranking högre än boost). Objekt som nedgraderas rankas lägre i sökresultaten.

Du kan superförstärka, marknadsföra eller nedgradera objekt med hjälp av **kontrollerna Rangordningsjustering** i listan **Aktiv,** eller genom att använda kontrollerna Marknadsför och sänk ned i förhandsgranskningsfönstret. Tjänsten lägger till segmentet i listan Aktiv och justerar rangordningen därefter.

> [!NOTE] 
> Att öka och degradera domäner och undersidor är en av många metoder som Bing Custom Search använder för att bestämma ordningen på sökresultaten. På grund av andra faktorer som påverkar rangordningen av olika webbinnehåll kan effekterna av att justera rang variera. Använd förhandsgranskningsfönstret för att testa effekterna av att justera rankningen för sökresultaten. 

Super boost, boost och degradering är inte tillgängliga för bild- och videosökningar.

## <a name="pin-slices-to-the-top-of-search-results"></a>Fästa segment högst upp i sökresultaten

Med portalen kan du också fästa webbadresser högst upp i sökresultaten för specifika söktermer med hjälp av fliken **Fäst.** Ange en URL och fråga för att ange webbsidan som ska visas som det bästa resultatet. Observera att du kan fästa högst en webbsida per sökfråga, och endast indexerade webbsidor visas i sökningar. Fästa resultat är inte tillgängligt för bild- eller videosökningar.

Du kan fästa en webbsida högst upp på två sätt:

* På fliken **Fäst** anger du webbadressen till webbsidan för att fästa högst upp och motsvarande fråga.

* I **fönstret Förhandsgranska** anger du en sökfråga och klickar på Sök. Leta reda på webbsidan som du vill fästa för frågan och klicka på **Fäst högst upp**. webbsidan och frågan läggs till i listan **Fäst.**

### <a name="specify-the-pins-match-condition"></a>Ange fästens matchningsvillkor

Som standard fästs webbsidor endast högst upp i sökresultaten när en användares frågesträng exakt matchar en som anges i listan **Fäst.** Du kan ändra det här beteendet genom att ange något av följande matchningsvillkor:

> [!NOTE]
> Alla jämförelser mellan användarens sökfråga och stiftets sökfråga är skiftlägesokänsliga.

| Värde | Beskrivning                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Börjar med | Stiftet är en matchning om användarens frågesträng börjar med stiftets frågesträng |
| Slutar med   | Stiftet är en matchning om användarens frågesträng slutar med stiftets frågesträng.  |
| Innehåller    | Stiftet är en matchning om användarens frågesträng innehåller stiftets frågesträng.   |


Om du vill ändra fästets matchningsvillkor klickar du på fästets redigeringsikon. Klicka på listrutan i kolumnen **Frågematchningsvillkor** och välj det nya villkor som ska användas. Klicka sedan på spara ikonen för att spara ändringen.

### <a name="change-the-order-of-your-pinned-sites"></a>Ändra ordningen på dina fästa webbplatser

Om du vill ändra ordningen på dina pins kan du dra och släppa dem eller redigera deras ordernummer genom att klicka på ikonen "redigera" i **kontrollkolumnen** i listan **Fäst.**

Om flera stift uppfyller ett matchningsvillkor använder Bing Custom Search det högsta i listan.

## <a name="view-statistics"></a>Visa statistik

Om du prenumererar på Anpassad sökning på lämplig nivå (se [prissidorna)](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)läggs fliken **Statistik** till i dina produktionsinstanser. På fliken Statistik visas information om hur dina anpassade sökslutpunkter används, inklusive samtalsvolym, de vanligaste frågorna, geografisk distribution, svarskoder och säker sökning. Du kan filtrera information med hjälp av de medföljande kontrollerna.

## <a name="usage-guidelines"></a>Riktlinjer för användning

- För varje anpassad sökinstans är det maximala antalet rangjusteringar som du kan göra i **aktiva** och **blockerade** segment begränsat till 400.
- Om du lägger till ett segment på flikarna Aktiv eller Blockerad räknas det som en rangordningsjustering.
- Öka och degradera räknas som två rankningsjusteringar.
- För varje anpassad sökinstans är det maximala antalet pins som du kan göra begränsat till 200.

## <a name="next-steps"></a>Nästa steg

- [Anropa din anpassade sökning](./search-your-custom-view.md)
- [Konfigurera värdbaserad UI-upplevelse](./hosted-ui.md)
- [Använda dekorationsmarkörer för att markera text](../bing-web-search/hit-highlighting.md)
- [Webbsidor för sida](./page-webpages.md)
