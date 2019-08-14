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
ms.openlocfilehash: e571c65e64fad73c646aa05366cab685aa745caa
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968945"
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

1. Om du kommer att använda ett kod fritt alternativ från portalen, t. ex. ett visuellt gränssnitt eller automatiskt ML experiment, gör du nu. Om du kommer att skapa en [Notebook VM](../articles/machine-learning/service/tutorial-1st-experiment-sdk-setup.md#azure) är du också klar. 

1. Om du planerar att använda kod i din lokala miljö som hänvisar till den här arbets ytan väljer du **Hämta config. JSON** från **översikts** avsnittet i arbets ytan.  

   ![Ladda ned config.json](./media/aml-create-in-portal/configure.png)
   
   Placera filen i katalog strukturen med dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml*eller i en överordnad katalog. När du skapar en Notebook VM, läggs den här filen till rätt katalog på den virtuella datorn åt dig.

    

