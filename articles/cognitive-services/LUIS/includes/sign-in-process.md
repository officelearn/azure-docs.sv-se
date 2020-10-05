---
title: inkludera fil
description: inkludera fil
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.openlocfilehash: fda9df6c7e9651bbd3b0b70ad9d47f23c0c19d01
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91541447"
---
## <a name="sign-in-to-luis-portal"></a>Logga in på LUIS-portalen

En ny användare av LUIS måste följa den här proceduren:

1. Logga in på [Luis-portalen](https://www.luis.ai), Välj land/region och godkänn användnings villkoren. Om du ser **Mina appar** i stället finns det redan en Luis-resurs och du bör gå vidare till skapa en app. För regioner som stöds, gå till [redigerings-och publicerings regioner och tillhör ande nycklar](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

1. Välj **Skapa Azure-resurs** och välj sedan **skapa en redigerings resurs för att migrera dina appar till.**

    ![Välj en typ av Language Understanding redigering av resurs](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Fyll i informationen för resursen.

    ![Skärm bild som visar fönstret Skapa en ny resurs för redigering.](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    När du **skapar en ny redigerings resurs**anger du följande information:

    * **Resurs namn** – ett anpassat namn som du väljer, används som en del av URL: en för din redigering och förutsägelse slut punkts frågor.
    * **Klient** organisation – klienten som din Azure-prenumeration är associerad med.
    * **Prenumerations namn** – den prenumeration som ska faktureras för resursen.
    * **Resurs grupp** – ett namn på en anpassad resurs grupp som du väljer eller skapar. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering.
    * **Plats** – plats valet baseras på **resurs grupps** valet.
    * **Pris nivå** – pris nivån avgör den högsta transaktionen per sekund och månad.

1. En sammanfattning av den resurs som ska skapas visas. Välj **Nästa**.

    ![Skärm bild som visar Välkomst sidan med alternativet att länka till ditt Azure-konto.](../media/sign-in/sign-in-confirm-key-selection.png)

1. Bekräfta genom att välja **Fortsätt**.

    ![Skärm bilden visar Välkomst sidan när du har länkat till ditt Azure-konto.](../media/sign-in/sign-in-confirm-continue.png)
