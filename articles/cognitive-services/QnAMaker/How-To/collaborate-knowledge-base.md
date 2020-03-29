---
title: Samarbete i kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker tillåter flera personer att samarbeta på en kunskapsbas. Den här funktionen är försedd med Azure-rollbaserad åtkomstkontroll.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75660757"
---
# <a name="collaborate-on-your-knowledge-base"></a>Samarbeta i din kunskapsbas

QnA Maker tillåter flera personer att samarbeta på alla kunskapsbaser i samma QnA Maker-resurs. Den här funktionen medföljer [Azure-rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Utför följande steg för att dela din QnA Maker-tjänst med någon:

1. Logga in på Azure-portalen och gå till din QnA Maker-resurs.

    ![Resurslista för QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Gå till fliken **Åtkomstkontroll (IAM).**

    ![QnA-tillverkare IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Välj **Lägg till**.

    ![QnA Maker IAM lägga till](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Välj rollen **Ägare** eller **Deltagare.** Du kan inte bevilja skrivskyddad åtkomst via rollbaserad åtkomstkontroll. Ägar- och deltagarroller har läs-skrivbehörighet till QnA Maker-tjänsten.

    ![QnA Maker IAM lägga till roll](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Ange användarens e-postadress och tryck på **Spara**.

    ![QnA Maker IAM lägga till e-post](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

När personen, du delade din QnA Maker-tjänst med, loggar in på [QnA Maker-portalen](https://qnamaker.ai) kan de se alla kunskapsbaser i den tjänsten.

Kom ihåg att du inte kan dela en viss kunskapsbas i en QnA Maker-tjänst. Om du vill ha mer detaljerad åtkomstkontroll kan du överväga att distribuera dina kunskapsbaser över olika QnA Maker-tjänster.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Testa en kunskapsbas](./test-knowledge-base.md)
