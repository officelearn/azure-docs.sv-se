---
title: Steg för importera appar
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74840824"
---
1. I [förhandsgranskningen av LUIS-portalen](https://preview.luis.ai)väljer du **Importera**på sidan **Mina appar** och importerar sedan **som JSON**. Leta reda på den sparade JSON-filen från föregående steg. Du behöver inte ändra namnet på appen. Välj **Klar**

1. Välj **Manage** versionen på fliken Versioner på fliken **Hantera,** välj sedan **Klon** för att klona versionen och ge den ett nytt 10-teckensnamn och välj sedan **Klar** för att slutföra klonprocessen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

    > [!TIP]
    > Kloning till en ny version är en bra metod innan du ändrar appen. När du är klar med en version exporterar du versionen (som en .json- eller .lu-fil) och kontrollerar filen i källkontrollsystemet.

1. Välj **Skapa** sedan **avsikter** för att se avsikter, de viktigaste byggstenarna i en LUIS-app.

    ![Ändra från sidan Versioner till sidan Avsikter.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)