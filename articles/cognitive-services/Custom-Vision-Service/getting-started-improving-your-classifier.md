---
title: Förbättra din klassificerare – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Lär dig att förbättra kvaliteten på din klassificerare.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: pafarley
ms.openlocfilehash: 2bee7f0af98bf03a13e376dea9dbf083b3f61815
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340298"
---
# <a name="how-to-improve-your-classifier"></a>Hur vi kan förbättra din klassificerare

Lär dig att förbättra kvaliteten på din klassificerare för Custom Vision Service. Kvaliteten på din klassificerare beror på mängden, kvaliteten och den märkta data som du anger till den och hur belastningsutjämnade datauppsättningen är olika. En bra klassificerare har vanligtvis en datauppsättning för belastningsutjämnade träning som är representativ för vad som ska skickas till klassificeraren. Processen för att skapa en sådan klassificerare är ofta iterativ. Det är vanligt att ta ett par Avrundar om utbildning för att nå önskat resultat.

Här är vanliga åtgärder som vidtagits för att förbättra en klassificerare. De här stegen är inte svårt och snabb regler, men heuristik som hjälper dig att bygga en bättre klassificerare.

1. Första round utbildning
1. Lägga till fler avbildningar och balansera data
1. Omträning
1. Lägga till avbildningar med olika bakgrund, belysning, Objektstorlek, kameravinkel och stil
1. Omtrimma & feed i avbildningen för förutsägelse
1. Granska förutsagda resultaten
1. Ändra befintlig utbildningsdata

## <a name="data-quantity-and-data-balance"></a>Data kvantitet och data saldo

Viktigaste är att ladda upp tillräckligt många bilder för att träna klassificeraren. Minst 50 bilder per etikett för träningsmängden rekommenderas som en startpunkt. Med färre avbildningar finns en risk för starkt att du overfitting. När dina prestanda-nummer kan föreslå god kvalitet, kan du behöva kämpa mot verkliga data. Utbildning klassificerare med fler avbildningar av kommer Allmänt öka noggrannheten för förutsägelser.

Ett annat övervägande är att bör du se till att dina data är balanserade. Exempelvis skapas har 500 avbildningar för en etikett och 50 avbildningar för en annan etikett en datauppsättning för imbalanced träning, orsakar att modellen ska bli mer rättvisande i att förutsäga en etikett än en annan. Du kommer förmodligen att se bättre resultat om du ha minst ett 1:2 förhållandet mellan etiketten med minst antal avbildningar och etiketten med de flesta bilder. Till exempel om etiketten med det högsta antalet bilder har 500 bilder, måste etiketten med minst avbildningar ha minst 250 avbildningar för utbildning.

## <a name="train-more-diverse-images"></a>Träna fler olika typer av bilder

Ger bilder som är representativ för vad som ska skickas till klassificeraren under normal användning. Till exempel om du tränar en ”äpple” klassificerare kanske din klassificerare inte som korrekt om du endast träna foton av äpplen i nivåer men gör förutsägelser på foton av äpplen på träd. Inklusive ett stort antal bilder ska se till att din klassificerare inte prioriterar och kan också generalisera. Nedan finns några sätt som du kan göra utbildning att ange mer diversifierad:

__Bakgrund:__ ger bilder för objektet framför olika bakgrunder (det vill säga frukt på lj jämfört med frukt i livsmedelskedja säck). Foton i kontexten är bättre än foton framför neutral bakgrunder eftersom de ger mer information om klassificeraren.

![Bild av BITS-exempel](./media/getting-started-improving-your-classifier/background.png)

__Belysning:__ ger bilder med olika belysning (det vill säga vidtas med flash, hög, etc.), särskilt om bilder som används för förutsägelse har olika belysning. Det är också bra att inkludera bilder med olika mättnad, hue och intensitet.

![Bild av belysning-exempel](./media/getting-started-improving-your-classifier/lighting.png)

__Objektstorlek:__ ange avbildningar som objekten är av olika storlek samla in olika delar av objektet. Till exempel knippen ett foto av bananer och en närbild av en enda bananer. Olika storlek hjälper klassificeraren generalisera bättre.

![Bild av storlek-exempel](./media/getting-started-improving-your-classifier/size.png)

__Kameravinkel:__ ger bilder med olika kameravinklar. Om alla dina foton utförs med en uppsättning fast kameror (till exempel övervakningskameror), kontrollera att du tilldela en annan etikett till varje kamera, även om de fångar in samma objekt för att undvika overfitting – modellering orelaterade objekt (till exempel lampposts) som kärnan.

![Bild av vinkel-exempel](./media/getting-started-improving-your-classifier/angle.png)

