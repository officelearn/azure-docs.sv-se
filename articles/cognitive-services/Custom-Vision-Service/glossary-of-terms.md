---
title: Ordlista för anpassad Vision Service - kognitiva Azure-tjänster | Microsoft Docs
description: Ordlista för anpassade Vision-tjänsten.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/08/2017
ms.author: anroth
ms.openlocfilehash: 871617ce3c1c5a84df746c0c7d87c113b3a6f354
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352887"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Ordlista för anpassad Vision tjänsten

Följande är vissa termer som används i anpassade Vision Service och deras innebörd.

## <a name="classifier"></a>Klassificerare

En klassificerare är en modell som du skapar med anpassade Vision tjänsten med hjälp av några utbildning bilder. När du är klar tränar en ny klassificerare, får du en utvärdering slutpunkt (HTTPS) som du kan lägga till din app. Varje klassificerare som du skapar är i sin egen projektet och du kan visa alla projekt när du har loggat in.

## <a name="domain"></a>Domän

När du skapar ett projekt kan välja du en ”domän” för projektet. Domänen optimerar en klassificerare för en viss typ av objekt i dina bilder. Till exempel om ditt scenario är att klassificera mellan avbildningar av apple cirkel jämfört med avbildningar av man odlar Lammefjordsmorötter enkelt, väljer du ”mat”-domän. Om du är osäker på vilken domän som du väljer, väljer du ”Allmänt” domän.

- **Mat-domän.** Optimerad för disk på en restaurang meny visas. Det har inte optimerats för att identifiera enskilda frukter eller grönsaker. Om du vill klassificera fotografier av enskilda frukter eller grönsaker kan du använda den allmänna domänen för detta ändamål.
- **Landmärke-domän.** Optimerad för att identifiera landmärken, både fysiska och artificiella. Den här domänen fungerar bäst när Landmärke tydligt visas i foto, även om Landmärke något hindras genom en grupp människor som utgör framför det.
- **Retail-domän.** Optimerad för att klassificera bilder i en i katalogen eller Shopping. Om du vill hög precision när klassificera klänningar, byxor, skjortor, etc., använder du Retail-domänen.
- **Vuxna domänen.** Optimerad för att definiera en bättre mellan vuxet innehåll och icke-vuxen innehåll. Till exempel om du vill blockera avbildningar av personer i badning passar kan den här domänen du skapa en anpassad klassificerare för att göra det.
- **Den allmänna domänen.** Utmärkt för ett stort antal uppgifter för klassificering av avbildningen.

Modeller som genererats av **komprimera domäner** kan exporteras med exportfunktionen iteration. De är optimerade för begränsningar i realtid klassificering på mobila enheter. Klassificerare som skapats med en compact domän kan vara något mindre exakt en standard domän med samma grad av utbildningsdata. Nackdelen är att de är tillräckligt små för att köras lokalt i nära realtid. 

## <a name="training-image"></a>Bild av utbildning

Om du vill skapa en hög precision klassificerare måste anpassad Vision tjänsten flera utbildning bilder. En avbildning av utbildning är ett foto av bilden som du vill anpassa Vision tjänsten att klassificera. Om du vill klassificera orange, skulle du behöva överföra flera avbildningar av orange till anpassad Vision tjänsten så att tjänsten kan skapa en klassificerare som kan identifiera orange. Vi rekommenderar minst 30 bilder per tagg.

## <a name="iteration"></a>Upprepning

Varje gång du Train eller nytt träna din klassificerare, du skapar en ny iteration av modellen. Vi behåller flera senaste iterationer så att du kan jämföra din status över tid. Du kan ta bort eventuella iterationer som du inte längre vara användbart. Kom ihåg att ta bort en iteration är permanent och du även ta bort alla avbildningar och ändringar som är unika för den iterationen. 

## <a name="workspace"></a>Arbetsyta

Arbetsytan innehåller bilderna för utbildning och den visar alla ändringar från din senaste iteration som tagits bort eller lägga till bilder. När du träna din klassificerare skapar du en ny iteration av din klassificerare med hjälp av avbildningar finns i din arbetsyta.

## <a name="tags"></a>Taggar

Använd taggar för att namnge objekten i din utbildning bilder. Om du skapar en klassificerare för att identifiera hund och ponnyer, skulle du spärra taggen ”hund” avbildningar som innehåller hund, taggen ”ponny” på avbildningar som innehåller ponnyer, och både ”hund” och ”ponny”-tagg på avbildningar som innehåller både en hund och en ponny.

## <a name="evaluation"></a>Utvärdering

När du har tränat din klassificerare, kan du skicka en bild för utvärdering via automatiskt genererade https-slutpunkten. Din klassificerare returnerar en mängd med förväntade taggar i ordning förtroende.

## <a name="predictions"></a>Förutsägelser

Eftersom din klassificerare tar emot nya bilder att klassificera, lagras bilderna. Du kan använda dessa avbildningar för att förbättra precisionen för din klassificerare genom att tagga felaktigt beräknade bilder på rätt sätt. Du kan sedan använda dessa nya avbildningar för att träna din klassificerare igen.

## <a name="precision"></a>Precision

När du klassificerar en avbildning, hur troligt är din klassificerare korrekt klassificera bilden? Slut på alla bilder används för att träna klassificeraren (hund och ponnyer), hur många procent modellen fick korrekt? 99 rätt taggar utanför 100 bilder ger en Precision 99%.

## <a name="recall"></a>Återkalla

Slut på alla bilder som bör klassificeras på rätt sätt, hur många din klassificerare identifieras korrekt? Återkalla 100% betyder att om det fanns 38 hund bilder i bilder som används för att träna klassificeraren, hittades 38 hund av klassificeraren.

## <a name="settings"></a>Inställningar

Det finns två typer av inställningar, inställningar och inställningar på användarnivå.

- Inställningar: 
  
  Inställningar som gäller för ett projekt eller klassificerare. De omfattar:

   - Projekt-domän
   - Projektnamn
   - Projektbeskrivning av
   - Användning:
      - Antalet avbildningar som utbildning
      - Antal taggar som har skapats
      - Antalet upprepningar som har skapats

- Användarnivå inställningar: 
   - Prenumerationen nycklar: en för träning, en för utvärdering/förutsägelse.
   - Användning:
      - Antalet projekt som har skapats
      - Antal utvärdering/förutsägelse API-anrop.
