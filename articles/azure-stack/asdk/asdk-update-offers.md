---
title: "I kursen får du lära dig hur att uppdatera Azure Stack erbjudanden och planer | Microsoft Docs"
description: "Den här artikeln beskriver hur du visa och ändra befintliga Azure-stacken erbjudanden och planer."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>Självstudier: uppdatera erbjudanden och planer
Som operatör Azure Stack skapa planer som innehåller de önskade tjänsterna och tillämpliga kvoter för dina användare att prenumerera på. Dessa *basera planer* innehåller grundläggande tjänster att erbjudas till användarna och du kan bara ha en grundläggande plan per erbjudandet. Om du behöver ändra erbjudandet kan du använda *tillägg planer* som gör att du kan ändra planen för att utöka dator, lagring, eller på nätverket, kvoter ursprungligen erbjuds med grundläggande planen. 

Om kombinerar allt i en enda plan optimala i vissa fall kan vilja du har planera och erbjuder ytterligare tjänster som använder tillägget planer. Exempelvis kan du välja att erbjuda IaaS-tjänster som en del av en grundläggande plan med alla PaaS-tjänster som behandlas som tillägg planer. Planer kan också användas för att styra förbrukningen av resurser i begränsad ASDK miljön. Till exempel om du vill att användarna ska vara uppmärksam på sin resursanvändning, du kan ha en relativt liten Basplan (beroende på vilka tjänster som krävs) och som användare kommer åt kapacitet måste de skulle bli aviserad om de har redan använts tilldelning av resurser baserat på deras tilldelade planen. Därifrån kan användarna välja en tillgängliga tillägg plan för ytterligare resurser. 

> [!NOTE]
> När en användare lägger till en plan för tillägg till en befintlig prenumeration erbjudande, kan ytterligare resurser ta upp till en timme ska visas. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en plan för tillägg 
> * Prenumerera på tillägget planen

## <a name="create-an-add-on-plan"></a>Skapa en plan för tillägg
**Tillägget planer** skapas genom att ändra ett befintligt erbjudande.

1. Logga in på den [Stack Azure portal](https://adminportal.local.azurestack.external) som en administratör i molnet.
2. Följ samma steg som tidigare [skapar en grundläggande plan](asdk-offer-services.md) att skapa en ny plan som erbjuder tjänster som inte tidigare har erbjudit. I det här exemplet inkluderas Key Vault (Microsoft.KeyVault) services i planen.
3. I administratörsportalen, klickar du på **erbjuder** och välj sedan erbjudandet uppdateras med en plan för tillägget.

   ![](media/asdk-update-offers/1.PNG)

4.  Rulla ned till slutet av egenskaperna erbjudandet och välj **tillägg planer**. Klicka på **Lägg till**.
   
    ![](media/asdk-update-offers/2.PNG)

5. Välj planen för att lägga till. I det här exemplet planen kallas **Key vault plan**, och klicka sedan på **Välj** att lägga till planen i erbjudandet. Du bör få ett meddelande om att planen har lagts till erbjudandet.
   
    ![](media/asdk-update-offers/3.PNG)

6. Granska listan över tillägg planer som ingår i erbjudandet att kontrollera att det nya tillägget planerar visas.
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>Prenumerera på tillägget planen
Du måste logga in på Azure-stacken användarportalen att utöka en befintlig Azure Stack-prenumeration med en plan för tillägget.

Följ dessa steg för att identifiera ytterligare resurser som har erbjudits av operatorn Azure Stack och lägga till en plan för tillägg till ett erbjudande som du tidigare har prenumererat på.

1. Logga in på den [användarportalen](https://portal.local.azurestack.external).
2. För att hitta prenumerationen att utöka med tillägget planen klickar du på **fler tjänster**, klickar du på **prenumerationer**, väljer din prenumeration.
   
    ![](media/asdk-update-offers/5.PNG)

3.  I översikten prenumeration klickar du på **Lägg till plan**.
   
    ![](media/asdk-update-offers/6.PNG)

4. I bladet Lägg till planen väljer du tillägget planen för att lägga till i prenumerationen. I det här exemplet **Key vault plan** är markerad. Du bör sedan få ett meddelande om att planen tillägg var lyckad förvärvas och att du behöver uppdatera portalen för att komma åt uppdaterade prenumerationen.
   
    ![](media/asdk-update-offers/7.PNG)

5. Slutligen granska tillägg planer som är associerade med publiceringsinställningsfilen för att se till att planen tillägget har lagts.
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en plan för tillägg 
> * Prenumerera på tillägget planen

> [!div class="nextstepaction"]
> [Skapa en virtuell dator från en mall](asdk-create-vm-template.md)

