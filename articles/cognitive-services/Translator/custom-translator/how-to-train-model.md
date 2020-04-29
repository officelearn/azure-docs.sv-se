---
title: Träna en modell – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Träna en modell är ett viktigt steg när du skapar en översättnings modell. Utbildning sker baserat på dokument som du väljer för den utbildningen.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "68595678"
---
# <a name="train-a-model"></a>Träna en modell

Träna en modell är det viktigaste steget för att skapa en översättnings modell, eftersom det inte går att skapa en modell utan någon utbildning. Utbildning sker baserat på dokument som du väljer för utbildningarna.

För att träna en modell:

1.  Välj det projekt där du vill bygga en modell.

2.  På fliken data för projektet visas alla relevanta dokument för projekt språk paret. Välj de dokument som du vill använda för att träna din modell manuellt. Du kan välja utbildnings-, justerings-och test dokument från den här skärmen. Du behöver också bara välja inlärnings uppsättningen och har anpassad översättare skapa justerings-och test uppsättningarna åt dig.

    -  Dokument namn: namnet på dokumentet.

    -  Koppling: om det här dokumentet är ett parallellt eller monolingual dokument. Monolingual-dokument stöds för närvarande inte för utbildning.

    -  Dokument typ: kan vara utbildning, justering, testning eller ord lista.

    -  Språk par: Detta visar käll-och mål språk för projektet.

    -  Käll meningar: visar antalet meningar som extraherats från käll filen.

    -  Mål meningar: visar antalet meningar som extraherats från målfilen.

    ![Träningsmodell](media/how-to/how-to-train-model.png)

3.  Klicka på knappen träna.

4.  Ange ett namn för din modell i dialog rutan.

5.  Klicka på träna modell.

    ![Dialog rutan träna modell](media/how-to/how-to-train-model-2.png)

6.  Anpassad översättare kommer att skicka utbildningen och Visa status för utbildningen på fliken modeller.

    ![Träna modell sida](media/how-to/how-to-train-model-3.png)

>[!Note]
>Anpassad översättare stöder 10 samtidiga utbildningar inom en arbets yta vid varje tidpunkt.


## <a name="edit-a-model"></a>Redigera en modell

Du kan redigera en modell med hjälp av länken Redigera på sidan modell information.

1.  Klicka på Penn ikonen.

    ![Redigera modellen](media/how-to/how-to-edit-model.png)

2.  I dialog rutan Ändra

    1.  Modell namn (obligatoriskt): ge din modell ett beskrivande namn.

        ![Redigera mer dialog](media/how-to/how-to-edit-model-dialog.png)

3.  Klicka på Spara.


## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du visar modell information](how-to-view-model-details.md).
