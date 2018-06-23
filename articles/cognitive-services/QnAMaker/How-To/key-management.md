---
title: Nyckelhantering - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Så här hanterar du dina frågor och svar om Maker nycklar
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b402187f4949dac34fa476648c81b980ba3efc96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354078"
---
# <a name="key-management"></a>Nyckelhantering

Frågor och svar om Maker tjänsten behandlar två typer av nycklar, **prenumeration nycklar** och **endpoint nycklar**.

![hantering av nycklar](../media/qnamaker-how-to-key-management/key-management.png)

1. **Prenumerationen nycklar**: dessa nycklar används för att få åtkomst till den [hanteringstjänsten för frågor och svar om Maker API: er](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Dessa API: er kan du utföra olika CRUD-åtgärder i kunskapsbasen.  

2. **Slutpunkten nycklar**: dessa nycklar används för åtkomst till knowledge base-slutpunkten för att få ett svar för en fråga användaren. Normalt använder du den här slutpunkten i chatt bot/App koden som förbrukar Maker frågor och svar om tjänsten.
 
## <a name="subscription-keys"></a>Prenumerationen nycklar
Du kan visa och återställa dina nycklar för prenumeration från Azure-portalen där du skapade resursen frågor och svar om Maker. 
1. Gå till resursen frågor och svar om Maker i Azure-portalen.

    ![Frågor och svar om Maker resurslistan](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Gå till **nycklar**.

    ![Prenumerationen nyckel](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Slutpunkten nycklar

Slutpunkten nycklar kan hanteras från den [frågor och svar om Maker portal](https://qnamaker.ai).

1. Logga in på den [frågor och svar om Maker portal](https://qnamaker.ai), och gå till **hantera nycklar**.

    ![slutpunktsnyckel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visa eller återställa dina nycklar.

    ![viktiga endpoint-hanteraren](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Uppdatera dina nycklar om du anser att de har komprometterats. Du kan behöva motsvarande ändringar i din App/Bot-kod.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en knowledge base på ett annat språk](./language-knowledge-base.md)
