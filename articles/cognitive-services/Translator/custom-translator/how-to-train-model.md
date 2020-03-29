---
title: Träna en modell - Custom Translator
titleSuffix: Azure Cognitive Services
description: Att träna en modell är ett viktigt steg när man bygger en översättningsmodell. Utbildning sker baserat på dokument som du väljer för den utbildningen.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595678"
---
# <a name="train-a-model"></a>Träna en modell

Utbildning av en modell är det viktiga steget för att bygga en översättningsmodell, för utan utbildning kan modellen inte byggas. Utbildning sker baserat på dokument som du väljer för utbildningarna.

Så här tränar du en modell:

1.  Välj det projekt där du vill skapa en modell.

2.  Fliken Data för projektet visar alla relevanta dokument för projektspråksparet. Välj manuellt de dokument som du vill använda för att träna din modell. Du kan välja tränings-, justerings- och testdokument från den här skärmen. Även du bara välja utbildning set och har Custom Translator skapa tuning och test uppsättningar för dig.

    -  Dokumentets namn: Dokumentets namn.

    -  Ihopparning: Om det här dokumentet är ett parallellt eller enspråkigt dokument. Enspråkiga dokument stöds för närvarande inte för utbildning.

    -  Dokumenttyp: Kan vara utbildning, justering, testning eller ordlista.

    -  Språkpar: Detta visar källan och målspråket för projektet.

    -  Källmeningar: Visar antalet meningar som extraherats från källfilen.

    -  Målmeningar: Visar antalet meningar som extraherats från målfilen.

    ![Träningsmodell](media/how-to/how-to-train-model.png)

3.  Klicka på Knappen Träna.

4.  Ange ett namn på modellen i dialogrutan.

5.  Klicka på Tågmodell.

    ![Dialogrutan Tågmodell](media/how-to/how-to-train-model-2.png)

6.  Custom Translator skickar in utbildningen och visar status för utbildningen på fliken Modeller.

    ![Sidan Tågmodell](media/how-to/how-to-train-model-3.png)

>[!Note]
>Custom Translator stöder 10 samtidiga utbildningar inom en arbetsyta när som helst.


## <a name="edit-a-model"></a>Redigera en modell

Du kan redigera en modell med länken Redigera på sidan Modellinformation.

1.  Klicka på ikonen Penna.

    ![Redigera modellen](media/how-to/how-to-edit-model.png)

2.  I dialogrutan ändras

    1.  Modellnamn (obligatoriskt): Ge din modell ett meningsfullt namn.

        ![Redigera mer dialog](media/how-to/how-to-edit-model-dialog.png)

3.  Klicka på Spara.


## <a name="next-steps"></a>Nästa steg

- Läs om hur du [visar modellinformation](how-to-view-model-details.md).
