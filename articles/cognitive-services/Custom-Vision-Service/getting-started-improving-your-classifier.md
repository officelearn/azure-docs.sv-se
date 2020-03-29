---
title: Förbättra klassificeraren - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur mängden, kvaliteten och mångfalden av data kan förbättra kvaliteten på klassificeraren i tjänsten Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: c2858d5f9bca662cbbcd48b2345a7dc2c7ae48b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718540"
---
# <a name="how-to-improve-your-classifier"></a>Förbättra din klassificerare

I den här guiden får du lära dig hur du förbättrar kvaliteten på din Anpassade Vision Service classifier. Kvaliteten på klassificeraren beror på mängden, kvaliteten och variationen av de märkta data som du tillhandahåller den och hur balanserad den totala datauppsättningen är. En bra klassificerare har en balanserad träningsdatauppsättning som är representativ för vad som ska skickas till klassificeraren. Processen att bygga en sådan klassificerare är iterativ; Det är vanligt att ta några rundor av utbildning för att nå förväntade resultat.

Följande är ett allmänt mönster som hjälper dig att skapa en mer exakt klassificerare:

1. Utbildning i första omgången
1. Lägg till fler bilder och balansera data; Omskola
1. Lägg till bilder med varierande bakgrund, belysning, objektstorlek, kameravinkel och stil. Omskola
1. Använd nya bilder för att testa förutsägelse
1. Ändra befintliga träningsdata enligt förutsägelseresultat

## <a name="prevent-overfitting"></a>Förhindra övermontering

Ibland lär sig en klassificerare att göra förutsägelser baserat på godtyckliga egenskaper som dina bilder har gemensamt. Till exempel, om du skapar en klassificerare för äpplen vs citrus, och du har använt bilder av äpplen i händer och citrus på vita plattor, kan klassificeraren ge onödig betydelse för händer kontra plattor, snarare än äpplen vs citrus.

![Bild av oväntad klassificering](./media/getting-started-improving-your-classifier/unexpected.png)

Om du vill åtgärda problemet kan du använda följande riktlinjer för utbildning med mer varierade bilder: ge bilder olika vinklar, bakgrunder, objektstorlek, grupper och andra varianter.

## <a name="data-quantity"></a>Datakvantitet

Antalet träningsbilder är den viktigaste faktorn. Vi rekommenderar att du använder minst 50 bilder per etikett som utgångspunkt. Med färre bilder finns det en högre risk för övermontering, och även om dina prestandasiffror kan tyda på god kvalitet kan din modell kämpa med verkliga data. 

## <a name="data-balance"></a>Databalans

Det är också viktigt att tänka på de relativa mängderna av dina träningsdata. Om du till exempel använder 500 bilder för en etikett och 50 bilder för en annan etikett blir det en obalanserad träningsdatauppsättning. Detta gör att modellen är mer exakt när det gäller att förutsäga en etikett än en annan. Du kommer sannolikt att se bättre resultat om du behåller minst ett 1:2-förhållande mellan etiketten med minst antal bilder och etiketten med flest bilder. Om etiketten med flest bilder till exempel har 500 bilder bör etiketten med minst bilder ha minst 250 bilder för träning.

## <a name="data-variety"></a>Variation av data

Var noga med att använda bilder som är representativa för vad som kommer att skickas till klassificeraren under normal användning. Annars kan klassificeraren lära sig att göra förutsägelser baserat på godtyckliga egenskaper som dina bilder har gemensamt. Till exempel, om du skapar en klassificerare för äpplen vs citrus, och du har använt bilder av äpplen i händer och citrus på vita plattor, kan klassificeraren ge onödig betydelse för händer kontra plattor, snarare än äpplen vs citrus.

![Bild av oväntad klassificering](./media/getting-started-improving-your-classifier/unexpected.png)

Om du vill åtgärda problemet kan du inkludera en mängd olika bilder för att säkerställa att klassificeraren kan generalisera väl. Nedan följer några sätt du kan göra din träningsuppsättning mer varierad:

* __Bakgrund:__ Ange bilder av ditt objekt framför olika bakgrunder. Bilder i naturliga sammanhang är bättre än bilder framför neutrala bakgrunder eftersom de ger mer information för klassificeraren.

    ![Bild av bakgrundsexempel](./media/getting-started-improving-your-classifier/background.png)

* __Belysning:__ Ge bilder med varierad belysning (det vill säga tagen med blixt, hög exponering och så vidare), särskilt om de bilder som används för förutsägelse har olika belysning. Det är också bra att använda bilder med varierande mättnad, nyans och ljusstyrka.

    ![Bild av belysningsprover](./media/getting-started-improving-your-classifier/lighting.png)

* __Objektstorlek:__ Ge bilder där objekten varierar i storlek och antal (till exempel ett foto av klasar av bananer och en närbild av en enda banan). Olika storlek hjälper klassificeraren generalisera bättre.

    ![Bild av storleksprover](./media/getting-started-improving-your-classifier/size.png)

