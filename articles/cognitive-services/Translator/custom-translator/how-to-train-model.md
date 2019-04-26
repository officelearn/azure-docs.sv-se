---
title: Träna en modell – anpassad Translator
titleSuffix: Azure Cognitive Services
description: Träna en modell är ett viktigt steg när du skapar en modell för översättning. Utbildning sker baserat på dokument som du väljer för de utbildningar.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 2045041a9ee4872e4510e5ee0deba258b0342557
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60510318"
---
# <a name="train-a-model"></a>Träna en modell

Träna en modell är viktigt att skapa en översättningsmodellen eftersom utan ett utbildnings modellen inte kan skapas. Utbildning sker baserat på dokument som du väljer för utbildningar.

För att träna en modell:

1.  Välj det projekt där du vill skapa en modell.

2.  Data-fliken för projektet visas alla relevanta dokumenten för språk-par för projektet. Manuellt välja de dokument som du vill använda för att träna din modell. Du kan välja utbildning, justering och testningen dokument från den här skärmen. Även du bara väljer träningsmängden och har anpassade Translator skapa den justering och testa uppsättningar för dig.

    -  Dokumentnamn: Namnet på dokumentet.

    -  Länkning: Om det här dokumentet är ett parallella eller enspråkig dokument. Enspråkig dokument har för närvarande inte stöd för utbildning.

    -  Typ av standarddokument: Kan vara utbildning, justering, testning eller ordlista.

    -  Språk-par: Det här visas käll- och målspråk för projektet.

    -  Källa meningar: Visar antalet meningar som extraheras från källfilen.

    -  Target meningar: Visar antalet meningar som extraheras från målfilen.

    ![Träna modell](media/how-to/how-to-train-model.png)

3.  Klicka på knappen träna.

4.  I dialogrutan, ange ett namn för din modell.

5.  Klicka på Train-modell.

    ![Träna modellen dialogrutan](media/how-to/how-to-train-model-2.png)

6.  Anpassade Translator kommer skicka utbildningen och visa status för utbildningen på fliken modeller.

    ![Träna modellen sidan](media/how-to/how-to-train-model-3.png)

>[!Note]
>Anpassade Translator stöder 10 samtidiga utbildningar i en arbetsyta när som helst i tid.


## <a name="edit-a-model"></a>Redigera en modell

Du kan redigera en modell med hjälp av länken Redigera på sidan med modellen.

1.  Klicka på pennikonen.

    ![Redigera modell](media/how-to/how-to-edit-model.png)

2.  I dialogrutan-ändring

    1.  Modellnamn (krävs): Ge ett beskrivande namn för din modell.

        ![Mer dialogrutan Redigera](media/how-to/how-to-edit-model-dialog.png)

3.  Klicka på Spara.


## <a name="next-steps"></a>Nästa steg

- Lär dig [visa modelldetaljer](how-to-view-model-details.md).
