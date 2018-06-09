---
title: I den här artikeln beskrivs hur du uppdaterar Azure Stack erbjudanden och planer | Microsoft Docs
description: Den här artikeln beskriver hur du visa och ändra befintliga Azure-stacken erbjudanden och planer.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a84148a3ac31d51ff30cebffab00e5fec8fdaa87
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238506"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack-tillägg-planer
Som operatör Azure Stack skapa planer som innehåller de önskade tjänsterna och tillämpliga kvoter för dina användare att prenumerera på. Dessa [ *basera planer* ](azure-stack-create-plan.md) innehåller grundläggande tjänster att erbjudas till användarna och du kan bara ha en grundläggande plan per erbjudandet. Om du behöver ändra erbjudandet kan du använda *tillägg planer* som gör att du kan ändra planen för att utöka dator, lagring, eller på nätverket, kvoter ursprungligen erbjuds med grundläggande planen. 

Om kombinerar allt i en enda plan optimala i vissa fall kan vilja du har planera och erbjuder ytterligare tjänster som använder tillägget planer. Exempelvis kan du välja att erbjuda IaaS-tjänster som en del av en grundläggande plan med alla PaaS-tjänster som behandlas som tillägg planer. Planer kan också användas för att styra förbrukningen av resurser i Azure Stack-miljö. Till exempel om du vill att användarna ska vara uppmärksam på sin resursanvändning, du kan ha en relativt liten Basplan (beroende på vilka tjänster som krävs) och som användare kommer åt kapacitet måste de skulle bli aviserad om de har redan använts tilldelning av resurser baserat på deras tilldelade planen. Därifrån kan användarna välja en tillgängliga tillägg plan för ytterligare resurser. 

> [!NOTE]
> När en användare lägger till en plan för tillägg till en befintlig prenumeration erbjudande, kan ytterligare resurser ta upp till en timme ska visas. 

## <a name="create-an-add-on-plan"></a>Skapa en plan för tillägg
Tillägget planer skapas genom att ändra ett befintligt erbjudande:

1. Logga in på Azure Stack-administratörsportalen som en administratör i molnet.
2. Följ samma steg som används för att [skapar en ny grundläggande plan](azure-stack-create-plan.md) att skapa en ny plan som erbjuder tjänster som inte tidigare har erbjudit. I det här exemplet inkluderas Key Vault (Microsoft.KeyVault) services i den nya planen.
3. I administratörsportalen, klickar du på **erbjuder** och välj sedan erbjudandet uppdateras med en plan för tillägget.

   ![](media/create-add-on-plan/1.PNG)

4.  Rulla ned till slutet av egenskaperna erbjudandet och välj **tillägg planer**. Klicka på **Lägg till**.
   
    ![](media/create-add-on-plan/2.PNG)

5. Välj planen för att lägga till. I det här exemplet planen kallas **Key vault plan**, och klicka sedan på **Välj** att lägga till planen i erbjudandet. Du bör få ett meddelande om att planen har lagts till erbjudandet.
   
    ![](media/create-add-on-plan/3.PNG)

6. Granska listan över tillägg planer som ingår i erbjudandet att kontrollera att det nya tillägget planerar visas.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Nästa steg
[Skapa ett erbjudande](azure-stack-create-offer.md)