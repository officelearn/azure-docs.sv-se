---
title: Så här skapar du en icke-engelska knowledge base - frågor och svar om Maker - Azure kognitiva Services | Microsoft Docs
description: Så här skapar du en icke-engelska knowledge base.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 3fbd590229044af0daa60968fd8d556d539a58c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354051"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Språkstöd för innehåll från knowledge base för frågor och svar om Maker
Frågor och svar om Maker stöder innehåll från knowledge base på många olika språk. Varje frågor och svar om Maker tjänst bör dock reserveras för ett språk. Första kunskapsbasen skapade inriktning på en viss tjänst för frågor och svar om Maker ställer in språket för tjänsten. Se [här](../Overview/languages-supported.md) lista över språk som stöds.

Språket som identifieras automatiskt från innehållet i de datakällor som extraheras. När du skapar en ny frågor och svar om Maker tjänst och en ny kunskapsbas i tjänsten kan kontrollera du att språket som har ställts in korrekt.

1. Navigera till den [Azure-portalen](https://portal.azure.com/).

2. Välj **resursgrupper** och gå till resursgruppen där tjänsten frågor och svar om Maker är distribuerade och väljer den **Azure Search** resurs.

    ![Välj Azure Search-resurs](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Välj den **testkb** index. Den här Azure Search-index är alltid den första skapas och innehåller sparade innehållet knowledge grunderna i tjänsten. 

    ![Välja Test KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Välj **fält** avsnitt visar testkb information.

    ![Välj fält](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Markera kryssrutan för **Analyzer** att se information om språk.

    ![Välj Analyzer](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Du ska söka efter att Analyzer har angetts till ett visst språk. Det här språket identifieras automatiskt under steget Skapa knowledge base. Det här språket kan inte ändras när resursen har skapats.

    ![Valda Analyzer](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa frågor och svar om bot med Azure Bot Service](../Tutorials/create-qna-bot.md)
