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
ms.openlocfilehash: 6d6e6a2aea867c5b603d950a4bbaa33f14695f45
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47011034"
---
Logga in på [Azure-portalen](https://portal.azure.com/) med autentiseringsuppgifterna för den Azure-prenumeration du ska använda. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

Portalens instrumentpanel för arbetsytan stöds i Edge, Chrome och Firefox webbläsare.

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard.png)

Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen.

   ![Skapa en resurs i Azure-portalen](./media/aml-create-in-portal/portal-create-a-resource.png)

Skriv **Machine Learning** i sökfältet. Välj sökresultatet **Machine Learning-arbetsyta**.

   ![Sök efter arbetsyta](./media/aml-create-in-portal/allservices-search.PNG)

I den **Machine Learning-arbetsyta** rutan, rulla ned till botten och välj **skapa** att börja.

   ![skapa](./media/aml-create-in-portal/portal-create-button.png)

I den **ML-arbetsyta** fönstret Konfigurera din arbetsyta.

   Fält|Beskrivning
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbetsyta.  Här använder vi docs ws. Namn måste vara unikt inom resursgruppen. Använd ett namn som är lätt att komma ihåg och skilja från arbetsytor som skapats av andra.  
   Prenumeration |Välj den prenumeration som du vill använda. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
   Resursgrupp | Använd en befintlig resursgrupp i prenumerationen eller ange ett namn för att skapa en ny resursgrupp. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning.  Här använder vi docs aml. 
   Plats | Välj den plats som är närmast användarna och dataresurserna. Det här är där att arbetsytan har skapats.

   ![Skapa arbetsyta](./media/aml-create-in-portal/workspace-create.png)

Välj **Skapa** för att påbörja skapandeprocessen.  Det kan ta en stund att skapa arbetsytan.

   Välj meddelandeikonen (klockan) i verktygsfältet för att kontrollera statusen för distributionen.

   ![Skapa arbetsyta](./media/aml-create-in-portal/notifications.png)

   När du är klar visas ett meddelande för distribution.  Det är också finns i meddelandeavsnittet.   Klicka på den **gå till resurs** för att visa den nya arbetsytan.
