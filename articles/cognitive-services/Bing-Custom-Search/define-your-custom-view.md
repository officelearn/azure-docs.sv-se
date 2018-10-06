---
title: Definiera en anpassad vy – Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Beskriver hur du skapar plats och lodräta söktjänster
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a74e6a6a90a242d4d1b2fd71a5fc6cf949ea55cb
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815264"
---
# <a name="configure-your-custom-search-experience"></a>Konfigurera din upplevelse för anpassad sökning

En anpassad sökning i instans kan du skräddarsy sökupplevelsen om du vill inkludera innehåll från webbplatser som användarna som intresserar dig. Istället för att utföra en webbkomponenterna sökning, söker Bing endast sektorn webb som intresserar dig. Använd Bing Custom Search för att skapa den anpassade vyn webb-, [portal](https://customsearch.ai). Information om att logga in på portalen finns i [skapar din första Bing Custom Search-instans](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). 

Portalen kan du skapa en sökning-instans som anger domänerna, underordnade sidor, och webbsidor som du vill att Bing för att söka och de som du inte vill att söka. Förutom att ange URL: er för det innehåll som du vet om ställa du även på portalen för att föreslå innehåll som du kanske vill lägga till i vyn. 

Här följer några sätt att du kan definiera ett segment av webb: 

1.  Domän. En sektor i domänen innehåller allt innehåll i en internet-domän. Till exempel www.microsoft.com. Om du utesluter ”www” gör Bing för att även söka domänens underdomäner. Exempel: Om du anger microsoft.com Bing också returnerar resultat från support.microsoft.com eller technet.microsoft.com.  
  
2.  Underordnad sida. En underordnad sida sektor innehåller allt innehåll i den underordnad sida och sökvägar under den. Du kan ange högst två underordnade sidor i sökvägen. Till exempel www.microsoft.com/en-us/windows/  
  
3.  Webbsidan. En webbsida sektor kan inkludera endast den webbsidan i en anpassad sökning. Du kan du ange om du vill inkludera underordnade sidor.

Alla domäner, underordnade sidor och webbsidor som du anger måste vara offentliga och indexeras av Bing. Om du äger en offentlig plats som du vill inkludera i sökningen och har ännu inte indexerat av Bing, se Bing [webbadministratör dokumentation](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) mer information om hur du får Bing för att indexera den. Se även webbadministratör-dokumentationen för mer information om hur du får Bing för att uppdatera webbplatsen crawlade om indexet är inaktuell.

## <a name="adding-slices-to-your-custom-search"></a>Att lägga till segment i din anpassade sökning

När du definierar din anpassade Sökinstans kan ange du den aktiva och blockerade domäner och underordnade sidor webbsidor som du vill söka efter inte.  

- Aktiva: En lista över domäner, underordnade sidor eller webbsidorna för att inkludera i sökningen.  
  
- Blockerad: En lista över domän, underordnade sidor eller webbsidor ska undantas från sökningen. De objekt som du blockerar ska vara innehåll domäner och underordnade sidor visas i listan Active.

Klicka på flikarna aktiv och blockerad i din anpassade Sökinstans för att komma åt varje lista. 

<a name="active-and-blocked-lists"></a>
## <a name="active-and-blocked-lists"></a>Aktiv och blockerad listor 

Om du vill ange en sektor av webben du vill Bing för att söka, klickar du på den **Active** fliken och listar de domäner och underordnade sidor webbsidorna för att söka. Du kan lägga till en sektor direkt i listan eller lägga till fler än en sektor genom att ladda upp en textfil med hjälp av ikonen ladda upp.

Ladda upp filinformation: 

- Filuppladdning är endast tillgänglig för att lägga till segment i listan över aktiva, du kan inte använda den för att lägga till segment i listan blockerad.  
  
- Skapa en textfil och ange en enda domän, underordnad sida eller webbsidan per rad. Hela överföringen avvisas om ett fel inträffar.  
  
- Om listan över blockerade innehåller den domän, en underordnad sida eller en webbsida som du angav i uppladdningsfilen, tas den bort från listan över blockerade tjänsten och lägger till den i listan Active.  
  
- Tjänsten ignorerar dubbletter i uppladdningsfilen.

Du kan redigera eller ta bort segment genom att använda alternativen under den **kontroller** kolumn. 

På samma sätt kan du lägga till segment i listan blockerad (utom om du inte använda en Överför fil för att ange segment).

## <a name="pinned-list"></a>Fästa lista

Portalen kan du fästa en specifik webbsida längst upp i sökresultatet om användaren anger ett specifikt sökord. Den **fästa** fliken innehåller en lista över frågan period och webbsidan par som anger på webbsidan som visas i det översta resultat för en specifik fråga. Information om att fästa resultat finns i [justera rangordning](#adjustrank).

Fästa resultatet är inte tillgänglig för den bildsökning och Videosökningsresultat inträffar.

## <a name="website-suggestions"></a>Webbplatsen förslag

När du lägger till segment i listan med Active genererar tjänsten webbplats och underordnad sida förslag som du kanske vill lägga till i din sökning. Den **kanske du vill lägga till** avsnittet innehåller förslag på. Instanssidan innehåller det här avsnittet om förslag är tillgängliga. 

Lägg till förslag Active listan, klicka på den + -ikonen.  Eftersom tjänsten genererar förslag baserat på dina inställningar, måste du klicka **uppdatera** när du lägger till förslagen. 

## <a name="preview-pane"></a>Förhandsgranskningsfönstret

Du kan testa din search-instans som använder förhandsgranskningsfönstret till höger för att skicka sökfrågor och visa resultat. 

1. Välj **min instans**
2. Välj ett filter för säker sökning och vad marknaden för att söka (se [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)).
3. Ange en fråga och tryck på RETUR eller klicka på sökikonen för att visa resultat från den aktuella konfigurationen. 

Klicka för att välja typ av sökning för att utföra **Web** att hämta Webbresultat, **bild** att få avbildningen resultat eller **Video** att få video resultat. 

Med förhandsgranskning kan du också granska Bing resultat genom att välja **Bing** i stället för **Mina instans**. Detta kan vara användbart att jämföra resultaten från din sökupplevelse till resultaten som returnerades av Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Justera rangordning

Portalen kan du justera rangordning för att manipulera resultat som Bing returnerar. Ange en sökterm och kör frågan i förhandsgranskningsfönstret. Förhandsgranskningsfönstret lista sökresultatet för frågan. Till höger om varje resultat är listan över justeringar, du kan göra. 

- Blockera. Flyttar den domän, en underordnad sida eller en webbsida i listan med blockerad. Du kan välja nivå om du vill blockera. Bing utesluter innehåll från den valda webbplatsen i sökresultatet.  
  
- Öka. Ökar innehåll i domänen eller en underordnad sida högre upp i sökresultaten. Du väljer om du vill öka innehåll från domänen eller subpage webbsidan tillhör. [Läs mer](#boosting-and-demoting)  
  
- Flytta ned. Flyttar ned innehåll från domänen eller underordnad sida lägre i sökresultaten. Du väljer om du vill flytta ned innehåll från domänen eller subpage webbsidan tillhör. [Läs mer](#boosting-and-demoting).  
  
- PIN-kod-överkant. Definiera på webbsidan som visas överst i resultatet om användarens frågesträngen matchar de PIN-koderna frågesträng baserat på matchningsvillkor för den PIN-kod. Den aktiva listan behöver inte innehålla på webbsidan som du kan fästa den. [Läs mer](#pin-to-top).

Justera rangordning är inte tillgänglig för den bildsökning och Videosökningsresultat inträffar.

## <a name="boosting-and-demoting"></a>Hur du ökar och degradering

Du kan mycket öka, öka, eller degradera alla domäner eller subpage i din aktiva listan. Som standard läggs alla sektorer med samma vikt. Objekt som är mycket förstärkta eller ökat rankas högre i sökresultaten (med super boost rangordning är högre än boost). Objekt som nedgraderas rangordnas lägre i sökresultaten.

Det är viktigt att notera som super öka öka och sänka ge vikt varianter till domäner eller underordnade sidor. Det här är bara en av många signaler som används av rankningen för att fastställa ordningen på resultaten. Det innebär att deras effekt för en specifik fråga inte är säkert som många andra faktorer kan påverka Webbresultat övergripande rangordning.  För att fastställa möjliga effekten har som boosting eller degradering rankningen, testet sökupplevelsen med förhandsgranskningsfönstret.

Du kan mycket öka, förbättra, eller degradera objekt med hjälp av rangordning justera kontrollerna i den aktiva listan eller genom att använda förstärkningen och flytta ned kontroller i förhandsgranskningsfönstret. Tjänsten lägger till sektorn i listan Active och justerar rangordning därefter.

Super öka öka och sänka är inte tillgängliga för bildsökning och Videosökningsresultat upplevelser.

## <a name="pin-to-top"></a>Fästa till början

Välj något av följande alternativ för att fästa en webbsida längst upp i sökresultaten för en specifik fråga:

1.  I den **fästa** ange Webbadressen till webbsidan för att fästa högst upp i resultatet och den fråga som utlöser den fästa.  
  
2.  I den **förhandsversion** fönstret, ange en frågeterm och klicka på Sök. Därefter identifiera webbsida i de resultat som du vill fästa högst upp i resultatet om användaren anger samma fråga. Klicka sedan på **PIN-kod till början**. Tjänsten lägger till webbsidan och frågan till den **fästa** lista. 

Du kan spåra din PIN-koder i den **fästa** fliken. Att PIN-koder visas som\<fråga\>, \<webbsidan\>-par. 

Du kan fästa högst en enda webbsida att överst bland resultaten för en specifik fråga.

PIN-koder är inte tillgängliga för den bildsökning och Videosökningsresultat inträffar.

### <a name="specifying-the-pins-match-condition"></a>Att ange pin-kodens matchningsvillkor

Webbsidan fästs högst upp i resultatet bara om användarens frågesträngen matchar den PIN-kod frågesträngen baserat på matchningsvillkor för PIN-kod. Som standard matchas med en PIN-kod bara om pin-kodens frågesträngen och frågesträngen för användarens matchar exakt. Du kan ändra standardinställningen genom att ange något av följande matchningsvillkor.

- Börjar med &mdash; PIN-koden finns en matchning om användarens frågesträngen som börjar med den PIN-kod-frågesträng.
- Slutar med &mdash; PIN-koden finns en matchning om användarens frågesträngen som slutar med pin-kodens frågesträngen.
- Innehåller &mdash; PIN-koden finns en matchning om användarens frågesträngen innehåller frågesträngen för den PIN-kod.

Klicka på redigeringsikonen för den PIN-kod (som ser ut som en penna) om du vill ändra pin-kodens matchningsvillkor. I den **fråga matchningsvillkor** kolumn, klicka på listrutan och välj det nya villkoret du vill använda. Klicka på Spara ikon för att spara ändringen.

Alla jämförelser är skiftlägeskänsliga.

### <a name="changing-the-order-of-the-pins"></a>Ändra ordningen på de PIN-koderna

Om du vill ändra ordningen på din PIN-koder, kan du dra och släpp PIN-koden eller du kan redigera PIN-koden och ändra dess ordningsnummer. Släpp musknappen att dra och släpp en PIN-kod, PIN-koden i listan för att flytta, hålla musknappen nedtryckt och dra PIN-koden över PIN-koden som du vill ersätta. Observera att pinkod ordning ändras.

Du kan också redigera pinkod ordning. I den **kontroller** kolumnen, klickar du på redigeringsikonen för den PIN-kod (som ser ut som en penna). Ange ordningstalet där du vill att PIN-kod visas i listan och tryck på RETUR eller klicka på Spara ikon. Till exempel om PIN-koden är för närvarande sjätte i listan och du vill att andra, ändra ordningstalet till två (2).

Om flera PIN-koder uppfyller villkoret matchning, anger ordningen för de PIN-koderna vilka Bing använder PIN-kod. Om PIN-koder två och tre uppfyller villkoret matchning, till exempel använder PIN-kod två Bing.

## <a name="use-bing-to-specify-slices"></a>Använd Bing för att ange vilka delar

Det finns ett par olika sätt att ange snitt av webben som utgör din anpassade sökning. Om du vet sektorer som du vill ska ingå i din instans, lägger du till dem till din instans **Active** lista. Information om hur du lägger till objekt i den **Active** listan själv, se [aktiv och blockerad listor](#active-and-blocked-lists).

Men om du inte vet vilka segment som ska ingå, kan du köra Bing frågor den **förhandsversion** fönstret och se vad Bing returnerar. Du kan sedan välja sektorer som du vill ska ingå i din anpassade sökning. Sannolikt måste du köra flera sökord att kontrollera att du identifiera alla sektorer som du vill använda för din instans. 

Följ dessa steg om du vill använda Bing att lägga till segment i din anpassade Sökinstans. 

1.  Logga in på Bing Custom Search [portal](https://customsearch.ai).
2.  Skapa en instans eller välj en instans att uppdatera.
3.  Välj Bing i förhandsgranskningsfönstret till höger, i listrutan.
4.  I sökrutan anger du en frågeterm som är relevant för din instans.
5.  Klicka på **Lägg till plats** bredvid resultatet som du vill inkludera.
6.  Klicka på knappen **OK**.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Visa statistik

Om du prenumererar på Custom Search på lämplig nivå (se den [prissidor](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), ett **statistik** fliken läggs till i din produktionsinstanser. Fliken statistik visar information om hur dina Custom Search-slutpunkter som används, inklusive anropsvolym, toppfrågor, geografisk fördelning, svarskoder och säker sökning. Du kan filtrera beskriver hur du använder de angivna kontrollerna.

## <a name="understanding-quota"></a>Förstå kvot

- För varje anpassad Sökinstans det maximala antalet rangordning justeringar som du kan göra på **Active** och **blockerad** segment är begränsad till 400.
- Att lägga till en sektor i flikarna aktiv eller blockerad räknas som en rangordning justering.
- Hur du ökar och degradering av antal som två rangordning justeringar.
- Det maximala antalet PIN-koder som du kan göra är begränsad till 200 för varje anpassad Sökinstans.

## <a name="next-steps"></a>Nästa steg

- [Anropa dina anpassad sökning](./search-your-custom-view.md)
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)
- [Använda decoration markörer för att markera text](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)