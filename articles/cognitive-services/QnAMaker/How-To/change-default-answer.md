---
title: Få standardsvar - QnA Maker
description: Standardsvaret returneras när det inte finns någon matchning till frågan. Du kanske vill ändra standardsvaret från standardsvaret.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843283"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Ange standardsvar för en kunskapsbas

Standardsvaret returneras när det inte finns någon matchning till frågan. Du kanske vill ändra standardsvaret från standardsvaret.

## <a name="change-default-answer"></a>Ändra standardsvar

1. Gå till [Azure-portalen](https://portal.azure.com) och navigera till resursgruppen som representerar QnA Maker-tjänsten som du skapade.

2. Öppna **Apptjänsten**genom att klicka här .

    ![I Azure-portalen får du åtkomst till apptjänsten för QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klicka på **Programinställningar och** redigera **fältet DefaultAnswer** till önskat standardsvar. Klicka på **Spara**.

    ![Välj Programinställningar och redigera sedan DefaultAnswer för QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Starta om apptjänsten

    ![När du har ändrat DefaultAnswer startar du om QnA Maker-apptjänsten](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa en robot med QnA Maker och LUIS](../tutorials/integrate-qnamaker-luis.md)