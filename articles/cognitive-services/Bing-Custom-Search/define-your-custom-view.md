---
title: 'Bing anpassad sökning: Definiera en anpassad vy | Microsoft Docs'
description: Beskriver hur du skapar en plats och lodräta search-tjänster
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 8ffe3087df398d6310828e41d0c6992199fafbed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352905"
---
# <a name="configure-your-custom-search-experience"></a>Konfigurera din anpassade sökinställningar
En anpassad sökning-instans kan du skräddarsy sökinställningar om du vill inkludera innehåll från webbplatser som användarna som intresserar dig. I stället för en sökning webbkomponenterna, söker Bing endast sektorn för webbplatsen som intresserar dig.
Använd Bing anpassad sökning för att skapa den anpassade vyn för webben [portal](https://customsearch.ai). Information om att logga in på portalen finns [skapa din första Bing anpassad sökning instans](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). Portalen kan du skapa en sökning-instans som anger domäner, underordnade sidor, och webbsidor som du vill använda Bing för att söka och de som du inte vill att söka. Ange URL: er för det innehåll som du vet om, kan du också begära portalen för att föreslå innehåll som du vill lägga till i vyn. Följande är ett sätt att du kan definiera ett segment av webbplatsen: 

1.  Domän. En sektor i domänen innehåller allt innehåll i en internet-domän. Till exempel www.microsoft.com. Om du utesluter ”www” gör Bing också söka domänens underdomäner. Till exempel om du anger microsoft.com returnerar Bing också resultat från support.microsoft.com eller technet.microsoft.com.
2.  Underordnad sida. En underordnad sida sektor innehåller allt innehåll i den underordnade och sökvägar under den. Du kan ange upp till två underordnade sidor i sökvägen. Till exempel www.microsoft.com/en-us/windows/ 
3.  Webbsidan. En sektor webbsidan kan inkludera endast webbsidan i en anpassad sökning. Alternativt kan du ange om du vill inkludera underordnade sidor.

Alla domäner underordnade sidor och webbsidor som du anger måste vara offentliga och indexerade från Bing. Om du äger en offentlig webbplats som du vill inkludera i sökningen och Bing så att den inte har indexerats, se Bing [webbadministratör dokumentationen](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) mer information om hur du får Bing att indexera den. Se även webbadministratör dokumentationen för information om hur du får Bing uppdatera webbplatsen crawlade om indexet är inaktuell.

## <a name="adding-slices-to-your-custom-search"></a>Lägga till segment i sökningen anpassad
När du definierar din egen search-instansen, ange aktiva och blockerade domäner, underordnade sidor och webbsidor som du vill söka efter inte.  

- Aktiv: En lista över domäner, underordnade sidor eller webbsidor ska ingå i sökningen. 
- Blockeras: En lista över domän, underordnade sidor eller webbsidor ska undantas från sökningen. De objekt som du blockerar ska vara innehåll domäner och underordnade sidor visas i listan med aktiva.

Klicka på flikarna aktiv och blockerade i din instans av anpassad sökning för att komma åt varje lista. 

<a name="active-and-blocked-lists"></a>
## <a name="active-and-blocked-lists"></a>Aktiv och blockerad listor 
Klicka på Ange en sektor i den webbplats som du vill Bing för att söka i **Active** lista över domäner, underordnade sidor och webbsidor att söka och fliken. Du kan lägga till ett segment direkt i listan eller lägga till fler än ett segment genom att ladda upp en textfil med hjälp av ikonen överföringen.

Överför information: 

- Ladda upp filen är bara tillgängligt för att lägga till segment i listan över aktiva, du kan inte använda den för att lägga till segment i listan över blockerade. 
- Skapa en textfil och ange en enda domän, en underordnad sida eller en webbsida per rad. Hela överföringen avvisas om ett fel inträffar. 
- Om listan över blockerade innehåller domänen, underordnad sida eller webbsida som du angav i överför filen, tas den bort från listan över blockerade tjänsten och lägger till den i den aktiva listan. 
- Tjänsten ignorerar dubbletter i uppladdningsfilen.

Om du vill redigera eller ta bort segment, Använd alternativ under kolumnen kontroller. 

På samma sätt kan du lägga till segment i listan blockerad (utom om du inte använda en uppladdningsfilen för att ange sektorer).

## <a name="pinned-list"></a>Övre listan
Portalen kan du fästa en specifik webbsida överst i sökresultatet om användaren anger en specifik sökterm. Den **fästa** fliken innehåller en lista över frågan termen webbsidan par och som anger på webbsidan som visas som det översta resultatet för en specifik fråga. Användarens frågeterm måste exakt matcha Fäst frågeterm.
Information om fästning resultat finns [justera rang](#adjustrank).

Fästning resultat är inte tillgänglig för avbildning sökinställningar.

## <a name="site-suggestions"></a>Förslag på platsen
När du lägger till segment i listan över aktiva, genererar tjänsten platsen och underordnade förslag som du kan lägga till i sökningen. Den **kanske du vill lägga till** avsnittet innehåller förslagen. Instanssidan innehåller det här avsnittet om förslag är tillgängliga. 

Lägg till förslag i listan över aktiva, klicka på + -ikonen.  Eftersom tjänsten genererar förslag baserat på dina inställningar, ska du klicka på **uppdatera** när du lägger till förslagen. 

## <a name="preview-pane"></a>Förhandsgranskning
Du kan testa din sökning-instans som använder förhandsgranskningsfönstret till höger för att skicka sökfrågor och visa resultat. Välj **Mina instans**, väljer du en säker sökfilter och vad marknaden för att söka (se [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters). Ange en fråga och tryck på enter eller klicka på sökikonen om du vill visa resultaten från den aktuella konfigurationen. Se web resultaten klickar du på **Web**, för att se bilden resultaten klickar du på **bild**. 

 Med hjälp av förhandsgranskningsfönstret, du kan också granska Bing resultat genom att välja **Bing** i stället för **Mina instans**. Detta kan vara användbart att jämföra resultatet från dina sökinställningar till resultat från Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Justera rang
Portalen kan du justera rangordning för att manipulera de resultat som Bing returnerar. Ange en sökterm i rutan Förhandsgranska och köra frågan. Förhandsgranskningsfönstret lista sökresultatet för frågan. Till höger om varje resultat är listan över justeringar, kan du se. 

- Block. Flyttar domänen, underordnad sida eller webbsidan till listan över blockerade. Du väljer att blockera nivån. Bing undantar innehåll från den valda webbplatsen i sökresultaten. 
- Öka. Förstärker innehåll från den domän eller en underordnad sida högre upp i sökresultatet. Du väljer om du vill öka innehåll från domänen eller subpage webbsidan tillhör.
- Degradera. Flyttar innehåll från den domän eller en underordnad sida lägre i sökresultaten. Du väljer om du vill degradera innehåll från domänen eller subpage webbsidan tillhör. 
- PIN-överkant. Definiera webbsidan som visas överst i resultatet om användarfrågan termen exakt matchar frågetermen som du har använt. Den aktiva listan inte innehåller webbsidan som du kan fästa den. 

Justera rang är inte tillgänglig för avbildning sökinställningar.

## <a name="boosting-and-demoting"></a>Förstärkning och degradering
Du kan super höja, öka, eller degraderar en domän eller subpage i listan med aktiva. Som standard läggs alla segment med samma vikt. Objekt som Super ökat eller ökat rankas högre i sökresultaten (med super förstärkningen rangordning är högre än förstärkningen). Objekt som nedgraderas rangordnas lägre i sökresultaten.

Det är viktigt att notera som super öka öka och sänka ge vikt varianter till domäner eller underordnade sidor. Detta är bara en av många signaler som används av ranker för att bestämma vilken resultaten. Det innebär att deras effekt för en specifik fråga inte kan garanteras som många faktorer kan påverka den övergripande rangordningen av web resultaten.  För att fastställa möjliga effekten som förstärkning eller degradering har på ranker, testet sökinställningar med hjälp av förhandsgranskningsfönstret.

Du kan super öka, förbättra, eller degradera objekt genom att använda rangordning justera kontrollerna i den aktiva listan eller genom att använda förstärkningen och sänka kontroller i förhandsgranskningsfönstret. Tjänsten lägger till sektorn i listan aktiva och justerar rangordning därefter.

Super öka öka och sänka ändringar sparas automatiskt och återspeglar direkt mot slutpunkten anpassad sökning. 

Super öka öka och sänka är inte tillgängliga för sökinställningar för avbildningen.

## <a name="pin-to-top"></a>Fästa upp
Välj något av följande alternativ för att fästa en webbsida överst i sökresultatet för en specifik fråga:

1.  Ange Webbadressen till webbsidan för att fästa till början av resultaten och exakt frågan som ska utlösa den fästning fästa på fliken. 
2.  Ange en frågeterm i förhandsgranskningsfönstret, och klicka på Sök. Därefter identifiera webbsidan i de resultat som du vill fästa överst i resultatet om användaren anger samma fråga. Klicka på PIN-kod till överst på sidan. Tjänsten lägger till webbsidan och fråga fästa-objekt. 

Du kan spåra din PIN-koder i fliken fästa. De PIN-koderna visas som\<frågan\>, \<webbsidan\>-par. 

Webbsidan fästs bara om användarens fråga exakt matchar din fråga. 

Du kan fästa maximalt en webbsida överst i resultaten för en specifik fråga.

PIN-koder är inte tillgängliga för sökinställningar för avbildningen.

## <a name="use-bing-to-specify-slices"></a>Använd Bing för att ange segment
Det finns ett par olika sätt att ange sektorerna i webbplatsen som utgör sökningen anpassad. Om du vet de segment som du vill inkludera i din instans, Lägg till dem din instans aktiva listan. För information om att lägga till objekt i listan med aktiva själv, se [aktiv och blockerad listor](#active-and-blocked-lists).
Men om du inte vet vilka segment att inkludera, kan du köra Bing frågor i förhandsgranskningsfönstret och se Bing returnerar. Du kan välja de segment som du vill inkludera i sökningen anpassad. Du troligen behöver köra flera sökord att se till att du identifiera alla segment som du vill använda för din instans. 

Följ dessa steg om du vill använda Bing för att lägga till segment i din instans av anpassad sökning. 
1.  Logga in på Bing anpassad sökning [portal](https://customsearch.ai).
2.  Skapa en instans eller välj en instans att uppdatera.
3.  Välj Bing i listrutan i rutan Förhandsgranska på höger sida.
4.  I sökrutan anger du en frågeterm som är relevanta för din instans.
5.  Klicka på **Lägg till webbplats** bredvid resultatet som du vill ha.
6.  Klicka på knappen **OK**.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Visa statistik
Om du prenumererar på anpassad sökning på lämplig nivå (finns i [priser sidor](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), en **statistik** flik läggs till produktion-instanser. Fliken statistik innehåller information om hur dina slutpunkter för anpassad sökning används, inklusive samtalsvolym, de vanligaste frågorna, geografisk fördelning, svarskoder och säker sökning. Du kan filtrera information med hjälp av angivna kontrollerna.

## <a name="understanding-quota"></a>Förstå kvot
- För varje anpassad sökning-instans, det maximala antalet rangordning justeringar som du kan göra att **Active** och **blockerad** segment är begränsad till 400.
- Lägga till en sektor i flikarna Active eller blockerad räknas som en rangordning justering.
- Förstärkning och degradering antal som två rangordning justeringar.
- För varje anpassad sökning-instans är det maximala antalet PIN-koder som du kan göra begränsad till 200.

## <a name="next-steps"></a>Nästa steg

- [Anropa sökningen anpassad](./search-your-custom-view.md)
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)
- [Markera text med hjälp av decoration markörer](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)