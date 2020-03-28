---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 155c88ec4766391f70701b17038b915c399d8b0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77372083"
---
## <a name="sign-in-to-luis-portal"></a>Logga in på LUIS-portalen

En ny användare till LUIS måste följa den här proceduren:

1. Logga in på [LUIS-portalen (förhandsgranskning)](https://preview.luis.ai)väljer du ditt land och godkänner användarvillkoren. Om du ser **Mina appar** i stället finns det redan en LUIS-resurs och du bör gå vidare för att skapa en app.

1. Välj **Skapa Azure-resurs** och välj sedan **Skapa en redigeringsresurs att migrera dina appar till.**

    ![Välj en typ av språk understanding-redigeringsresurs](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Fyll i informationen för resursen.

    ![Skapa redigeringsresurs](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    När **du skapar en ny redigeringsresurs**anger du följande information:

    * **Resursnamn** - ett eget namn som du väljer, som används som en del av URL:en för dina redigerings- och förutsägelseslutpunktsfrågor.
    * **Klient** - klienten som din Azure-prenumeration är associerad med.
    * **Prenumerationsnamn** - den prenumeration som faktureras för resursen.
    * **Resursgrupp** - ett anpassat resursgruppnamn som du väljer eller skapar. Med resursgrupper kan du gruppera Azure-resurser för åtkomst och hantering.
    * **Plats** - platsvalet baseras på valen **av resursgrupp.**
    * **Prisnivå** - prisnivån bestämmer den maximala transaktionen per sekund och månad.

1. En sammanfattning av resursen som ska skapas visas. Välj **Nästa**.

    ![Skapa redigeringsresurs](../media/sign-in/sign-in-confirm-key-selection.png)

1. Bekräfta genom att välja **Fortsätt**.

    ![Skapa redigeringsresurs](../media/sign-in/sign-in-confirm-continue.png)