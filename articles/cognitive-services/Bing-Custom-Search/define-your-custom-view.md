---
title: 'Anpassad sökning i Bing: Definiera en anpassad vy | Microsoft Docs'
description: Beskriver hur du skapar en webbplats och lodräta söktjänster
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 8ef8aabc7363db88317a6428301512b0a0d4c055
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158780"
---
# <a name="configure-your-custom-search-experience"></a>Konfigurera din upplevelse för anpassad sökning
En anpassad sökning i instans kan du skräddarsy sökupplevelsen om du vill inkludera innehåll från webbplatser som användarna som intresserar dig. Istället för att utföra en webbkomponenterna sökning, söker Bing endast sektorn webb som intresserar dig.
Använd Bing Custom Search för att skapa den anpassade vyn webb-, [portal](https://customsearch.ai). Information om att logga in på portalen finns i [skapar din första Bing Custom Search-instans](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). Portalen kan du skapa en sökning-instans som anger domänerna, underordnade sidor, och webbsidor som du vill att Bing för att söka och de som du inte vill att söka. Förutom att ange URL: er för det innehåll som du vet om ställa du även på portalen för att föreslå innehåll som du kanske vill lägga till i vyn. Här följer några sätt att du kan definiera ett segment av webb: 

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

Använd alternativen under kolumnen kontroller för att redigera eller ta bort segment. 

På samma sätt kan du lägga till segment i listan blockerad (utom om du inte använda en Överför fil för att ange segment).

## <a name="pinned-list"></a>Fästa lista
Portalen kan du fästa en specifik webbsida längst upp i sökresultatet om användaren anger ett specifikt sökord. Den **fästa** fliken innehåller en lista över frågan period och webbsidan par som anger på webbsidan som visas i det översta resultat för en specifik fråga. Användarens frågetermen måste exakt matcha fästa frågetermen.
Information om att fästa resultat finns i [justera rangordning](#adjustrank).

Fästa resultatet är inte tillgänglig för bildsökning-upplevelse.

## <a name="site-suggestions"></a>Webbplatsförslag
När du lägger till segment i listan med Active genererar tjänsten plats och underordnad sida förslag som du kanske vill lägga till i din sökning. Den **kanske du vill lägga till** avsnittet innehåller förslag på. Instanssidan innehåller det här avsnittet om förslag är tillgängliga. 

Lägg till förslag Active listan, klicka på den + -ikonen.  Eftersom tjänsten genererar förslag baserat på dina inställningar, måste du klicka **uppdatera** när du lägger till förslagen. 

## <a name="preview-pane"></a>Förhandsgranskningsfönstret
Du kan testa din search-instans som använder förhandsgranskningsfönstret till höger för att skicka sökfrågor och visa resultat. Välj **Mina instans**, Välj ett filter för säker sökning och vad marknaden för att söka (se [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters). Ange en fråga och tryck på RETUR eller klicka på sökikonen för att visa resultat från den aktuella konfigurationen. Se Webbresultat klickar du på **Web**, för att se bildresultat klickar du på **bild**. 

 Med förhandsgranskning kan du också granska Bing resultat genom att välja **Bing** i stället för **Mina instans**. Detta kan vara användbart att jämföra resultaten från din sökupplevelse till resultaten som returnerades av Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Justera rangordning
Portalen kan du justera rangordning för att manipulera resultat som Bing returnerar. Ange en sökterm och kör frågan i förhandsgranskningsfönstret. Förhandsgranskningsfönstret lista sökresultatet för frågan. Till höger om varje resultat är listan över justeringar, du kan göra. 

- Blockera. Flyttar den domän, en underordnad sida eller en webbsida i listan med blockerad. Du kan välja nivå om du vill blockera. Bing utesluter innehåll från den valda webbplatsen i sökresultatet. 
- Öka. Ökar innehåll i domänen eller en underordnad sida högre upp i sökresultaten. Du väljer om du vill öka innehåll från domänen eller subpage webbsidan tillhör.
- Flytta ned. Flyttar ned innehåll från domänen eller underordnad sida lägre i sökresultaten. Du väljer om du vill flytta ned innehåll från domänen eller subpage webbsidan tillhör. 
- PIN-kod-överkant. Definiera på webbsidan som visas överst i resultatet om användarfråga termen exakt matchar frågetermen som du har använt. Den aktiva listan behöver inte innehålla på webbsidan som du kan fästa den. 

Justera rangordning är inte tillgänglig för bildsökning-upplevelse.

## <a name="boosting-and-demoting"></a>Hur du ökar och degradering
Du kan mycket öka, öka, eller degradera alla domäner eller subpage i din aktiva listan. Som standard läggs alla sektorer med samma vikt. Objekt som är mycket förstärkta eller ökat rankas högre i sökresultaten (med super boost rangordning är högre än boost). Objekt som nedgraderas rangordnas lägre i sökresultaten.

Det är viktigt att notera som super öka öka och sänka ge vikt varianter till domäner eller underordnade sidor. Det här är bara en av många signaler som används av rankningen för att fastställa ordningen på resultaten. Det innebär att deras effekt för en specifik fråga inte är säkert som många andra faktorer kan påverka Webbresultat övergripande rangordning.  För att fastställa möjliga effekten har som boosting eller degradering rankningen, testet sökupplevelsen med förhandsgranskningsfönstret.

Du kan mycket öka, förbättra, eller degradera objekt med hjälp av rangordning justera kontrollerna i den aktiva listan eller genom att använda förstärkningen och flytta ned kontroller i förhandsgranskningsfönstret. Tjänsten lägger till sektorn i listan Active och justerar rangordning därefter.

Super öka öka och sänka ändringar sparas automatiskt och återspeglar direkt mot din slutpunkt för anpassad sökning. 

Super öka öka och sänka är inte tillgängliga för bildsökning-upplevelse.

## <a name="pin-to-top"></a>Fästa till början
Välj något av följande alternativ för att fästa en webbsida längst upp i sökresultaten för en specifik fråga:

1.  Ange Webbadressen till webbsidan för att fästa högst upp i resultatet och exakta frågan som ska utlösa den fästa på fliken fästa. 
2.  Ange en frågeterm i förhandsgranskningsfönstret, och klicka på Sök. Därefter identifiera webbsida i de resultat som du vill fästa högst upp i resultatet om användaren anger samma fråga. Klicka på PIN-kod till början. Tjänsten lägger till webbsidan och fråga fästa-objekt. 

Du kan spåra din PIN-koder i fliken fästa. Att PIN-koder visas som\<fråga\>, \<webbsidan\>-par. 

Webbsidan fästs endast om användarens fråga exakt matchar din fråga. 

Du kan fästa högst en enda webbsida att överst bland resultaten för en specifik fråga.

PIN-koder är inte tillgängliga för bildsökning-upplevelse.

## <a name="use-bing-to-specify-slices"></a>Använd Bing för att ange vilka delar
Det finns ett par olika sätt att ange snitt av webben som utgör din anpassade sökning. Om du vet sektorer som du vill ska ingå i din instans kan du lägga till dem i din instans Active lista. Information om att lägga till objekt i listan med Active själv, finns i [aktiv och blockerad listor](#active-and-blocked-lists).
Men om du inte vet vilka segment som ska ingå, kan du köra Bing frågor i förhandsgranskningsfönstret och Visa Bing returnerar. Du kan sedan välja sektorer som du vill ska ingå i din anpassade sökning. Sannolikt måste du köra flera sökord att kontrollera att du identifiera alla sektorer som du vill använda för din instans. 

Följ dessa steg om du vill använda Bing att lägga till segment i din anpassade Sökinstans. 
1.  Logga in på Bing Custom Search [portal](https://customsearch.ai).
2.  Skapa en instans eller välj en instans att uppdatera.
3.  Välj Bing i förhandsgranskningsfönstret till höger, i listrutan.
4.  I sökrutan anger du en frågeterm som är relevant för din instans.
5.  Klicka på **Lägg till plats** bredvid resultatet som du vill inkludera.
6.  Klicka på knappen **OK**.

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

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