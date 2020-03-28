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
ms.openlocfilehash: 8ccd3e6129f4a061eacf83a1f4e70174c697480f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73633753"
---
1. Logga in på [Azure-portalen](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för din Azure-prenumeration.

1. I det övre vänstra hörnet av Azure-portalen väljer du **+ Skapa en resurs**.

    ![Skapa en ny resurs](media/aml-create-in-portal/create-workspace.gif)

1. Använd sökfältet för att hitta **Machine Learning**.

1. Välj **Maskininlärning**.

1. Välj **Skapa** för att börja i fönstret **Maskininlärning.**

1. Ange följande information för att konfigurera den nya arbetsytan:

   Field|Beskrivning 
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar arbetsytan. I det här exemplet använder vi **docs-ws**. Namnen måste vara unika i resursgruppen. Använd ett namn som är lätt att komma ihåg och att skilja från arbetsytor som skapats av andra.  
   Prenumeration |Ange den prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resursgrupp i din prenumeration eller ange ett namn för att skapa en ny resursgrupp. En resursgrupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **docs-aml**. 
   Location | Välj den plats som är närmast användarna och de dataresurser som ska skapas på arbetsytan.
   Arbetsyte utgåva | Välj **Grundläggande** som arbetsytastyp för den här självstudien. Arbetsytans typ (Basic & Enterprise) avgör vilka funktioner du har åtkomst till och prissättning till. Allt i den här självstudien kan utföras med antingen en Basic- eller Enterprise-arbetsyta.

1. När du har konfigurerat arbetsytan väljer du **Granska + Skapa**. 

   > [!Warning] 
   > Det kan ta flera minuter att skapa arbetsytan i molnet.

   När processen är klar visas ett meddelande om lyckad distribution. 
 
 1. Om du vill visa den nya arbetsytan väljer du **Gå till resurs**.

