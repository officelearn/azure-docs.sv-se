---
title: ta med fil
description: ta med fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/21/2019
ms.openlocfilehash: 2ec45b67367198c14fc9d03cdb659a51aed8a504
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841482"
---
1. Logga in på den [Azure-portalen](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för den Azure-prenumeration du använder. 

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. I det övre vänstra hörnet i portalen, väljer **skapa en resurs**.

   ![Skapa en resurs i Azure-portalen](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. Använd sökfältet för att välja **Machine Learning-tjänstens arbetsyta**.

   ![Sök efter en arbetsyta](./media/aml-create-in-portal/allservices-search.png)

1. I den **ML-arbetsyta på tjänsten** väljer **skapa** att börja.

    ![Knapp för att skapa](./media/aml-create-in-portal/portal-create-button.png)

1. I den **ML-arbetsyta på tjänsten** fönstret Konfigurera din arbetsyta.

    ![Skapa arbetsyta](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Fält|Beskrivning
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbetsyta. I det här exemplet använder vi **docs ws**. Namn måste vara unikt inom resursgruppen. Använd ett namn som är lätt att komma ihåg och skilja från arbetsytor som skapats av andra.  
   Subscription |Ange den prenumeration som du vill använda.
   Resource group | Använd en befintlig resursgrupp i prenumerationen eller ange ett namn för att skapa en ny resursgrupp. En resursgrupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **docs aml**. 
   Location | Välj platsen närmast användarna och dataresurserna. Den här platsen är där att arbetsytan har skapats.

1. Granskar du konfigurationen av arbetsytan och välj **skapa**. Det kan ta en stund att skapa arbetsytan.

1. När processen är klar visas ett meddelande för distribution. Det är också finns i meddelandeavsnittet. Om du vill visa den nya arbetsytan, Välj **gå till resurs**.

   ![Skapandestatus för arbetsytan](./media/aml-create-in-portal/notifications.png)
