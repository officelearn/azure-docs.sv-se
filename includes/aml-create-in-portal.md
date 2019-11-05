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
ms.date: 11/04/2019
ms.openlocfilehash: d49a84f26453a6a7b2ff6d7f419fbc53ad8b98ba
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475958"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för din Azure-prenumeration.

1. I det övre vänstra hörnet av Azure Portal väljer du **+ skapa en resurs**.

      ![Skapa en ny resurs](media/aml-create-in-portal/portal-create-resource.png)

1. Använd Sök fältet för att hitta **Machine Learning**.

1. Välj **Machine Learning**.

1. I fönstret **Machine Learning** väljer du **skapa** för att börja.

1. Ange följande information för att konfigurera din nya arbets yta:

   Fält|Beskrivning 
   ---|---
   Namn på arbets yta |Ange ett unikt namn som identifierar din arbets yta. I det här exemplet använder vi **dokument-WS**. Namn måste vara unika i resurs gruppen. Använd ett namn som är enkelt att återkalla och särskilja från arbets ytor som skapats av andra.  
   Prenumeration |Ange den prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resurs grupp i din prenumeration eller ange ett namn för att skapa en ny resurs grupp. En resurs grupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **AML-dokument**. 
   Plats | Välj den plats som är närmast dina användare och data resurserna för att skapa din arbets yta.
   Arbetsyte version | Välj **Basic** som arbets ytans typ för den här självstudien. Typ av arbets yta (Basic & Enterprise) bestämmer vilka funktioner du kommer att ha åtkomst till och prissättning. Allt i den här självstudien kan utföras med en grundläggande eller Enterprise-arbetsyta.

1. När du är färdig med konfigurationen av arbets ytan väljer du **Granska + skapa**. 

   > [!Warning] 
   > Det kan ta flera minuter att skapa din arbets yta i molnet.

   När processen är klar visas ett meddelande om lyckad distribution. 
 
 1. Om du vill visa den nya arbets ytan väljer du **gå till resurs**.

