---
title: Ansvarig Machine Learning (ML)
titleSuffix: Azure Machine Learning
description: Lär dig vad som är ansvarig ML och hur du använder det i Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/08/2020
ms.openlocfilehash: 17cbf4f7b0ae31b0523a0fbea0c1cae8f83b234b
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "84982602"
---
# <a name="responsible-machine-learning-ml"></a>Ansvarig Machine Learning (ML)

I den här artikeln får du lära dig vad ansvarig ML är och hur du kan lägga det i praxis med Azure Machine Learning.

I utvecklingen och användningen av AI-system måste förtroendet ligga på kärnan. Förtroende i plattform, process och modeller. På Microsoft omfattar den ansvariga ML följande värden och principer:

- Förstå Machine Learning-modeller
  - Tolka och förklara modell beteende
  - Utvärdera och minimera modellens marknadsmässighet
- Skydda personer och deras data
  - Förhindra data exponering med differentiell sekretess  
- Styr processen från slut punkt till slut punkt för Machine Learning
  - Dokumentera livs cykeln för Machine Learning med datablad

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Ansvariga ML-pelare":::

Eftersom artificiell intelligens och autonoma system integrerar mer i samhället i samhället är det viktigt att proaktivt göra en satsning på att förutse och minimera oönskade konsekvenser av dessa tekniker.

## <a name="interpret-and-explain-model-behavior"></a>Tolka och förklara modell beteende

Svårt att förklara eller svarta system kan vara problematiska eftersom det gör det svårt för intressenter som systemutvecklare, tillsynsmyndigheter, användare och affärs besluts fattare att förstå varför system gör vissa beslut. Vissa AI-system är mer förklarare än andra och det finns ibland en kompromiss mellan ett system med högre precision och ett som är mer förklarat.

Om du vill skapa tolknings bara AI-system använder du [InterpretML](https://github.com/interpretml/interpret), ett paket med öppen källkod som skapats av Microsoft. [InterpretML kan användas i Azure Machine Learning](how-to-machine-learning-interpretability.md) för att [tolka och förklara dina Machine Learning-modeller](how-to-machine-learning-interpretability-aml.md), inklusive [automatiserade maskin inlärnings modeller](how-to-machine-learning-interpretability-automl.md).

## <a name="assess-and-mitigate-model-unfairness"></a>Utvärdera och minimera modellens marknadsmässighet

Eftersom AI-system blir mer inblandade i den dagliga besluts fattandet av samhället är det mycket viktigt att dessa system fungerar bra med att tillhandahålla verkliga resultat för alla.

Att AI-system är rättvist kan resultera i följande oönskade konsekvenser:

- Käll möjligheter, resurser eller information från individer.
- Förstärka bias och stereotyper.

Många aspekter av skälighet kan inte fångas eller representeras av mått. Det finns verktyg och metoder som kan förbättra skälighet i utformningen och utvecklingen av AI-system.

Det finns två viktiga steg för att minska den verkliga risken i AI-system för utvärdering och skydd. Vi rekommenderar [FairLearn](https://github.com/fairlearn/fairlearn), ett paket med öppen källkod som kan bedöma och minimera risken för AI-system. Läs mer om skälighet och FairLearn-paketet i [artikeln skälighet i ml](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Förhindra data exponering med differentiell sekretess

När data används för analys är det viktigt att data förblir privata och konfidentiella under hela användningen. Differentiell sekretess är en uppsättning system och metoder som hjälper till att skydda enskilda personers och privata data.

I traditionella scenarier lagras rå data i filer och databaser. När användarna analyserar data använder de vanligt vis rå data. Detta är ett problem eftersom det kan orsaka intrång i en individs integritet. Differentiell sekretess försöker åtgärda problemet genom att lägga till "brus" eller slumpmässig het för data så att användarna inte kan identifiera enskilda data punkter.

Att implementera Differentiellt privat system är svårt. [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) är ett projekt med öppen källkod som innehåller olika komponenter för att skapa globala, Differentiellt privata system. Mer information om differentiell sekretess och WhiteNoise-projektet finns i avsnittet [bevara data sekretess med hjälp av differentiella sekretess-och WhiteNoise](./concept-differential-privacy.md) -artiklar.

> [!NOTE]
> Observera att vi byter namn på Toolkit och kommer att introducera det nya namnet under de kommande veckorna. 

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Dokumentera livs cykeln för Machine Learning med datablad

Det är viktigt att dokumentera rätt information i Machine Learning-processen för att fatta ansvariga beslut i varje steg. Data blad är ett sätt att dokumentera maskin inlärnings resurser som används och skapas som en del av Machine Learning-livscykeln.

Modeller brukar betraktas som svarta rutor och det finns ofta lite information om dem. Eftersom Machine Learning-system blir mer genomgripande och används för besluts fattande, är det ett steg att utveckla fler ansvariga Machine Learning-system.

Viss modell information som du kanske vill dokumentera som en del av ett datablad:

- Avsedd användning
- Modell arkitektur
- Tränings data som används
- Använda utvärderings data
- Prestanda mått för tränings modell
- Skälighet-information.

I följande exempel kan du lära dig hur du använder Azure Machine Learning SDK för att implementera [data blad för modeller](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb).

## <a name="additional-resources"></a>Ytterligare resurser

- Använd homomorphic-kryptering för att [distribuera en krypterad inferencing-webbtjänst](how-to-homomorphic-encryption-seal.md).
- Lär dig [mer om de här rikt](https://www.partnershiponai.org/about-ml/) linjerna för Machine Learning-system.