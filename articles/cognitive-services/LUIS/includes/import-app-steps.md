---
title: Steg för importera appar
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422766"
---
1. I [förhandsgranskningsportalen FÖRhandsgranskning väljer](https://preview.luis.ai)du **+ Ny app för konversation**på sidan Mina **appar** och importerar sedan **som JSON**. Leta reda på den sparade JSON-filen från föregående steg. Du behöver inte ändra namnet på appen. Välj **Klar**

1. Välj `0.1` versionen på fliken Versioner **på** fliken **Hantera,** välj sedan **Klon** för att klona versionen och `ml-entity`ge den ett nytt 10-teckensnamn på och välj sedan **Klar** för att slutföra klonprocessen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

    > [!TIP]
    > Kloning till en ny version är en bra metod innan du ändrar appen. När du är klar med en ändring av en version exporterar du versionen (som en .json- eller .lu-fil) och kontrollerar filen i källkontrollsystemet.

1. Välj **Skapa** sedan **avsikter** för att se avsikter, de viktigaste byggstenarna i en LUIS-app.

    ![Ändra från sidan Versioner till sidan Avsikter.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)