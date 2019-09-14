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
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5e50c814fef24aa799549d055ad6496f5bdf05e0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961484"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Språk stöd för kunskaps bas innehåll för QnA Maker

QnA Maker stöder innehåll i kunskaps basen på många språk. Men varje QnA Maker tjänst bör vara reserverad för ett enda språk. Den första kunskaps bas som skapats som mål för en viss QnA Maker tjänst anger språket för den tjänsten. [Här](../Overview/languages-supported.md) finns en lista över vilka språk som stöds.

Språket identifieras automatiskt från innehållet i de data källor som extraheras. När du har skapat en ny QnA Maker tjänst och en ny kunskaps bas i tjänsten kan du kontrol lera att språket har angetts korrekt.

1. Navigera till [Azure-portalen](https://portal.azure.com/).

1. Välj **resurs grupper** och navigera till resurs gruppen där QNA Makers tjänsten har distribuerats och välj **Azure Search** resursen.

    ![Välj Azure Search resurs](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Välj **index**och välj sedan **testkb** -indexet. Det här är det första Azure Searchs indexet som skapas och innehåller det sparade innehållet i alla kunskaps banker i tjänsten. 

1. Välj **fält** om du vill se fälten i indexet.

1. Kolumnen _Analyzer_ i `questions` fälten och `answer` har angetts till ett särskilt språk. Det här språket identifierades automatiskt under steget Skapa kunskaps bas från de importerade filerna och URL: erna. Det här språket kan inte ändras när resursen har skapats.

    ![Markerad analys](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en QnA-robot med Azure Bot Service](../Tutorials/create-qna-bot.md)
