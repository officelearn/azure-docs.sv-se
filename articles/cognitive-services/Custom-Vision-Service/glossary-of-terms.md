---
title: Ordlista – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Ordlista för Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/08/2017
ms.author: anroth
ms.openlocfilehash: 8d3962459bc173e2db19cfa4728e7eb683511eb3
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217164"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Ordlista för Custom Vision Service

Här följer några termer som används i Custom Vision Service och deras innebörd.

## <a name="classifier"></a>Klassificerare

En klassificerare är en modell som du skapar med Custom Vision Service med hjälp av några inlärningsbilder. När du är klar med utbildning för en ny klassificerare får du en utvärdering slutpunkt (HTTPS) som du kan lägga till din app. Varje klassificerare som du skapar är i sin egen projekt och du kan visa alla projekt när du har loggat in.

## <a name="domain"></a>Domän

När du skapar ett projekt kan välja du en ”domän” för projektet. Domänen optimerar en klassificerare för en viss typ av objekt i dina avbildningar. Till exempel, om ditt scenario är att klassificera mellan bilder av apple cirkel jämfört med avbildningar av man odlar Lammefjordsmorötter enkelt kan sedan välja ”mat”-domänen. Om du är osäker på vilken domän som du väljer, väljer du ”Generic” domän.

- **Mat-domän.** Optimerat för disk som du ser på en restaurang-meny. Det har inte optimerats för att identifiera enskilda frukt eller grönsaker. Om du vill klassificera fotografier av enskilda frukter och grönsaker använda den allmänna domänen för detta ändamål.
- **Landmärken-domän.** Optimerat för identifierbara landmärken, både naturliga och konstgjorda. Den här domänen fungerar bäst när landmärken syns tydligt i fotot, även om landmärken något hindras av en grupp människor som utgör framför den.
- **Retail-domän.** Optimerat för klassificera bilder i en i katalogen eller Shopping. Om du vill ha hög precision när klassificera klänningar, använder byxor, skjortor, osv, Retail-domänen.
- **Domänen som är olämpligt för barn.** Optimerad för att definiera en bättre mellan vuxet innehåll och inte är vuxen innehåll. Till exempel om du vill blockera bilder av personer i badning passar kan den här domänen du skapa en anpassad klassificerare för att göra detta.
- **Den allmänna domänen.** Utmärkt för många olika slags aktiviteter för klassificering av avbildning.

Modeller som genererats av **komprimera domäner** kan exporteras med exportfunktionen iteration. De är optimerade för begränsningar i realtid klassificeringen på mobila enheter. Klassificerare som skapats med en kompakt domän kan vara något mindre exakt en standard domän med samma mängd träningsdata. Nackdelen är att de är tillräckligt liten för att köras lokalt i nära realtid. 

## <a name="training-image"></a>Bild för utbildning

Om du vill skapa en klassificerare med hög precision måste Custom Vision Service flera inlärningsbilder. En avbildning av en utbildning är ett foto av den önskade Custom Vision Service att klassificera bilden. Om du vill klassificera apelsiner, skulle du behöva överföra flera bilder apelsiner till Custom Vision Service för att skapa en klassificerare som kan identifiera apelsiner-tjänsten. Vi rekommenderar minst 30 bilder per tagg.

## <a name="iteration"></a>Iteration

Varje gång du träna eller nytt lära din klassificerare kan du skapa en ny iteration av din modell. Vi lagrar flera senaste iterationer så att du kan jämföra förloppet över tid. Du kan ta bort eventuella iterationer som du inte längre vara användbara. Kom ihåg att ta bort en iteration är permanent och du också ta bort alla bilder eller ändringar som har unika för den iterationen. 

## <a name="workspace"></a>Arbetsyta

Din arbetsyta innehåller bilderna för utbildning och återspeglar alla ändringar från din senaste iterationen som har tagits bort eller lagt till avbildningar. När du lära din klassificerare, skapar du en ny iteration av din klassificerare med hjälp av avbildningar finns i din arbetsyta.

## <a name="tags"></a>Taggar

Använd taggar för att märka objekten i dina avbildningar för utbildning. Om du skapar en klassificerare för att identifiera hundar och ponnyer, placerar du en ”hund” tagg på avbildningar som innehåller hundar, en ”programmet pony” tagg på avbildningar som innehåller ponnyer, och både ”hund” och en ”programmet pony” tagg på avbildningar som innehåller både en hund och en programmet pony.

## <a name="evaluation"></a>Utvärdering

När du har tränats din klassificerare, kan du skicka en avbildning för utvärdering med hjälp av automatiskt genererade https-slutpunkten. Din klassificerare returnerar en uppsättning förväntade taggar i ordning tillförlitlighet.

## <a name="predictions"></a>Förutsägelser

Eftersom din klassificerare tar emot nya bilder för att klassificera, lagras avbildningarna. Du kan använda dessa avbildningar för att förbättra precisionen för din klassificerare genom att tagga korrekt argumentantal förväntade avbildningar. Du kan sedan använda de nya bilderna för att lära din klassificerare igen.

## <a name="precision"></a>Precision

När du klassificerar en avbildning, hur sannolikt är din klassificerare att klassificera bilden korrekt? Utanför alla bilder som används för att träna klassificerare (hundar och ponnyer), hur många procent modellen fick korrekt? 99 rätt taggar från 100 bilder ger en noggrannhet på 99%.

## <a name="recall"></a>Återkalla

Från alla avbildningar som bör har klassificerats korrekt, hur många din klassificerare identifieras korrekt? En träffsäkerhet på 100% betyder att om det finns 38 hund bilder i bilder som används för att träna klassificeraren, hittades 38 hundar av klassificeraren.

## <a name="settings"></a>Inställningar

Det finns två typer av inställningar, inställningar och användarnivå.

- Inställningar: 
  
  Inställningar gäller för ett projekt eller klassificerare. Dessa är:

   - Project-domain
   - Projektnamn
   - Projektbeskrivning
   - Syntax:
      - Antal inlärningsbilder
      - Antal taggar som har skapats
      - Antal upprepningar som har skapats

- Användarnivå inställningar: 
   - Prenumerationsnycklar: en för träning, en för utvärdering/förutsägelse.
   - Syntax:
      - Antalet projekt som har skapats
      - Antal utvärdering/förutsägelse-API-anrop.
