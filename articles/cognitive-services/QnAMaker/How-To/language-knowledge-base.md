---
title: Kunskaps bas som inte är på engelska – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker stöder innehåll i kunskaps basen på många språk. Men varje QnA Maker tjänst bör vara reserverad för ett enda språk. Den första kunskaps bas som skapats som mål för en viss QnA Maker tjänst anger språket för den tjänsten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 63eb13dd131fcc1c424c02fdac10f531cc9f0282
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876629"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Språk stöd för kunskaps bas innehåll för QnA Maker

QnA Maker stöder innehåll i kunskaps basen på många språk. Men varje QnA Maker tjänst bör vara reserverad för ett enda språk. Den första kunskaps bas som skapats som mål för en viss QnA Maker tjänst anger språket för den tjänsten. [Här](../Overview/languages-supported.md) finns en lista över vilka språk som stöds.

Språket identifieras automatiskt från innehållet i de data källor som extraheras. När du har skapat en ny QnA Maker tjänst och en ny kunskaps bas i tjänsten kan du kontrol lera att språket har angetts korrekt.

1. Navigera till [Azure-portalen](https://portal.azure.com/).

1. Välj **resurs grupper** och navigera till resurs gruppen där QNA Makers tjänsten har distribuerats och välj **Azure Search** resursen.

    ![Välj Azure Search resurs](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Välj **testkb** -indexet. Det här Azure Search indexet är alltid det första som skapats och det innehåller det sparade innehållet i alla kunskaps baser i den tjänsten. 

    ![Välj test KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

1. Avsnittet Välj **fält** som visar _testkb_ -information.

    ![Välj fält](../media/qnamaker-how-to-language-kb/selectfields.png)

1. Markera kryss rutan för **Analyzer** om du vill visa information om språket.

    ![Välj Analyzer](../media/qnamaker-how-to-language-kb/select-analyzer.png)

1. Du bör se att _analys_ verktyget är inställt på ett speciellt språk. Det här språket identifierades automatiskt under steget Skapa kunskaps bas från de importerade filerna och URL: erna. Det här språket kan inte ändras när resursen har skapats.

    ![Markerad analys](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en QnA-robot med Azure Bot Service](../Tutorials/create-qna-bot.md)
