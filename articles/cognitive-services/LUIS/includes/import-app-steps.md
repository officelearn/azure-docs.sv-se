---
title: Importera app-steg
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/22/2020
ms.author: diberry
ms.openlocfilehash: 37f1b85b4ce8510d5e288df985a55dba659f0c9b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035928"
---
1. I [Luis-portalen](https://www.luis.ai)väljer du **+ ny app för konversation**på sidan **Mina appar** och **importerar sedan som JSON**. Hitta den sparade JSON-filen från föregående steg. Du behöver inte ändra namnet på appen. Välj **färdig**

1. I avsnittet **Hantera** går du till fliken **versioner** , väljer `0.1` version, väljer **klon** för att klona versionen och ger den ett nytt namn och `ml-entity` väljer sedan **klar** för att slutföra klonings processen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

    > [!TIP]
    > Att klona till en ny version är en bra metod innan du ändrar appen. När du är klar med en ändring i en version exporterar du versionen (som en. JSON-eller. lu-fil) och kontrollerar filen i käll kontroll systemet.

1. Välj **build** och **avsikt** för att se avsikterna, de viktigaste Bygg stenarna för en Luis-app.

    ![Ändra från sidan versioner till sidan avsikter.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
