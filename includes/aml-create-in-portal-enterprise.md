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
ms.date: 10/04/2019
ms.openlocfilehash: b44d624db419919823ec85bcb599007fb75c92b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73929629"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för din Azure-prenumeration. 

1. I det övre vänstra hörnet av Azure Portal väljer du **+ skapa en resurs**.

      ![Skapa en ny resurs](media/aml-create-in-portal/create-workspace.gif)

1. Använd Sök fältet för att hitta **Machine Learning**.

1. Välj **Machine Learning**.

1. I fönstret **Machine Learning** väljer du **skapa** för att börja.

1. Ange följande information för att konfigurera din nya arbets yta:

   Field|Beskrivning 
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbets yta. I det här exemplet använder vi **dokument-WS**. Namn måste vara unika i resurs gruppen. Använd ett namn som är enkelt att återkalla och särskilja från arbets ytor som skapats av andra. 
   Prenumeration |Ange den prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resurs grupp i din prenumeration eller ange ett namn för att skapa en ny resurs grupp. En resurs grupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **AML-dokument**. 
   Location | Välj den plats som är närmast dina användare och data resurserna för att skapa din arbets yta.
   Arbetsyte version | Välj **företag**. Den här självstudien kräver att Enterprise Edition används. Enterprise-utgåvan är i för hands version och lägger inte till några extra kostnader. 

1. När du är klar med konfigurationen av arbets ytan väljer du **skapa**. 

   > [!WARNING] 
   > Det kan ta flera minuter att skapa din arbets yta i molnet.

   När processen är klar visas ett meddelande om lyckad distribution. 
 
 1. Om du vill visa den nya arbets ytan väljer du **gå till resurs**.

