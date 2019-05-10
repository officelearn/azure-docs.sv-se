---
title: Konfigurera din upplevelse för anpassad sökning i Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Beskriver hur du skapar plats och lodräta söktjänster
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 83ae5f923130ecb46b7b94cd8112ee45ae13e3f4
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236252"
---
# <a name="configure-your-bing-custom-search-experience"></a>Konfigurera din Bing Custom Search

En anpassad sökning i instans kan du skräddarsy sökupplevelsen om du vill inkludera innehåll från webbplatser som användarna som intresserar dig. Istället för att utföra en webbkomponenterna sökning, söker Bing bara de delar av webben som intresserar dig. Du skapar en anpassad vy av webben med hjälp av [portalen](https://customsearch.ai) för anpassad sökning i Bing.

Portalen kan du skapa en sökning-instans som anger snitt av webben: domäner, underordnade sidor, och webbsidor som du vill att Bing för att söka i, och de som du inte vill att söka. Portalen kan också föreslå innehåll som du kanske vill inkludera.

Använd följande när du definierar din snitt av webben:

| Sektorn namn | Beskrivning                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domän     | En sektor i domänen innehåller allt innehåll i en internet-domän. Till exempel `www.microsoft.com`. Om du utesluter `www.` orsakar Bing för att även söka domänens underdomäner. Exempel: Om du anger `microsoft.com`, Bing också returnerar resultat från `support.microsoft.com` eller `technet.microsoft.com`. |
| Underordnad sida    | En underordnad sida sektor innehåller allt innehåll i den underordnad sida och sökvägar under den. Du kan ange högst två underordnade sidor i sökvägen. Till exempel, `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Webbsida    | En webbsida sektor kan inkludera endast den webbsidan i en anpassad sökning. Du kan du ange om du vill inkludera underordnade sidor.                                                                                                                                                                                  |

> [!IMPORTANT]
> Alla domäner, underordnade sidor och webbsidor som du anger måste vara offentliga och indexeras av Bing. Om du äger en offentlig plats som du vill inkludera i sökningen och har ännu inte indexerat av Bing, se Bing [webbadministratör dokumentation](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) mer information om hur du får Bing för att indexera den. Se även webbadministratör-dokumentationen för mer information om hur du får Bing för att uppdatera webbplatsen crawlade om indexet är inaktuell.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Lägga till snitt av webben till din anpassade Sökinstans

När du skapar din anpassade Sökinstans, kan du ange snitt av webben: domäner, underordnade sidor och webbsidor som du vill ha ingår eller blockeras från sökresultaten. 

Om du vet sektorer som du vill ska ingå i din anpassade Sökinstans kan läggas till i din instans **Active** lista. 

Om du inte vet vilka segment som ska ingå, kan du skicka sökfrågor till Bing i den **förhandsversion** rutan och välj sektorer som du vill. Gör så här: 

1. Välj ”Bing” i listrutan i förhandsgranskningsfönstret och ange en sökfråga

2. Klicka på **Lägg till plats** bredvid resultatet som du vill inkludera. Klicka sedan på OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Anpassa användningen med aktiva och en lista över blockerade 

Du kan komma åt listan över aktiva och blockerade segment genom att klicka på den **Active** och **blockerad** flikar i din anpassade Sökinstans. Sektorer som lagts till i listan över aktiva ska ingå i din anpassade sökning. Blockerade segment inte söks igenom och inte längre visas i sökresultaten.

Klicka på Ange snitt av webben du vill Bing för att söka i **Active** fliken och Lägg till en eller flera webbadresser. Du kan redigera eller ta bort URL genom att använda alternativen under den **kontroller** kolumn. 

Om att lägga till URL: er till de **Active** listan som du kan lägga till webbadresser som enda eller flera webbadresser på en gång genom att ladda upp en textfil med hjälp av ikonen ladda upp.

![Bing Custom Search aktiv flik](media/file-upload-icon.png)

Om du vill överföra en fil, skapa en textfil och ange en enda domän, underordnad sida eller webbsidan per rad. Filen återställs när det är felaktigt formaterad.

> [!NOTE]
> * Du kan bara ladda upp en fil till den **Active** lista. Du kan inte använda den för att lägga till sektorer till den **blockerad** lista.  
> * Om den **blockerad** listan innehåller en domän, en underordnad sida eller en webbsida som du angav i uppladdningsfilen ladda det tas bort från den **blockerad** lista och läggs till i **Active** lista .
> * Dubbla poster i ladda uppladdningsfilen kommer att ignoreras av Bing Custom Search. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Få webbplats förslag för din sökupplevelse

När du lägger till webb-sektorer till den **Active** lista, Bing Custom Search portal genererar webbplats och underordnad sida förslag längst ned på fliken. Det här är sektorer som anpassad sökning i Bing tror kanske du vill inkludera. Klicka på **uppdatera** att hämta uppdaterade förslag när du har uppdaterat din anpassade Sökinstans inställningar. Det här avsnittet visas endast om förslag är tillgängliga.

## <a name="search-for-images-and-videos"></a>Sök efter bilder och videor

Du kan söka efter bilder och videor på samma sätt som webbinnehåll med hjälp av den [anpassade bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference) eller [anpassad sökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference). Du kan visa de resultaten med den [finns Användargränssnittet](hosted-ui.md), eller API: erna. 

Dessa API: er som liknar anpassat [bildsökning i Bing](../Bing-Image-Search/overview.md) och [Videosökning](../Bing-Video-Search/search-the-web.md) API: er, men söka igenom hela webbplatsen och kräver inte den `customConfig` frågeparameter. Se dessa dokumentuppsättningar för mer information om arbete med bilder och videor. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testa din Sökinstans i förhandsgranskningsfönstret

Du kan testa din Sökinstans genom att använda förhandsgranskningsfönstret portalens till höger för att skicka sökfrågor och granska resultaten. 

1. Under sökrutan, Välj **Mina instans**. Du kan jämföra resultaten från din sökupplevelse till Bing, genom att välja **Bing**. 
2. Välj ett filter för säker sökning och som marknaden för att söka (se [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)).
3. Ange en fråga och tryck på RETUR eller klicka på sökikonen för att visa resultat från den aktuella konfigurationen. Du kan ändra din söktyp du utföra genom att klicka på **Web**, **bild**, eller **Video** att få motsvarande resultat. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Justera rangordningen för specifika sökresultat

På portalen kan du justera sökrankningen av innehåll från vissa domäner, underordnade sidor och webbsidor. När du har skickat en sökfråga i förhandsgranskningsfönstret innehåller varje sökresultat en lista över justeringar som du kan göra för den:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Blockera      | Flyttar den domän, en underordnad sida eller en webbsida i listan med blockerad. Bing undantar innehåll från den valda platsen inte visas i sökresultatet.                    |
| Förstärkning      | Ökar innehåll i domänen eller en underordnad sida högre i sökresultatet.                                                                                        |
| Flytta ned     | Flyttar ned innehåll från domänen eller underordnad sida lägre i sökresultaten. Du väljer om du vill flytta ned innehåll från domänen eller subpage webbsidan tillhör. |
| Fästa till början | Flyttar den domän, en underordnad sida eller en webbsida att den **fästa** lista. Detta gör att webbsidan visas som det översta sökresultatet för en given sökfråga.                   |

Justera rangordning är inte tillgänglig för bild eller video sökningar.

### <a name="boosting-and-demoting-search-results"></a>Hur du ökar och degradering av sökresultat

Du kan mycket öka, öka, eller degradera alla domäner eller subpage i den **Active** lista. Som standard läggs alla sektorer utan några justeringar för rangordning. Snitt av webben mycket förstärkta eller Boosted rankas högre i sökresultaten (med super boost rangordning är högre än boost). Objekt som nedgraderas rangordnas lägre i sökresultaten.

Du mycket kan öka, öka eller nedgradera objekt med hjälp av den **rangordning justera** styr i den **Active** -lista, eller genom med förstärkningen och flytta ned kontroller i förhandsgranskningsfönstret. Tjänsten lägger till sektorn i listan Active och justerar rangordning därefter.

> [!NOTE] 
> Hur du ökar och degradering av domäner och underordnade sidor är ett av många metoder för anpassad sökning i Bing använder för att fastställa ordningen för sökresultat. På grund av andra faktorer som påverkar olika webbinnehåll rangordning kan effekterna av att justera rangordning variera. Använda förhandsgranskningsfönstret för att testa effekterna av att justera rankningen av sökresultaten. 

Super öka öka och sänka är inte tillgängliga för bild- och video sökningar.

## <a name="pin-slices-to-the-top-of-search-results"></a>PIN-kod sektorer längst upp i sökresultaten

Portalen kan du fästa URL: er längst upp i sökresultaten för specifika sökvillkor med hjälp av den **fästa** fliken. Ange en URL och fråga om du vill ange på webbsidan som visas som ett översta resultat. Observera att du kan fästa högst en enda webbsida per sökfråga och endast indexerade webbsidor visas i sökningar. Fästa resultatet är inte tillgänglig för bild eller video sökningar.

Du kan fästa en webbsida högst upp på två sätt:

* I den **fästa** ange Webbadressen till webbsidan för att fästa till högst upp och dess motsvarande fråga.

* I den **förhandsversion** fönstret, ange en sökfråga och klicka på Sök. Hitta den webbsida som du vill fästa på din fråga och klicka på **PIN-kod till början**. webbsidan och frågan kommer att läggas till i **fästa** lista.

### <a name="specify-the-pins-match-condition"></a>Ange pin-kodens matchningsvillkor

Som standard webbsidor är endast fästa högst upp i sökresultaten när en användarfrågesträng matchar exakt namnet visas i den **fästa** lista. Du kan ändra det här beteendet genom att ange något av följande matchningsvillkor:

> [!NOTE]
> Alla jämförelser mellan användarens sökfråga och pin-kodens sökfråga är skiftlägeskänsliga.

| Värde | Beskrivning                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Börjar med | PIN-koden finns en matchning om användarens frågesträngen som börjar med pin-kodens frågesträng |
| Slutar med   | PIN-koden är en matchning om användarens frågesträngen som slutar med pin-kodens frågesträngen.  |
| Innehåller    | PIN-koden är en matchning om användarens frågesträngen innehåller frågesträngen för den PIN-kod.   |


Klicka på fästikonen Redigera om du vill ändra pin-kodens matchningsvillkor. I den **fråga matchningsvillkor** kolumn, klicka på listrutan och välj det nya villkoret du vill använda. Klicka på Spara ikon för att spara ändringen.

### <a name="change-the-order-of-your-pinned-sites"></a>Ändra ordningen på dina fästa webbplatser

Du kan dra och släpp den dem om du vill ändra ordningen på din PIN-koder, eller redigera deras ordningsnummer genom att klicka på ikonen ”Redigera” i den **kontroller** kolumnen i den **fästa** lista.

Om flera PIN-koder uppfyller ett matchningsvillkor, använder anpassad sökning i Bing det högst upp i listan.

## <a name="view-statistics"></a>Visa statistik

Om du prenumererar på Custom Search på lämplig nivå (se den [prissidor](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), ett **statistik** fliken läggs till i din produktionsinstanser. Fliken statistik visar information om hur dina Custom Search-slutpunkter som används, inklusive anropsvolym, toppfrågor, geografisk fördelning, svarskoder och säker sökning. Du kan filtrera beskriver hur du använder de angivna kontrollerna.

## <a name="usage-guidelines"></a>Riktlinjer för användning

- För varje anpassad Sökinstans det maximala antalet rangordning justeringar som du kan göra på **Active** och **blockerad** segment är begränsad till 400.
- Att lägga till en sektor i flikarna aktiv eller blockerad räknas som en rangordning justering.
- Hur du ökar och degradering av antal som två rangordning justeringar.
- Det maximala antalet PIN-koder som du kan göra är begränsad till 200 för varje anpassad Sökinstans.

## <a name="next-steps"></a>Nästa steg

- [Anropa din anpassade sökning](./search-your-custom-view.md)
- [Konfigurera värdbaserad UI-upplevelse](./hosted-ui.md)
- [Använda dekorationsmarkörer för att markera text](./hit-highlighting.md)
- [Webbsidor för sida](./page-webpages.md)
