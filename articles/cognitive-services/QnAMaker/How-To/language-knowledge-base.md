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
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 26792246267ced6d9fff50fe4fea11cc8d280d6a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966674"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Språk stöd för kunskaps bas innehåll för QnA Maker
QnA Maker stöder innehåll i kunskaps basen på många språk. Men varje QnA Maker tjänst bör vara reserverad för ett enda språk. Den första kunskaps bas som skapats som mål för en viss QnA Maker tjänst anger språket för den tjänsten. [Här](../Overview/languages-supported.md) finns en lista över vilka språk som stöds.

Språket identifieras automatiskt från innehållet i de data källor som extraheras. När du har skapat en ny QnA Maker tjänst och en ny kunskaps bas i tjänsten kan du kontrol lera att språket har angetts korrekt.

1. Navigera till [Azure Portal](https://portal.azure.com/).

2. Välj **resurs grupper** och navigera till resurs gruppen där QNA Makers tjänsten har distribuerats och välj **Azure Search** resursen.

    ![Välj Azure Search resurs](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Välj **testkb** -indexet. Det här Azure Search indexet är alltid det första som skapats och det innehåller det sparade innehållet i alla kunskaps baser i den tjänsten. 

    ![Välj test KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Avsnittet Välj **fält** som visar testkb-information.

    ![Välj fält](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Markera kryss rutan för **Analyzer** om du vill visa information om språket.

    ![Välj Analyzer](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Du bör se att analys verktyget är inställt på ett visst språk. Det här språket identifierades automatiskt under skapandet av kunskaps basen. Det här språket kan inte ändras när resursen har skapats.

    ![Markerad analys](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en QnA-robot med Azure Bot Service](../Tutorials/create-qna-bot.md)
