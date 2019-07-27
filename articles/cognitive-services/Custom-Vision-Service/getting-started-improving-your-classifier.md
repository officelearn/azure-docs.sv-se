---
title: Förbättra din klassificerare – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du kan förbättra klassificerarens kvalitet.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: d71c750185589fd488df70b63fd48e9e674ee3dc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561039"
---
# <a name="how-to-improve-your-classifier"></a>Förbättra din klassificerare

I den här guiden får du lära dig hur du förbättrar kvaliteten på din Custom Vision Service klassificerare. Klassificeringens kvalitet beror på mängden, kvaliteten och antalet olika märkta data som du anger och hur balanserade den övergripande data uppsättningen är. En bra klassificerare har en balanserade inlärnings data uppsättning som är representativ för vad som kommer att skickas till klassificeraren. Processen för att skapa en sådan klassificerare är iterativ. Det är vanligt att ta några få avvisningar för att uppnå förväntade resultat.

Följande är ett allmänt mönster som hjälper dig att bygga en mer exakt klassificerare:

1. Första rundade utbildningen
1. Lägg till fler bilder och Balance data; omtrimma
1. Lägg till bilder med varierande bakgrund, belysning, objekt storlek, kamera vinkel och stil; omtrimma
1. Använd nya avbildningar för att testa förutsägelse
1. Ändra befintliga tränings data enligt förutsägelse resultat

## <a name="prevent-overfitting"></a>Förhindra överanpassning

Ibland kan en klassificerare lära sig att fatta förutsägelser baserat på valfria egenskaper som dina bilder har gemensamt. Om du till exempel skapar en klassificerare för äpplen eller citrus, och du har använt bilder av äpplen i händerna och av citrus på vita plåtar, kan klassificeraren ge onödigt stor betydelse för praktiska och plåtar i stället för äpplen eller citrus.

![Bild av oväntad klassificering](./media/getting-started-improving-your-classifier/unexpected.png)

Du kan åtgärda det här problemet med hjälp av följande rikt linjer för utbildning med fler varierade bilder: ge bilder med olika vinklar, bakgrunder, objekt storlek, grupper och andra variationer.

## <a name="data-quantity"></a>Data mängd

Antalet utbildnings bilder är den viktigaste faktorn. Vi rekommenderar att du använder minst 50 bilder per etikett som start punkt. Med färre avbildningar, finns det en högre risk för överanpassning, och när prestanda siffrorna kan föreslå bra kvalitet kan din modell ha problem med verkliga data. 

## <a name="data-balance"></a>Data balans

Det är också viktigt att överväga de relativa mängderna av dina utbildnings data. Till exempel kan du använda 500-bilder för en etikett och 50 bilder för en annan etikett för en data uppsättning som är i balans. Detta gör att modellen är mer korrekt för att förutsäga en etikett än en annan. Du får förmodligen bättre resultat om du behåller minst ett 1:2-förhållande mellan etiketten med minst bilder och etiketten med de flesta bilderna. Om etiketten med de flesta bilder till exempel har 500 bilder bör etiketten med minst bilder ha minst 250 bilder för träning.

## <a name="data-variety"></a>Data mängd

Se till att använda bilder som är representativa för vad som kommer att skickas till klassificeraren under normal användning. Annars kunde din klassificerare lära sig att fatta förutsägelser utifrån valfria egenskaper som dina bilder har gemensamt. Om du till exempel skapar en klassificerare för äpplen eller citrus, och du har använt bilder av äpplen i händerna och av citrus på vita plåtar, kan klassificeraren ge onödigt stor betydelse för praktiska och plåtar i stället för äpplen eller citrus.

![Bild av oväntad klassificering](./media/getting-started-improving-your-classifier/unexpected.png)

Du kan åtgärda det här problemet genom att inkludera en mängd olika avbildningar för att säkerställa att klassificeraren kan generaliseras. Nedan visas några exempel på hur du kan göra inlärningen Mer Diverse:

* __Lägg__ Ange bilder för ditt objekt framför olika bakgrunder. Foton i naturliga sammanhang är bättre än foton framför neutrala bakgrunder eftersom de ger mer information om klassificeraren.

    ![Bild av bakgrunds exempel](./media/getting-started-improving-your-classifier/background.png)

* __Blixt__ Ge bilder med varierande belysning (det vill säga med blixt, hög exponering och så vidare), särskilt om de bilder som används för förutsägelsen har olika belysning. Det är också användbart att använda bilder med varierande mättnad, nyans och ljus styrka.

    ![Bild av belysnings exempel](./media/getting-started-improving-your-classifier/lighting.png)

* __Objekt storlek:__ Ange bilder där objekten varierar i storlek och nummer (till exempel ett foto av bananer och en närbild av en enda banan). Olika storlekar hjälper klassificeraren att generaliseras bättre.

    ![Bild av storleks exempel](./media/getting-started-improving-your-classifier/size.png)

