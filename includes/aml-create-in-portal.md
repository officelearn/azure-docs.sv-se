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
ms.openlocfilehash: 99c31293168fd5ff3e6b95a70dc481e01e4ac8b4
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400249"
---
Logga in på den [Azure-portalen](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för den Azure-prenumeration du använder. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

Portalens instrumentpanel för arbetsytan stöds i Edge, Chrome och Firefox webbläsare.

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard.png)

I det övre vänstra hörnet i portalen, väljer **skapa en resurs**.

   ![Skapa en resurs i Azure-portalen](./media/aml-create-in-portal/portal-create-a-resource.png)

I sökfältet anger **Maskininlärning**. Välj den **Machine Learning-tjänstens arbetsyta** sökresultat.

   ![Sök efter arbetsyta](./media/aml-create-in-portal/allservices-search.PNG)

I den **Machine Learning-tjänstens arbetsyta** rutan, rulla ned till botten och välj **skapa** att börja.

   ![Skapa](./media/aml-create-in-portal/portal-create-button.png)

I den **ML-arbetsyta på tjänsten** fönstret Konfigurera din arbetsyta.

   Fält|Beskrivning
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbetsyta. Vi använder här docs-ws. Namn måste vara unikt inom resursgruppen. Använd ett namn som är lätt att komma ihåg och skilja från arbetsytor som skapats av andra.  
   Prenumeration |Ange den prenumeration som du vill använda. Om du har mer än en prenumeration väljer du den prenumeration som används för fakturering.
   Resursgrupp | Använd en befintlig resursgrupp i prenumerationen eller ange ett namn för att skapa en ny resursgrupp. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Vi använder här docs-aml. 
   Plats | Välj platsen närmast användarna och dataresurserna. Den här platsen är där att arbetsytan har skapats.

   ![Skapa arbetsyta](./media/aml-create-in-portal/workspace-create.png)

För att starta processen markerar **skapa**. Det kan ta en stund att skapa arbetsytan.

Välj meddelandeikonen (klockan) i verktygsfältet för att kontrollera statusen för distributionen.

   ![Skapandestatus för arbetsytan](./media/aml-create-in-portal/notifications.png)

När processen är klar visas ett meddelande för distribution. Det är också finns i meddelandeavsnittet. Om du vill visa den nya arbetsytan, Välj **gå till resurs**.
