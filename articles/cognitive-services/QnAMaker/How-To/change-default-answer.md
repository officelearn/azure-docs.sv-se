---
title: Hämta standard svar – QnA Maker
description: Standard svaret returneras när det inte finns någon matchning till frågan. Du kanske vill ändra standard svaret från standard-standardsvaret.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843283"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Ange standard svar för en kunskaps bas

Standard svaret returneras när det inte finns någon matchning till frågan. Du kanske vill ändra standard svaret från standard-standardsvaret.

## <a name="change-default-answer"></a>Ändra Standardsvar

1. Gå till den [Azure-portalen](https://portal.azure.com) och navigera till den resursgrupp som representerar QnA Maker-tjänsten som du skapade.

2. Klicka för att öppna den **Apptjänst**.

    ![Få åtkomst till App service för QnA Maker i Azure-portalen](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klicka på **programinställningar** och redigera den **DefaultAnswer** till önskad Standardsvaret. Klicka på **Spara**.

    ![Välj programinställningar och sedan redigera DefaultAnswer för QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Starta om App service

    ![När du ändrar DefaultAnswer, startar du om appservice QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa en robot med QnA Maker och LUIS](../tutorials/integrate-qnamaker-luis.md)