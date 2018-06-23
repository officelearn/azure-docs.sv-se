---
title: Förbättra dina klassificerare med hjälp av anpassade Vision Service - kognitiva Azure-tjänster | Microsoft Docs
description: Lär dig att förbättra kvaliteten på anpassad Vision Service-klassificerare.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352872"
---
# <a name="how-to-improve-your-classifier"></a>Hur vi kan förbättra din klassificerare

Lär dig att förbättra kvaliteten på anpassad Vision Service-klassificerare. Kvaliteten på din klassificerare är beroende av kvaliteten på märkta data som du anger till den. 

## <a name="train-more-varied-images"></a>Träna fler olika bilder

Ger taggade bilder med olika vinklar, bakgrund, storlek på objekt, förbättrar grupper av foton och andra varianter klassificeraren. Foton i kontexten är bättre än foton framför neutral bakgrund. Inkludera bilder som är representativ för vad som ska skickas till klassificeraren vid normal användning.

Mer information om att lägga till avbildningar finns i [bygga en klassificerare](getting-started-build-a-classifier.md) dokumentet.

> [!IMPORTANT]
> Kom ihåg att träna klassificeraren när du har lagt till bilder.

## <a name="use-images-submitted-for-prediction"></a>Använda bilder som skickats för en prognos

Anpassad Vision tjänsten lagrar bilder som skickats till slutpunkten för förutsägelse. Om du vill använda dessa avbildningar för att förbättra klassificeraren, använder du följande steg:

1. Om du vill visa bilder som skickats till klassificeraren, öppna den [anpassad Vision webbsida](https://customvision.ai) och välj den __förutsägelser__ fliken.

    ![Bild av fliken förutsägelser](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > Standardvyn visar avbildningar från den aktuella iterationen. Du kan använda den __Iteration__ listrutan fält som du vill visa bilder som skickats under föregående iterationer.

2. Hovra över en bild för att se vilka taggar som har förutsade av klassificeraren.

    > [!TIP]
    > Bilder rangordnas så att de avbildningar som kan ge de flesta vinster klassificeraren visas överst. Markera en annan sortering med hjälp av __sortera__ avsnitt.

    Om du vill lägga till en bild utbildningsdata, Välj avbildningen, markera taggen och välj sedan __spara och Stäng__. Bilden tas bort från __förutsägelser__ och läggs till i utbildning-avbildningar. Du kan visa den genom att välja den __utbildning bilder__ fliken.

    ![Bild av sidan märkning](./media/getting-started-improving-your-classifier/tag-image.png)

3. Använd den __Train__ för att träna om klassificeraren.

## <a name="visually-inspect-predictions"></a>Visuellt inspektera förutsägelser

Om du vill granska bilden förutsägelser, Välj den __utbildning bilder__ och markera sedan __Iteration historik__. Bilder som beskrivs med en röd ruta förutsade var felaktigt.

![Bild av iteration historiken](./media/getting-started-improving-your-classifier/iteration-history.png)

Ibland visuell kontroll kan identifiera mönster som du sedan kan korrigera genom att lägga till ytterligare utbildningsdata. Exempelvis kan en klassificerare för rosor kontra daises felaktigt etikett alla vit rosor som daises. Du kanske kan lösa problemet genom att lägga till och träningsdata som innehåller taggade vit rosor-avbildningar.

## <a name="unexpected-classification"></a>Oväntat klassificering

Ibland klassificeraren lär sig egenskaper som är gemensamma för bilderna. Till exempel vill skapa en klassificerare för rosor kontra tulpaner passa. Du kan ange avbildningar på tulpaner passa i fälten och rosor i en röd vas framför en blå bakgrund. Givet dessa data, kan klassificerare träna för fältet kontra brandvägg- + vas i stället för rosor kontra tulpaner passa.

Åtgärda problemet genom att använda riktlinjerna för utbildning med flera olika bilder: ger bilder med olika vinklar, bakgrund, storlek på objekt, grupper och andra varianter.

## <a name="negative-image-handling"></a>Negativ bildhantering

Den anpassade Vision tjänsten stöder vissa automatisk negativ bild-hantering. Om du skapar en katt kontra hund klassificerare och du skickar en avbildning av en sko för förutsägelse, bör klassificerare score avbildningen som nära 0% för både katt och hund. 

> [!WARNING]
> Den automatiska metoden fungerar för tydligt negativa bilder. Det kanske inte fungerar bra i fall där negativa bilder är bara en variation av bilder som används i utbildning. 
>
> Om du har en husky kontra corgi klassificerare, och du mata in en avbildning av en Pomeranian, kan det exempelvis poängsätta Pomeranian som en Husky. Om bilderna negativt av den här typen kan skapa en ny tagg (till exempel ”andra”) och tillämpa det på bilderna negativt utbildning.

## <a name="next-steps"></a>Nästa steg

[Använd förutsägelser API](use-prediction-api.md)