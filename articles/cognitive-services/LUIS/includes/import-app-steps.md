---
title: Importera app-steg
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: c0253360c66ef6fd995f65e8a83ba5adcdf19543
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806429"
---
1. I [Luis-portalen för förhands granskning](https://preview.luis.ai)väljer du **Importera**på sidan **Mina appar** och sedan **Importera som JSON**. Hitta den sparade JSON-filen från föregående steg. Du behöver inte ändra namnet på appen. Välj **färdig**

1. Välj version på fliken **versioner** i avsnittet **Hantera** och välj sedan **klona** för att klona versionen och ge den ett nytt 10-Character-namn och välj sedan **klar** för att slutföra klonings processen. Eftersom versionsnamnet används som en del av URL-vägen får namnet inte innehålla några tecken som inte är giltiga i en URL.

    > [!TIP]
    > Att klona en version till en ny version är en bra metod innan du ändrar appen. När du är klar med en version exporterar du versionen (som en. JSON-eller. lu-fil) och kontrollerar filen i käll kontroll systemet.

1. Välj **build** och **avsikt** för att se avsikterna, de viktigaste Bygg stenarna för en Luis-app.

    ![Ändra från sidan versioner till sidan avsikter.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)