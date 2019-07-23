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
ms.date: 07/21/2019
ms.openlocfilehash: 51bd3dfb33b1f445db8672e1b987ee6c6242e09c
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370967"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för den Azure-prenumeration du använder. 

1. I det övre vänstra hörnet i portalen väljer du **skapa en resurs**.

   ![Skapa en resurs i Azure-portalen](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. Använd Sök fältet för att hitta **Machine Learning service-arbetsyta**.

1. Välj **Machine Learning tjänst arbets yta**.

1. I fönstret **ml service-arbetsyta** väljer du **skapa** för att börja.

1. Konfigurera din nya arbets yta genom att ange arbets ytans namn, prenumeration, resurs grupp och plats.

    ![Skapa arbetsyta](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Fält|Beskrivning
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbets yta. I det här exemplet använder vi **dokument-WS**. Namn måste vara unika i resurs gruppen. Använd ett namn som är enkelt att återkalla och särskilja från arbets ytor som skapats av andra.  
   Subscription |Ange den prenumeration som du vill använda.
   Resource group | Använd en befintlig resurs grupp i din prenumeration eller ange ett namn för att skapa en ny resurs grupp. En resurs grupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **AML-dokument**. 
   Location | Välj den plats som är närmast dina användare och data resurserna för att skapa din arbets yta.

1. När du är färdig med konfigurationen av arbets ytan väljer du **skapa**. 

   Det kan ta en stund att skapa arbets ytan.

   När processen är klar visas ett meddelande om lyckad distribution. Den finns också i avsnittet meddelanden. Om du vill visa den nya arbets ytan väljer du **gå till resurs**.

   ![Status för skapande av arbets yta](./media/aml-create-in-portal/notifications.png)
