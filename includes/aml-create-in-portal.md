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
ms.date: 09/24/2018
ms.openlocfilehash: 6f73b15ed16cfe26bf14e60a5206568e1a1564fd
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53594322"
---
Logga in på den [Azure-portalen](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för den Azure-prenumeration du använder. 

Portalens instrumentpanel för arbetsytan stöds i Edge, Chrome och Firefox webbläsare.

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard.png)

I det övre vänstra hörnet i portalen, väljer **skapa en resurs**.

   ![Skapa en resurs i Azure-portalen](./media/aml-create-in-portal/portal-create-a-resource.png)

I sökfältet anger **Maskininlärning**. Välj den **Machine Learning-tjänstens arbetsyta** sökresultat.

   ![Sök efter en arbetsyta](./media/aml-create-in-portal/allservices-search.PNG)

I den **ML-arbetsyta på tjänsten** rutan, rulla ned till botten och välj **skapa** att börja.

   ![Skapa](./media/aml-create-in-portal/portal-create-button.png)

I den **ML-arbetsyta på tjänsten** fönstret Konfigurera din arbetsyta.

   Fält|Beskrivning
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbetsyta. I det här exemplet använder vi **docs ws**. Namn måste vara unikt inom resursgruppen. Använd ett namn som är lätt att komma ihåg och skilja från arbetsytor som skapats av andra.  
   Prenumeration |Ange den prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resursgrupp i prenumerationen eller ange ett namn för att skapa en ny resursgrupp. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **docs aml**. 
   Plats | Välj platsen närmast användarna och dataresurserna. Den här platsen är där att arbetsytan har skapats.

   ![Skapa arbetsyta](./media/aml-create-in-portal/workspace-create.png)

För att starta processen markerar **skapa**. Det kan ta en stund att skapa arbetsytan.

Om du vill kontrollera statusen för distributionen väljer du ikonen meddelanden **bell**, i verktygsfältet.

   ![Skapandestatus för arbetsytan](./media/aml-create-in-portal/notifications.png)

När processen är klar visas ett meddelande för distribution. Det är också finns i meddelandeavsnittet. Om du vill visa den nya arbetsytan, Välj **gå till resurs**.
