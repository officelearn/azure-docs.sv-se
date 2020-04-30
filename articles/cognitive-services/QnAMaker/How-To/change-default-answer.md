---
title: Hämta standard svar – QnA Maker
description: Standard svaret returneras när det inte finns någon matchning till frågan. Du kanske vill ändra standard svaret från standard-standardsvaret.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097106"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Ändra standard svar för en QnA Maker resurs

Standard svaret returneras när det inte finns någon matchning till frågan. Du kanske vill ändra standard svaret från standard-standardsvaret.

## <a name="change-default-answer-in-the-azure-portal"></a>Ändra standard svar i Azure Portal

1. Gå till [Azure Portal](https://portal.azure.com) och navigera till resurs gruppen som representerar den QNA Maker tjänst som du har skapat.

2. Klicka för att öppna **App Service**.

    ![I Azure Portal, Access app service för QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klicka på **program inställningar** och redigera fältet **DefaultAnswer** till önskat standard svar. Klicka på **Spara**.

    ![Välj program inställningar och redigera sedan DefaultAnswer för QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Starta om App Service

    ![När du har ändrat DefaultAnswer startar du om QnA Maker AppService](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa en kunskapsbas](../How-to/manage-knowledge-bases.md)