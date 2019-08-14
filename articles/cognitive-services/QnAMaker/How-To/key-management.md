---
title: Hantering av resursen och nyckeln – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker-tjänsten innehåller två typer av nycklar, prenumeration och slutpunkten nycklar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0b82f37b76a6bca6d84a05bc48b7ae9986fb76b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967689"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Så här hanterar du nycklar i QnA Maker

QnA Maker-tjänsten innehåller två typer av nycklar, **prenumerationsnycklar** och **endpoint nycklar**.

![Nyckelhantering](../media/qnamaker-how-to-key-management/key-management.png)

1. **Prenumerations nycklar**: Dessa nycklar används för att få åtkomst till [API: erna för QNA Maker Management-tjänsten](https://go.microsoft.com/fwlink/?linkid=2092179). Dessa API: er gör att du kan redigera kunskaps basen.  

2. **Slut punkts nycklar**: Dessa nycklar används för att få åtkomst till kunskaps bas slut punkten för att få svar på en användar fråga. Du använder vanligt vis den här slut punkten i din chatt-robot eller klient program kod som använder tjänsten QnA Maker.
 
## <a name="subscription-keys"></a>Prenumerationsnycklar
Du kan visa och återställa dina prenumerationsnycklar från Azure-portalen där du skapade QnA Maker-resursen. 
1. Gå till QnA Maker-resurs i Azure-portalen.

    ![QnA Maker resurslistan](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Gå till **nycklar**.

    ![Prenumerationsnyckel](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Slutpunkt-nycklar

Slutpunkt-nycklar kan hanteras från den [QnA Maker portal](https://qnamaker.ai).

1. Logga in på [QNA Maker Portal](https://qnamaker.ai), gå till din profil och klicka sedan på **tjänst inställningar**.

    ![Slutpunktsnyckel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visa eller återställa dina nycklar.

    ![viktiga endpoint-hanteraren](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Uppdatera dina nycklar om du tycker att de har komprometterats. Detta kan kräva motsvarande ändringar i klient programmet eller bot koden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas på ett annat språk](./language-knowledge-base.md)
