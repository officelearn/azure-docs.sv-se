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
   Resursgrupp | Använd en befintlig resursgrupp i prenumerationen eller ange ett namn för att skapa en ny resursgrupp. En resursgrupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **docs-aml**. 
   Location | Välj den plats som är närmast användarna och de dataresurser som ska skapas på arbetsytan.
   Arbetsyte utgåva | Välj **Företag**. Den här självstudien kräver användning av Enterprise-utgåvan. Enterprise-utgåvan är i förhandsversion och lägger för närvarande inte till några extra kostnader. 

1. När du är klar med konfigurationen av arbetsytan väljer du **Skapa**. 

   > [!WARNING] 
   > Det kan ta flera minuter att skapa arbetsytan i molnet.

   När processen är klar visas ett meddelande om lyckad distribution. 
 
 1. Om du vill visa den nya arbetsytan väljer du **Gå till resurs**.

