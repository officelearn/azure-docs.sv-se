---
title: inkludera fil
description: inkludera fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 11/04/2019
ms.openlocfilehash: f5f132d257e30cd8f4fa1153087bf0df2f0f5b2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841870"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för din Azure-prenumeration.

1. I det övre vänstra hörnet av Azure Portal väljer du **+ skapa en resurs**.

    ![Skärm bild som visar alternativet Skapa en resurs.](media/aml-create-in-portal/create-workspace.gif)

1. Använd Sök fältet för att hitta **Machine Learning**.

1. Välj **Machine Learning**.

1. I fönstret **Machine Learning** väljer du **skapa** för att börja.

1. Ange följande information för att konfigurera din nya arbets yta:

   Field|Beskrivning
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbets yta. I det här exemplet använder vi **dokument-WS**. Namn måste vara unika i resurs gruppen. Använd ett namn som är enkelt att återkalla och särskilja från arbets ytor som skapats av andra.
   Prenumeration |Välj den Azure-prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resurs grupp i din prenumeration eller ange ett namn för att skapa en ny resurs grupp. En resurs grupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **AML-dokument**. 
   Location | Välj den plats som är närmast dina användare och data resurserna för att skapa din arbets yta.
   Arbetsyte version | Välj **Basic** som arbets ytans typ för den här självstudien. Typ av arbets yta avgör vilka funktioner du kommer att ha åtkomst till och prissättning på. Allt i den här självstudien kan utföras med en grundläggande eller Enterprise-arbetsyta.

1. När du är klar med konfigurationen av arbets ytan väljer du **Granska + skapa**.

   > [!Warning]
   > Det kan ta flera minuter att skapa din arbets yta i molnet.

   När processen är klar visas ett meddelande om lyckad distribution.
 
 1. Om du vill visa den nya arbets ytan väljer du **gå till resurs**.

