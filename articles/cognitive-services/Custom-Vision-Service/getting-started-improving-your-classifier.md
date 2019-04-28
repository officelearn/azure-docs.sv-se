---
title: Förbättra din klassificerare – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Lär dig att förbättra kvaliteten på din klassificerare.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 35f83832b0ceb7507b39095e9cc974d82a480c69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60606794"
---
# <a name="how-to-improve-your-classifier"></a>Hur vi kan förbättra din klassificerare

I den här guiden får du lära dig hur du kan förbättra kvaliteten på din klassificerare för Custom Vision Service. Kvaliteten på din klassificerare beror på mängden, kvaliteten och olika typer av märkta data som du anger den och hur belastningsutjämnade övergripande datauppsättningen är. En bra klassificerare har en datauppsättning för belastningsutjämnade träning som är representativ för vad som ska skickas till klassificeraren. Processen för att skapa en sådan klassificerare är iterativ; Det är vanligt att ta ett par Avrundar om utbildning för att nå önskat resultat.

Följande är ett allmänt mönster för att skapa en mer exakta klassificerare:

1. Första round utbildning
1. Lägga till fler avbildningar och belastningsutjämna data; omtrimma
1. Lägga till avbildningar med olika bakgrund, belysning, Objektstorlek, kameravinkel och linje. omtrimma
1. Använda nya avbildningar för att testa förutsägelse
1. Ändra befintlig träningsdata enligt förutsagda resultaten

## <a name="prevent-overfitting"></a>Förhindra overfitting

Ibland kan en klassificerare lära dig göra förutsägelser utifrån godtyckliga egenskaper som är gemensamma för dina avbildningar. Om du skapar en klassificerare för äpplen kontra citrus och du har använt avbildningar av äpplen i händer och citrus på vit nivåer, kan klassificeraren ge onödig extra prioritet till hands jämfört med nivåer i stället för äpplen kontra citrus.

![Bild av oväntat klassificering](./media/getting-started-improving-your-classifier/unexpected.png)

Om du vill åtgärda det här problemet använder du följande riktlinjer på utbildning med flera olika bilder: ger bilder med olika vinklar, bakgrunder, Objektstorlek, grupper och andra ändringar.

## <a name="data-quantity"></a>Data kvantitet

Antalet inlärningsbilder är den viktigaste faktorn. Vi rekommenderar att du använder minst 50 bilder per etikett som utgångspunkt. Det finns en större risk för overfitting med färre bilder och medan dina prestanda-nummer kan föreslå god kvalitet, din modell kan behöva kämpa med verkliga data. 

## <a name="data-balance"></a>Data saldo

Det är också viktigt att tänka på den relativa mängden dina utbildningsdata. Till exempel gör med hjälp av 500 avbildningar för en etikett och 50 avbildningar för en annan etikett för en imbalanced utbildning datamängd. Detta innebär att modellen ska bli mer rättvisande i att förutsäga en etikett än en annan. Du kommer förmodligen att se bättre resultat om du ha minst ett 1:2 förhållandet mellan etiketten med minst antal avbildningar och etiketten med de flesta bilder. Om etiketten med de flesta bilder har 500 bilder, bör etiketten med minst bilderna ha minst 250 avbildningar för utbildning.

## <a name="data-variety"></a>Mängd data

Glöm inte att använda avbildningar som är representativ för vad som ska skickas till klassificeraren under normal användning. I annat fall kan din klassificerare Lär dig att göra förutsägelser utifrån godtyckliga egenskaper som är gemensamma för dina avbildningar. Om du skapar en klassificerare för äpplen kontra citrus och du har använt avbildningar av äpplen i händer och citrus på vit nivåer, kan klassificeraren ge onödig extra prioritet till hands jämfört med nivåer i stället för äpplen kontra citrus.

![Bild av oväntat klassificering](./media/getting-started-improving-your-classifier/unexpected.png)

Lös problemet, ta med olika bilder för att se till att din klassificerare kan också generalisera. Nedan finns några sätt som du kan göra utbildning att ange mer diversifierad:

* __Bakgrund:__ Ger bilder för objektet framför olika bakgrunder. Foton i naturlig sammanhang är bättre än foton framför neutral bakgrunder eftersom de ger mer information om klassificeraren.

    ![Bild av BITS-exempel](./media/getting-started-improving-your-classifier/background.png)

* __Ljus:__ Ger bilder med olika belysning (som, tas med flash, hög exponering), och så vidare, särskilt om bilder som används för förutsägelse har olika belysning. Det är också bra att använda bilder i olika mättnad, hue och intensitet.

    ![Bild av belysning-exempel](./media/getting-started-improving-your-classifier/lighting.png)

* __Objektstorlek:__ Ger bilder objekten kan variera i storlek och nummer (till exempel ett foto av knippen bananer och en närbild av en enda bananer). Olika storlek hjälper klassificeraren generalisera bättre.

    ![Bild av storlek-exempel](./media/getting-started-improving-your-classifier/size.png)

