---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 1effd07cef439a6257028549b7b7114b742eb478
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488823"
---
## <a name="sign-in-to-luis-portal"></a>Logga in på LUIS-portalen

En ny användare av LUIS måste följa den här proceduren:

1. Logga in på [Luis-portalen](https://www.luis.ai), Välj ditt land och godkänn användnings villkoren.
1. Välj **Skapa Azure-resurs** och välj sedan **skapa en redigerings resurs för att migrera dina appar till.**

    ![Välj en typ av Language Understanding redigering av resurs](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Fyll i informationen för resursen.

    ![Skapa en redigerings resurs](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    När du **skapar en ny redigerings resurs**anger du följande information: 

    * **Resurs namn** – ett anpassat namn som du väljer, används som en del av URL: en för din redigering och förutsägelse slut punkts frågor.
    * **Klient** organisation – klienten som din Azure-prenumeration är associerad med. 
    * **Prenumerations namn** – den prenumeration som ska faktureras för resursen.
    * **Resurs grupp** – ett namn på en anpassad resurs grupp som du väljer eller skapar. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering. 
    * **Plats** – plats valet baseras på **resurs grupps** valet.
    * **Pris nivå** – pris nivån avgör den högsta transaktionen per sekund och månad.

1. En sammanfattning av den resurs som ska skapas visas. Välj **Nästa**.

    ![Skapa en redigerings resurs](../media/sign-in/sign-in-confirm-key-selection.png)

1. Bekräfta genom att välja **Fortsätt**. 

    ![Skapa en redigerings resurs](../media/sign-in/sign-in-confirm-continue.png)