* __Kamera vinkel:__ Ange bilder tagna med olika kamera vinklar. Alternativt, om alla dina foton måste tas med fasta kameror (till exempel övervaknings kameror), måste du tilldela en annan etikett till varje objekt som utförs regelbundet för att undvika överanpassning&mdash;av orelaterade objekt (till exempel lampposts) som nyckel funktion.

    ![Bild av vinkel exempel](./media/getting-started-improving-your-classifier/angle.png)

* __ATS__ Ange bilder av olika format för samma klass (till exempel olika sorters frukt). Men om du har objekt av drastiskt olika format (t. ex. Mickey mus eller en verklig mus) rekommenderar vi att du ger dem etiketter som separata klasser för att bättre representera deras distinkta funktioner.

    ![Bild av format exempel](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Negativa bilder

Vid något tillfälle i projektet kan du behöva lägga till _negativa exempel_ för att göra din klassificerar mer exakt. Negativa exempel är de som inte matchar någon av de andra taggarna. När du överför de här avbildningarna ska du använda den speciella **negativa** etiketten på dem.

> [!NOTE]
> Custom Vision Service stöder viss automatisk negativ bild hantering. Om du till exempel skapar en druvmust för druvmust och banan och skickar en bild av en sko för förutsägelse, bör klassificeraren lägga ut bilden som nära 0% för både druvmust och banan.
> 
> Å andra sidan, i de fall där de negativa bilderna bara är en variation av de bilder som används i utbildningen, är det troligt att modellen klassificerar de negativa bilderna som en etikettad klass på grund av fantastiska likheter. Om du till exempel har en orange vs. grapefrukt-klassificerare och du matar in en bild av en Clementine kan det leda till att Clementine är orange eftersom många funktioner i Clementine liknar dem för apelsiner. Om dina negativa bilder är av samma typ, rekommenderar vi att du skapar en eller flera ytterligare Taggar (till exempel **andra**) och etiketterar de negativa bilderna med den här taggen under träningen så att modellen bättre skiljer sig mellan dessa klasser.

## <a name="use-prediction-images-for-further-training"></a>Använd förutsägelse avbildningar för ytterligare utbildning

När du använder eller testar avbildnings klassificeraren genom att skicka avbildningar till förutsägelse slut punkten, lagrar Custom Vision tjänsten dessa avbildningar. Du kan sedan använda dem för att förbättra modellen.

1. Om du vill visa bilder som skickats till klassificeraren öppnar du [Custom vision webb sidan](https://customvision.ai), går till projektet och väljer fliken __förutsägelser__ . Standardvyn visar bilder från den aktuella iterationen. Du kan använda List menyn __iteration__ för att visa bilder som skickats under föregående iterationer.

    ![skärm bild av fliken förutsägelser med bilder i vyn](./media/getting-started-improving-your-classifier/predictions.png)

2. Hovra över en bild för att se de taggar som förutsägs av klassificeraren. Bilderna sorteras så att de som kan få flest förbättringar av klassificeraren visas överst. Om du vill använda en annan sorterings metod gör du ett val i avsnittet __Sortera__ . 

    Om du vill lägga till en bild i dina befintliga utbildnings data väljer du bilden, anger rätt tagg (er) och klickar på __Spara och Stäng__. Bilden tas bort från förutsägelserna  och läggs till i uppsättningen med utbildnings bilder. Du kan visa den genom att välja fliken __träna bilder__ .

    ![Bild av taggnings Sidan](./media/getting-started-improving-your-classifier/tag.png)

3. Använd sedan knappen __träna__ för att träna om klassificeraren.

## <a name="visually-inspect-predictions"></a>Granska förutsägelser visuellt

Om du vill kontrol lera bild förutsägelserna går du till fliken __inlärnings bilder__ , väljer din tidigare  inlärning på upprepnings menyn och markerar en eller flera taggar under avsnittet **taggar** . Vyn bör nu visa en röd ruta runt var och en av de bilder som modellen inte kunde förutsäga den angivna taggen på rätt sätt.

![Bild av upprepnings historiken](./media/getting-started-improving-your-classifier/iteration.png)

Ibland kan en visuell granskning identifiera mönster som du sedan kan korrigera genom att lägga till fler utbildnings data eller ändra befintliga tränings data. En klassificerare för äpplen eller kalk kan till exempel felaktigt märka alla gröna äpplen som kalk. Du kan sedan åtgärda problemet genom att lägga till och tillhandahålla tränings data som innehåller taggade bilder av grön äpplen.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig flera tekniker för att göra din anpassade bild klassificerings modell mer exakt. Nu ska du lära dig hur du testar bilder genom programmering genom att skicka dem till förutsägelse-API: et.

> [!div class="nextstepaction"]
> [Använd förutsägelse-API: et](use-prediction-api.md)
