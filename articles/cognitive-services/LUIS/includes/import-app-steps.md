---
title: Importera app-steg
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130756"
---
1. I [Luis-portalen](https://www.luis.ai)väljer du **+ ny app för konversation** på sidan **Mina appar** och **importerar sedan som JSON** . Hitta den sparade JSON-filen från föregående steg. Du behöver inte ändra namnet på appen. Välj **färdig**

1. I avsnittet **Hantera** går du till fliken **versioner** , väljer `0.1` version, väljer **klon** för att klona versionen och ger den ett nytt namn och `ml-entity` väljer sedan **klar** för att slutföra klonings processen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

    > [!TIP]
    > Att klona till en ny version är en bra metod innan du ändrar appen. När du är klar med en ändring i en version exporterar du versionen (som en. JSON-eller. lu-fil) och kontrollerar filen i käll kontroll systemet.

1. Välj **Bygg** överst på skärmen och klicka sedan på **avsikter** i den vänstra navigerings menyn. Appens avsikter visas, som är de viktigaste Bygg stenarna för en LUIS-app.
