---
title: Visa systemtestresultat och distribution - Custom Translator
titleSuffix: Azure Cognitive Services
description: När din träning är framgångsrik, granska systemtester för att analysera dina träningsresultat. Om du är nöjd med utbildningsresultaten placerar du en distributionsbegäran för den tränade modellen.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f5e1a21f24fdd2458d4803d6ed1675455fa5037d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595610"
---
# <a name="view-system-test-results"></a>Visa testresultat för system

När din träning är framgångsrik, granska systemtester för att analysera dina träningsresultat. Om du är nöjd med utbildningsresultaten placerar du en distributionsbegäran för den tränade modellen.

## <a name="system-test-results-page"></a>Sidan Resultatsida för systemtest

Välj ett projekt, välj sedan fliken Modeller för det projektet, leta reda på den modell du vill använda och slutligen välja testfliken.

På fliken Test visas:

1.  **Systemtestresultat:** Resultatet av testprocessen i träningarna. Testprocessen ger BLEU-poängen.

    **Antal meningar:** Hur många parallella meningar användes i testuppsättningen.

     **BLEU Betyg:** BLEU-poäng som genereras för en modell efter avslutad utbildning.

    **Status:** Anger om testprocessen är klar eller pågår.

    ![Systemtestresultat](media/how-to/how-to-system-test-results.png)

2.  Klicka på systemtestresultaten, så tar du dig till sidan med resultatinformation. På den här sidan visas maskinöversättning av meningar som ingick i testdatauppsättningen.

3.  Tabellen på sidan testresultatinformation har två kolumner - en för varje språk i paret. Kolumnen för källspråket visar meningen som ska översättas. Kolumnen för målspråket innehåller två meningar på varje rad.

    **Referens:** Denna mening är referensöversättningen av käll mening som anges i testet datauppsättningen.

    **MT:** Denna mening är automatisk översättning av käll meningen görs av den modell som byggdes efter utbildningen genomfördes.

    ![Systemtestresultat jämför](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Ladda ner test

Klicka på länken Hämta översättningar för att hämta en zip-fil. Dragkedjan innehåller maskinöversättningar av källmeningar i testdatauppsättningen.

![Ladda ner test](media/how-to/how-to-system-test-download.png)

Detta nedladdade zip-arkiv innehåller tre filer.

1.  **custom.mt.txt:** Den här filen innehåller maskinöversättningar av källspråkmeningar på målspråket som görs av modellen som tränas med användarens data.

2.  **ref.txt:** Den här filen innehåller användaröversättningar av källspråkmeningar på målspråket.

3.  **source.txt:** Den här filen innehåller meningar på källspråket.

    ![Nedladdade systemtestresultat](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Distribuera en modell

Så här begär du en distribution:

1.  Välj ett projekt, gå till fliken Modeller.

2. För en modell som har tränats visas "Deploy"-knappen, om den inte har distribuerats.

    ![Distribuera modell](media/how-to/how-to-deploy-model.png)

3.  Klicka på Distribuera.
4.  Välj **Distribuerad** för de regioner där du vill att modellen ska distribueras och klicka på Spara. Du kan välja **Distribuerad** för flera regioner.

    ![Distribuera modell](media/how-to/how-to-deploy-model-regions.png)

5.  Du kan visa modellens status i kolumnen "Status".

>[!Note]
>Custom Translator stöder 10 distribuerade modeller inom en arbetsyta när som helst.

## <a name="update-deployment-settings"></a>Uppdatera distributionsinställningar

Så här uppdaterar du distributionsinställningarna:

1.  Välj ett projekt och gå till fliken **Modeller.**

2. För en modell som har distribuerats visas en **uppdateringsknapp.**

    ![Distribuera modell](media/how-to/how-to-update-undeploy-model.png)

3.  Välj **Uppdatera**.
4.  Välj **Distribuerad** eller **Ej distribuerad** för den region(er) där du vill att modellen ska distribueras eller inte distribueras och klicka sedan på **Spara**.

    ![Distribuera modell](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Om du väljer **Ej distribuerad** för alla regioner är modellen odedelad från alla regioner och hamnar i ett odestrikt tillstånd. Den är nu inte tillgänglig för användning.

## <a name="next-steps"></a>Nästa steg

- Börja använda din distribuerade anpassade översättningsmodell via [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Läs om hur du [hanterar inställningar](how-to-manage-settings.md) för att dela arbetsytan och hantera prenumerationsnyckeln.
- Lär dig hur du [migrerar arbetsytan och projektet](how-to-migrate.md) från [Microsoft Translator Hub](https://hub.microsofttranslator.com)