* __Kameravinkel:__ Ger bilder med olika kameravinklar. Du kan också om alla dina foton måste du vara noggrann med fast kameror (till exempel övervakningskameror), måste du ange en annan etikett för varje inträffar regelbundet objekt att undvika overfitting&mdash;tolka orelaterade objekt (till exempel lampposts) som nyckelegenskap.

    ![Bild av vinkel-exempel](./media/getting-started-improving-your-classifier/angle.png)

* __Format:__ Ger bilder av olika typer av samma klass (till exempel varianter av samma frukt). Om du har objekt av drastiskt olika format (till exempel Mickey mus jämfört med musen verkliga) rekommenderar vi dock dem etiketter som separata klasser för att bättre representerar deras olika funktioner.

    ![Bild av formatmallar](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Negativa bilder

Vid en viss tidpunkt i projektet kan du behöva lägga till _negativt exempel_ för att göra din klassificerare mer exakta. Negativa prov är de som inte matchar någon av de andra taggarna. När du överför dessa avbildningar, gäller särskilda **negativt** etiketten på dem.

> [!NOTE]
> Custom Vision Service har stöd för vissa automatiska negativt bildhantering. Om du skapar en delvis kontra bananer klassificerare och skicka in en avbildning av en sko för förutsägelse bör klassificeraren exempelvis score avbildningen som nära 0% för både delvis och bananer.
> 
> Å andra sidan i fall där negativt bilderna är bara en variant av avbildningarna som används i utbildning, är det troligt att modellen klassificera negativt avbildningar som en klass som är märkta på grund av bra likheterna. Till exempel om du har en orange kontra grapefrukter klassificerare och du flöde i en avbildning av en clementine, kan det bedöma clementine som ett orange eftersom många funktioner i clementine liknar apelsiner. Om dina negativt avbildningar är av den här typen kan vi rekommenderar att du skapar en eller flera ytterligare taggar (till exempel **andra**) och märka negativt bilder med den här taggen under utbildning för att tillåta modellen bättre skilja mellan dessa klasser .

## <a name="use-prediction-images-for-further-training"></a>Använd förutsägelse avbildningar för ytterligare utbildning

När du använder eller testa klassificerare för avbildning genom att skicka avbildningar till slutpunkten för förutsägelse lagrar Custom Vision service dessa avbildningar. Du kan sedan använda dem för att förbättra modellen.

1. Om du vill visa bilder som skickas till klassificeraren, öppna den [Custom Vision-webbsida](https://customvision.ai), gå till ditt projekt och väljer den __förutsägelser__ fliken. Standardvyn visar avbildningar från den aktuella iterationen. Du kan använda den __Iteration__ nedrullningsbara meny och visa bilder som har skickats under föregående iterationer.

    ![Skärmbild av förutsägelserna fliken med bilder i vyn](./media/getting-started-improving-your-classifier/predictions.png)

2. Hovra över en bild att se vilka taggar som har uppskattad av klassificeraren. Avbildningar sorteras så att de vilket kan de förbättringar för klassificerarna visas längst upp. Om du vill använda en annan sortering metod, gör ett val i den __sortera__ avsnittet. 

    Om du vill lägga till en bild i dina befintliga utbildningsdata, Välj avbildningen och ange rätt taggar, klicka __spara och Stäng__. Avbildningen tas bort från __förutsägelser__ och läggs till i uppsättningen med inlärningsbilder. Du kan visa den genom att välja den __Inlärningsbilder__ fliken.

    ![Bild av sidan taggning](./media/getting-started-improving-your-classifier/tag.png)

3. Använd sedan den __träna__ knappen för att träna om klassificeraren.

## <a name="visually-inspect-predictions"></a>Visuellt inspektera förutsägelser

Om du vill kontrollera avbildningen förutsägelser, går du till den __Inlärningsbilder__ , Välj din föregående iteration utbildning i den **Iteration** nedrullningsbara menyn och markera en eller flera taggar under den **taggar** avsnittet. Vyn bör nu visa en röd ram runt var och en av avbildningarna som modellen det gick inte att korrekt förutse den angivna taggen.

![Bild av iteration historiken](./media/getting-started-improving-your-classifier/iteration.png)

Ibland kan en visuell granskning identifiera mönster som du kan korrigera genom att lägga till fler träningsdata eller ändra befintliga träningsdata. Exempelvis kan en klassificerare för äpplen kontra byggkalk felaktigt märka alla grön äpplen som byggkalk. Du kan korrigera det här problemet genom att lägga till och ange träningsdata som innehåller taggade bilder av grön äpplen.

## <a name="next-steps"></a>Nästa steg

Du lärt dig flera olika metoder för att göra det mer exakta din modell för klassificering av anpassad avbildning i den här guiden. Därefter lär du dig hur du testavbildningar programmässigt genom att skicka dem till förutsägelse-API.

> [!div class="nextstepaction"]
> [Använd förutsägelse-API](use-prediction-api.md)
