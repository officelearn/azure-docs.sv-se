---
title: Hantering av resursen och nyckeln – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker-tjänsten innehåller två typer av nycklar, prenumeration och slutpunkten nycklar.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 11/26/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 3b75c2f0ec24fd58527643c8ccfec35f8cdd5914
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871905"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Så här hanterar du nycklar i QnA Maker

QnA Maker-tjänsten innehåller två typer av nycklar, **prenumerationsnycklar** och **endpoint nycklar**.

![Nyckelhantering](../media/qnamaker-how-to-key-management/key-management.png)

1. **Prenumerationsnycklar**: De här nycklarna används för åtkomst till den [hanteringstjänsten för QnA Maker API: er](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Dessa API: er kan du genomföra redigera kunskapsbasen.  

2. **Slutpunkten nycklar**: Dessa nycklar används för åtkomst till knowledge base-slutpunkten för att få ett svar för en användare-fråga. Normalt använder du den här slutpunkten i chattrobot eller klientkod för program som förbrukar QnA Maker-tjänsten.
 
## <a name="subscription-keys"></a>Prenumerationsnycklar
Du kan visa och återställa dina prenumerationsnycklar från Azure-portalen där du skapade QnA Maker-resursen. 
1. Gå till QnA Maker-resurs i Azure-portalen.

    ![QnA Maker resurslistan](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Gå till **nycklar**.

    ![Prenumerationsnyckel](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Slutpunkt-nycklar

Slutpunkt-nycklar kan hanteras från den [QnA Maker portal](https://qnamaker.ai).

1. Logga in på den [QnA Maker portal](https://qnamaker.ai), gå till din profil och klicka sedan på **tjänstinställningar**.

    ![Slutpunktsnyckel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visa eller återställa dina nycklar.

    ![viktiga endpoint-hanteraren](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Uppdatera dina nycklar om du tycker att de har komprometterats. Du kan behöva motsvarande ändringar i ditt klientprogram eller bot-kod.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas på ett annat språk](./language-knowledge-base.md)
