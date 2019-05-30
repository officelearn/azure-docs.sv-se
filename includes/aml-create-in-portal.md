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
ms.openlocfilehash: 72f23b10047928f32886d9054f4dd1abdc569bd8
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66396970"
---
1. Logga in på den [Azure-portalen](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för den Azure-prenumeration du använder. 

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. I det övre vänstra hörnet i portalen, väljer **skapa en resurs**.

   ![Skapa en resurs i Azure-portalen](./media/aml-create-in-portal/portal-create-a-resource-05-2019.png)

1. I sökfältet anger **Maskininlärning**. Välj den **Machine Learning-tjänstens arbetsyta** sökresultat.

   ![Sök efter en arbetsyta](./media/aml-create-in-portal/allservices-search-05-2019.png)

1. I den **ML-arbetsyta på tjänsten** väljer **skapa** att börja.

    ![Knapp för att skapa](./media/aml-create-in-portal/portal-create-button-05-2019.png)

1. I den **ML-arbetsyta på tjänsten** fönstret Konfigurera din arbetsyta.

   Fält|Beskrivning
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbetsyta. I det här exemplet använder vi **docs ws**. Namn måste vara unikt inom resursgruppen. Använd ett namn som är lätt att komma ihåg och skilja från arbetsytor som skapats av andra.  
   Prenumeration |Ange den prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resursgrupp i prenumerationen eller ange ett namn för att skapa en ny resursgrupp. En resursgrupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **docs aml**. 
   Location | Välj platsen närmast användarna och dataresurserna. Den här platsen är där att arbetsytan har skapats.

   ![Skapa arbetsyta](./media/aml-create-in-portal/workspace-create-main-tab-05-2019.png)

1. För att starta processen markerar **granska + skapa**.

    ![Skapa](./media/aml-create-in-portal/workspace-create-main-review-button-05-2019.png)

1. Granskar du konfigurationen av arbetsytan. Om den är korrekt, Välj **skapa**. Det kan ta en stund att skapa arbetsytan.

    ![Skapa](./media/aml-create-in-portal/workspace-create-review-tab-05-2019.png)

1. Om du vill kontrollera statusen för distributionen väljer du ikonen meddelanden **bell**, i verktygsfältet.

1. När processen är klar visas ett meddelande för distribution. Det är också finns i meddelandeavsnittet. Om du vill visa den nya arbetsytan, Välj **gå till resurs**.

   ![Skapandestatus för arbetsytan](./media/aml-create-in-portal/notifications-05-2019.png)
