---
title: Hantering av resursen och nyckeln – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker-tjänsten innehåller två typer av nycklar, prenumeration och slutpunkten nycklar.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 9134819fc4610daadb617e123d861673e8cbfd32
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543455"
---
# <a name="key-management"></a>Nyckelhantering

QnA Maker-tjänsten innehåller två typer av nycklar, **prenumerationsnycklar** och **endpoint nycklar**.

![Nyckelhantering](../media/qnamaker-how-to-key-management/key-management.png)

1. **Prenumerationsnycklar**: de här nycklarna används för åtkomst till den [hanteringstjänsten för QnA Maker API: er](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Dessa API: er kan du utföra olika CRUD-åtgärder på kunskapsbasen.  

2. **Slutpunkten nycklar**: nycklarna används för åtkomst till knowledge base-slutpunkten för att få ett svar för en användare-fråga. Normalt använder du den här slutpunkten i chatten Robotapp/koden som förbrukar QnA Maker-tjänsten.
 
## <a name="subscription-keys"></a>Prenumerationsnycklar
Du kan visa och återställa dina prenumerationsnycklar från Azure-portalen där du skapade QnA Maker-resursen. 
1. Gå till QnA Maker-resurs i Azure-portalen.

    ![QnA Maker resurslistan](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Gå till **nycklar**.

    ![Prenumerationsnyckel](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Slutpunkt-nycklar

Slutpunkt-nycklar kan hanteras från den [QnA Maker portal](https://qnamaker.ai).

1. Logga in på den [QnA Maker portal](https://qnamaker.ai), och gå till **hantera nycklar**.

    ![Slutpunktsnyckel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visa eller återställa dina nycklar.

    ![viktiga endpoint-hanteraren](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Uppdatera dina nycklar om du tycker att de har komprometterats. Du kan behöva motsvarande ändringar i koden App-robot.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas på ett annat språk](./language-knowledge-base.md)