__Format:__ ger bilder av olika typer av samma klass (det vill säga olika typer av citrus). Men om du har bilder av objekt i drastiskt olika format (det vill säga Mickey mus jämfört med en verklig rat) rekommenderas att du märker dem som separata klasser som bättre representerar deras olika funktioner.

![Bild av formatmallar](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-images-submitted-for-prediction"></a>Använda avbildningar som skickats för förutsägelse

Custom Vision Service lagrar bilder som har skickats till slutpunkten för förutsägelse. Om du vill använda dessa avbildningar för att förbättra klassificeraren, använder du följande steg:

1. Om du vill visa bilder som skickas till klassificeraren, öppna den [Custom Vision-webbsida](https://customvision.ai), gå till ditt projekt och väljer den __förutsägelser__ fliken. Standardvyn visar avbildningar från den aktuella iterationen. Du kan använda den __Iteration__ nedrullningsbar listruta fält som du vill visa bilder som har skickats under föregående iterationer.

    ![Bild av fliken förutsägelser](./media/getting-started-improving-your-classifier/predictions.png)

2. Hovra över en bild att se vilka taggar som har uppskattad av klassificeraren. Bilder rangordnas så att de avbildningar som kan ge de flesta vinster klassificeraren är högst upp. Välj en annan sortering genom att använda den __sortera__ avsnittet. Om du vill lägga till en bild i dina befintliga utbildningsdata, Välj en avbildning med rätt taggen och välj klickar du på __spara och Stäng__. Avbildningen tas bort från __förutsägelser__ och läggs till i inlärningsbilder. Du kan visa den genom att välja den __Inlärningsbilder__ fliken.

    ![Bild av sidan taggning](./media/getting-started-improving-your-classifier/tag.png)

3. Använd den __träna__ knappen för att träna om klassificeraren.

## <a name="visually-inspect-predictions"></a>Visuellt inspektera förutsägelser

Om du vill kontrollera avbildningen förutsägelser, Välj den __Inlärningsbilder__ fliken och välj sedan __Iteration historik__. Avbildningar som markeras med en röd ruta förutse var felaktigt.

![Bild av iteration historiken](./media/getting-started-improving-your-classifier/iteration.png)

Ibland Visuell granskning kan identifiera mönster som du kan korrigera genom att lägga till ytterligare data eller ändra befintliga träningsdata. Exempelvis kan en klassificerare för apple jämfört med grön felaktigt märka alla grön äpplen som byggkalk. Du kanske kan åtgärda problemet genom att lägga till och tillhandahåller utbildningsdata för som innehåller taggade bilder av grön äpplen.

## <a name="unexpected-classification"></a>Oväntat klassificering

Ibland inlärning klassificeraren felaktigt egenskaper som är gemensamma för dina avbildningar. Till exempel om du skapar en klassificerare för äpplen kontra citrus och är angivna bilderna av äpplen i händer och citrus i vit nivåer, kan klassificeraren träna för händer jämfört med vit nivåer i stället för äpplen kontra citrus.

![Bild av oväntat klassificering](./media/getting-started-improving-your-classifier/unexpected.png)

Om du vill åtgärda det här problemet använder ovan riktlinjerna på utbildning med mer varierade bilder: ger bilder med olika vinklar, bakgrunder, Objektstorlek, grupper och andra varianter.

## <a name="negative-image-handling"></a>Negativt bildhantering

Custom Vision Service har stöd för vissa automatiska negativt bildhantering. I fall där du skapar en delvis kontra bananer klassificerare och skicka in en avbildning av en sko för förutsägelse bör klassificeraren score avbildningen som nära 0 procent för delvis såväl bananer.

Å andra sidan i fall där negativt bilderna är bara en variant av avbildningarna som används i utbildning, är det troligt att modellen klassificera negativt avbildningar som en klass som är märkta på grund av bra likheterna. Om du har en orange kontra grapefrukter klassificerare och du flöde i en avbildning av en clementine, kan det exempelvis bedöma clementine som orange. Detta kan inträffa eftersom många funktioner i clementine (färg, forma, struktur, naturliga habitat o.s.v.) liknar som apelsiner.  Om dina negativt avbildningar är av den här typen, rekommenderar vi att du skapar ett eller flera mellan taggarna (”andra”) och märka negativt bilder med den här taggen under utbildning för att tillåta modellen bättre skilja mellan dessa klasser.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du kan testa avbildningar via programmering genom att skicka dem till förutsägelse-API.

> [!div class="nextstepaction"]
[Använd förutsägelse-API](use-prediction-api.md)
