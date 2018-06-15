---
title: Så här skapar du en kognitiva knowledge base - frågor och svar om Maker - tjänster | Microsoft Docs
description: Så här skapar du en kunskapsbas
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355937"
---
# <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

Frågor och svar om Maker gör det enkelt att publicera dina befintliga datakällor för att skapa en knowledge base. Du kan skapa en ny frågor och svar om Maker knowledge base från vanliga frågor och svar sidor, produkter handböcker, strukturerade dokument eller lägga till dem redigeringsmässigt.

## <a name="steps"></a>Steg

1. Om du vill komma igång, logga in på att den [frågor och svar om Maker portal](https://qnamaker.ai) med dina autentiseringsuppgifter för azure och klicka på **Skapa ny tjänst**.

    ![Skapa KB ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Om du inte redan har skapat en tjänst för frågor och svar om Maker väljer **skapar du en tjänst för frågor och svar om**. Annars väljer du en tjänst för frågor och svar om Maker från listrutorna i steg 2. Välj tjänsten frågor och svar om Maker som är värd för Knowledge Base.

    ![Ställ frågor och svar om tjänsten](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Ange följande information för att skapa knowledge base.

    ![Set-datakällor](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Ge din tjänst en **namn.** Dubblettnamn som stöds och specialtecken stöds också.
    - Klistra in i URL: er som extraheras från. Visa mer information om vilka typer av datakällor som stöds [här](../Concepts/data-sources-supported.md).
    - Alternativt kan överföra filer från vilka data som extraheras. Finns det [prisinformation](https://aka.ms/qnamaker-pricing
) att se hur många dokument du kan lägga till.
    - Om du vill lägga till QnAs manuellt kan du hoppa över länka filer.

4. Välj **Skapa**.

    ![Skapa KB](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Det tar några minuter för data som ska extraheras.

    ![Extrahering](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Om kunskapsbasen har skapats, omdirigeras du till den **Knowledge base** sidan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera en kunskapsbas](../Tutorials/migrate-knowledge-base.md)
