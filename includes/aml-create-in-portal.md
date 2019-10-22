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
ms.date: 08/14/2019
ms.openlocfilehash: 1da2afc6c0dfa0571d6e7ccbf358bb574aa736c5
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "71830181"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för den Azure-prenumeration du använder. 

1. I det övre vänstra hörnet av Azure Portal väljer du **+ skapa en resurs**.

      ![Skapa en ny resurs](media/aml-create-in-portal/portal-create-resource.png)

1. Använd Sök fältet för att hitta **Machine Learning service-arbetsyta**.

1. Välj **Machine Learning tjänst arbets yta**.

1. I fönstret **Machine Learning service-arbetsyta** väljer du **skapa** för att börja.

1. Ange följande information för att konfigurera din nya arbets yta:

   Fält|Beskrivning 
   ---|---
   Namn på arbets yta |Ange ett unikt namn som identifierar din arbets yta. I det här exemplet använder vi **dokument-WS**. Namn måste vara unika i resurs gruppen. Använd ett namn som är enkelt att återkalla och särskilja från arbets ytor som skapats av andra.  
   Prenumeration |Ange den prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resurs grupp i din prenumeration eller ange ett namn för att skapa en ny resurs grupp. En resurs grupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **AML-dokument**. 
   Plats | Välj den plats som är närmast dina användare och data resurserna för att skapa din arbets yta.

1. När du är färdig med konfigurationen av arbets ytan väljer du **skapa**. 

   > [!Warning] 
   > Det kan ta flera minuter att skapa din arbets yta i molnet.

   När processen är klar visas ett meddelande om lyckad distribution. 
 
 1. Om du vill visa den nya arbets ytan väljer du **gå till resurs**.

