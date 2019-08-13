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
ms.date: 07/31/2019
ms.openlocfilehash: 0a497ce506e1aa3b0f9afc47bf1ab8382c9c0405
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951453"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för den Azure-prenumeration du använder. 

1. I det övre vänstra hörnet av Azure Portal väljer du **skapa en resurs**.

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

1. Om din kod refererar till den här arbets ytan från den lokala miljön väljer du **Hämta config. JSON** från **översikts** avsnittet i arbets ytan.  

   ![Ladda ned config.json](./media/aml-create-in-portal/configure.png)

    Placera denna JSON-fil i katalog strukturen som innehåller dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml*eller i en överordnad katalog.

   Om du kommer att använda en [Notebook-VM](../articles/machine-learning/service/tutorial-1st-experiment-sdk-setup.md#azure) behöver du inte ladda ned den här filen, den läggs till i rätt katalog på den virtuella datorn åt dig.
