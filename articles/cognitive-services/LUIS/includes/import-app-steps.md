---
title: Importera app-steg
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840824"
---
1. I [Luis-portalen för förhands granskning](https://preview.luis.ai)väljer du **Importera**på sidan **Mina appar** och sedan **Importera som JSON**. Hitta den sparade JSON-filen från föregående steg. Du behöver inte ändra namnet på appen. Välj **färdig**

1. Välj version på fliken **versioner** i avsnittet **Hantera** och välj sedan **klona** för att klona versionen och ge den ett nytt 10-Character-namn och välj sedan **klar** för att slutföra klonings processen. Eftersom versionsnamnet används som en del av URL-vägen får namnet inte innehålla några tecken som inte är giltiga i en URL.

    > [!TIP]
    > Att klona till en ny version är en bra metod innan du ändrar appen. När du är klar med en version exporterar du versionen (som en. JSON-eller. lu-fil) och kontrollerar filen i käll kontroll systemet.

1. Välj **build** och **avsikt** för att se avsikterna, de viktigaste Bygg stenarna för en Luis-app.

    ![Ändra från sidan versioner till sidan avsikter.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)