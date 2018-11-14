---
title: Visa testresultaten för system och distribution – anpassad Translator
titleSuffix: Azure Cognitive Services
description: När utbildning har genomförts, granskar tester för att analysera dina resultat för utbildning. Om du är nöjd med resultaten utbildning kan du placera en distributionsbegäran för den tränade modellen.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 61d3869559d88e14c0b9a3c3e23cd8a1f9c2b6c4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627649"
---
# <a name="view-system-test-results"></a>Visa testresultat för system

När utbildning har genomförts, granskar tester för att analysera dina resultat för utbildning. Om du är nöjd med resultaten utbildning kan du placera en distributionsbegäran för den tränade modellen. 

## <a name="system-test-results-page"></a>Resultatsidan för system-test

Välj ett projekt och sedan välja fliken modeller i projektet, letar du upp den modell som du vill använda och slutligen välja fliken test.

Fliken test visar:

1.  Testresultat för system: Resultatet av test-processen i utbildningar. Testa processen genererar BLEU poängen.

    **Antal mening:** hur många parallella meningar har använts i test-uppsättningen.

     **BLEU poäng:** BLEU poäng genereras för en modell efter slutförande för utbildning.

    **Status:** anger om test-processen är klar eller pågår.

    ![Testresultat för system](media/how-to/how-to-system-test-results.png)

2.  Klicka på resultaten av System och som tar dig att testa resultatet informationssida. Den här sidan visar maskinöversättning i meningar som ingick i test-datauppsättning.

3.  Tabellen på informationssidan för test-resultatet har två kolumner – en för varje språk i paret. Kolumnen för en källspråket visar meningen översättas. Kolumnen för målspråket som innehåller två meningar i varje rad.

    **Ref:** den här meningen är referens översättningen av källa meningen som anges i test-datauppsättning.

    **MT:** den här meningen är automatisk översättning av källa meningen utförd av modellen som skapats efter utbildningen utfördes.

    ![Jämfört med system test resultat](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Ladda ned test

Klicka på länken ladda ned översättningar för att ladda ned en zip-fil. ZIP-filen innehåller maskinöversättningar av källa meningar i test-datauppsättningen.

![Ladda ned test](media/how-to/how-to-system-test-download.png)

Det här hämtade zip-arkivet innehåller tre filer.

1.  Custom.MT.txt: den här filen innehåller maskinöversättningar källspråk versal i målspråk utförd av modellen tränas med användarens data.

2.  Ref.txt: den här filen innehåller anges av användaren översättningar av källspråk meningar på språket som mål.

3.  Source.txt: den här filen innehåller meningar på språket som källa.

    ![Testresultat för hämtade system](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Distribuera en modell

Att begära en distribution:

1.  Välj ett projekt, går till fliken modeller.

2. För en har tränad modell, den visar knappen ”distribuera”, om inte distribuerats.

    ![Distribuera modell](media/how-to/how-to-deploy-model.png)

3.  Klicka på distribuera.
4.  Välj den region där du vill att din modell som ska distribueras och klicka på Spara. Du kan välja flera regioner.

    ![Distribuera modell](media/how-to/how-to-deploy-model-regions.png)

5.  Du kan visa statusen för din modell i kolumnen ”Status”.

>[!Note]
>Om en modell är redan distribuerat, visas en ”Undeploy”-knappen för den modellen. Klicka på ”Undeploy” om du vill ta bort en modell.

## <a name="next-steps"></a>Nästa steg

- Börja använda dina distribuerade anpassade översättningsmodellen via [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Lär dig [hur du hanterar inställningar](how-to-manage-settings.md) hantera prenumerationsnyckel för att dela din arbetsyta.
- Lär dig [hur du migrerar din arbetsyta och projekt](how-to-migrate.md) från [Microsoft Translator Hub](https://hub.microsofttranslator.com)