* __Kameravinkel:__ Ge bilder tagna med olika kameravinklar. Alternativt, om alla dina bilder måste tas med fasta kameror (t.ex. övervakningskameror), se till att tilldela en&mdash;annan etikett till varje regelbundet förekommande objekt för att undvika övermontering tolka icke-närstående objekt (t.ex. lyktstolpar) som nyckelfunktion.

    ![Bild av vinkelprover](./media/getting-started-improving-your-classifier/angle.png)

* __Stil:__ Tillhandahålla bilder av olika stilar av samma klass (till exempel olika sorter av samma frukt). Men om du har objekt med drastiskt olika stilar (till exempel Musse Pigg vs en verklig mus), rekommenderar vi att du märker dem som separata klasser för att bättre representera deras olika funktioner.

    ![Bild av formatprover](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Negativa bilder

Vid något tillfälle i projektet kan du behöva lägga till _negativa exempel_ för att göra klassificeraren mer exakt. Negativa exempel är de som inte matchar någon av de andra taggarna. När du laddar upp dessa bilder, tillämpa den särskilda **negativa** etiketten på dem.

> [!NOTE]
> Tjänsten Custom Vision stöder en viss automatisk negativ bildhantering. Till exempel, om du bygger en druva vs banan klassificerare och lämna in en bild av en sko för förutsägelse, bör klassificeraren poäng den bilden så nära 0% för både druva och banan.
> 
> Å andra sidan, i de fall där de negativa bilderna är bara en variant av de bilder som används i utbildning, är det troligt att modellen kommer att klassificera de negativa bilderna som en märkt klass på grund av de stora likheterna. Till exempel, om du har en orange vs grapefrukt klassificerare, och du matar in en bild av en clementin, kan det poäng clementin som en orange eftersom många funktioner i clementin liknar de av apelsiner. Om dina negativa bilder är av detta slag rekommenderar vi att du skapar en eller flera ytterligare taggar (till exempel **Andra)** och märker de negativa bilderna med den här taggen under träningen så att modellen bättre kan skilja mellan dessa klasser.

## <a name="use-prediction-images-for-further-training"></a>Använd förutsägelsebilder för vidareutbildning

När du använder eller testar bildklassificeraren genom att skicka bilder till slutpunkten förutsägelse lagras dessa bilder i tjänsten Custom Vision. Du kan sedan använda dem för att förbättra modellen.

1. Om du vill visa bilder som skickats till klassificeraren öppnar du [webbsidan Anpassad vision,](https://customvision.ai)går till projektet och väljer fliken __Förutsägelser.__ Standardvyn visar bilder från den aktuella iterationen. Du kan använda listrutan __Iteration__ för att visa bilder som skickats in under tidigare iterationer.

    ![skärmbild av fliken förutsägelser, med bilder i sikte](./media/getting-started-improving-your-classifier/predictions.png)

2. Hovra över en bild för att se taggarna som förutspåddes av klassificeraren. Bilderna sorteras så att de som kan ge flest förbättringar av klassificeraren visas överst. Om du vill använda en annan sorteringsmetod gör du en markering i avsnittet __Sortera.__ 

    Om du vill lägga till en bild i befintliga träningsdata markerar du bilden, ställer in rätt tagg och klickar på __Spara och stäng__. Bilden tas bort från __Förutsägelser__ och läggs till i uppsättningen träningsbilder. Du kan visa den genom att välja fliken __Träningsbilder.__

    ![Bild på taggningssidan](./media/getting-started-improving-your-classifier/tag.png)

3. Använd sedan __tågknappen__ för att träna om klassificeraren.

## <a name="visually-inspect-predictions"></a>Inspektera förutsägelser visuellt

Om du vill granska bildförutsägelser går du till fliken __Träningsbilder,__ väljer din tidigare träningsiteritering i listrutan **Iteration** och kontrollerar en eller flera taggar under avsnittet **Taggar.** Vyn ska nu visa en röd ruta runt var och en av bilderna för vilka modellen inte korrekt kunde förutsäga den angivna taggen.

![Bild av iterationshistoriken](./media/getting-started-improving-your-classifier/iteration.png)

Ibland kan en visuell inspektion identifiera mönster som du sedan kan korrigera genom att lägga till mer träningsdata eller ändra befintliga träningsdata. Till exempel kan en klassificerare för äpplen vs limes felaktigt märka alla gröna äpplen som limefrukter. Du kan sedan korrigera det här problemet genom att lägga till och tillhandahålla träningsdata som innehåller taggade bilder av gröna äpplen.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig flera tekniker för att göra din anpassade bildklassificeringsmodell mer exakt. Lär dig sedan hur du testar bilder programmässigt genom att skicka dem till förutsägelse-API:et.

> [!div class="nextstepaction"]
> [Använd förutsägelse-API:t](use-prediction-api.md)
