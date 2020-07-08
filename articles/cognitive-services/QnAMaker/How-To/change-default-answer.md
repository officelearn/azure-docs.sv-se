---
title: Hämta standard svar – QnA Maker
description: Standard svaret returneras när det inte finns någon matchning till frågan. Du kanske vill ändra standard svaret från standard-standardsvaret.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979986"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Ändra standard svar för en QnA Maker resurs

Standard svaret för en kunskaps bas är tänkt att returneras när det inte går att hitta något svar. Om du använder ett klient program, till exempel [Azure bot service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), kan det också ha ett separat standard svar, vilket indikerar att inget svar uppfyllde Poäng tröskelvärdet.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>Ange standard svar när du skapar kunskaps basen

När du skapar en ny kunskaps bas är standard svars texten en av inställningarna. Om du väljer att inte ställa in den under skapande processen kan du ändra den senare med följande procedur.

## <a name="change-default-answer-in-qna-maker-portal"></a>Ändra standard svar i QnA Maker Portal

Standard svaret för kunskaps basen returneras när inget svar returneras från QnA Makers tjänsten.

1. Logga in på [QNA Maker Portal](https://www.qnamaker.ai/) och välj din kunskaps bas i listan.
1. Välj **Inställningar** i navigerings fältet.
1. Ändra värdet för **standard svars text** i avsnittet **Hantera kunskaps bas** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Skärm bild av QnA Maker Portal, sidan Inställningar, med text rutan standard svar markerad.":::

1. Välj **Spara och träna** för att spara ändringen.

## <a name="next-steps"></a>Nästa steg

* [Skapa en kunskapsbas](../How-to/manage-knowledge-bases.md)