---
title: "I den här självstudiekursen skapar du en Azure-stacken erbjudande | Microsoft Docs"
description: "Lär dig hur du skapar ett Azure-stacken erbjudande inklusive planer och kvoter."
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
ms.openlocfilehash: 083b5e20b89f22cb8e523926858fe9ffb1441319
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>Självstudier: erbjuder Azure Stack IaaS-tjänster
Du kan skapa erbjudanden som användarna (kallas ibland klienter) kan prenumerera på som administratör Stack för Azure-molnet. Använda sin prenumeration kan kan användare använda Azure Stack-tjänster.

Den här kursen visar hur du skapar ett erbjudande så att användarna kan skapa virtuella datorer baserat på Azure-stacken marketplace Windows Server 2016 Datacenter-avbildning som användes i den [tidigare kursen](asdk-marketplace-item.md).

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett erbjudande
> * Skapa en plan
> * Ange kvoter
> * Ange erbjudande till offentligt

Tjänster som levereras till användare med hjälp av prenumerationer, erbjudanden och planer i Azure-stacken. Användare kan prenumerera på flera erbjudanden. Erbjudanden som kan ha en eller flera planer och planer kan ha en eller flera tjänster som visas i följande diagram:

![Prenumerationer, erbjudanden och planer](media/asdk-offer-services/sop.png)

Som operatör Azure Stack erbjuda tjänster först uppmanas du att skapa erbjudandet, och sedan en plan och slutligen kvoter. När du har skapat ett erbjudande Azure Stack användare kan sedan prenumerera erbjudanden via användarportalen.

## <a name="create-an-offer"></a>Skapa ett erbjudande
**Erbjuder** är grupper med en eller flera serviceplaner Azure Stack operatörer presentera för användare att köpa eller prenumerera på.

1. Logga in på den [Stack Azure portal](https://adminportal.local.azurestack.external) som en administratör i molnet.

2. Klicka på **nya** > **erbjuder + planer** > **erbjuder**.

   ![Nytt erbjudande](media/asdk-offer-services/new-offer.png)

2. I den **nya erbjuder** avsnittet, Fyll i **visningsnamn** och **resursnamnet**, och välj en ny eller befintlig **resursgruppen**. Visningsnamnet är det erbjudande offentliga eget namn som visas för användarna. Endast moln-operatorn kan se resursnamnet som används av administratörer för att arbeta med erbjudandet som en Azure Resource Manager-resurs.

   ![Visningsnamn](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>Skapa en plan
När du har angett informationen om grundläggande erbjudande, måste du lägga till minst en Basplan erbjudandet. 

**Planer** Tillåt Azure Stack operatorer som gruppen tjänster och deras associerade kvoter att erbjudas till användare.

1. Klicka på **basera planer**, och i den **Plan** klickar du på **Lägg till** att lägga till en ny plan i erbjudandet.

   ![Lägg till en plan](media/asdk-offer-services/new-plan.png)

2. I den **nya planera** avsnittet, Fyll i **visningsnamn** och **resursnamnet**. Visningsnamnet är planens eget namn som visas för användarna. Endast moln-operatorn kan se resursnamnet som används av molnoperatörer för att arbeta med planen som en Azure Resource Manager-resurs.

   ![Planera visningsnamn](media/asdk-offer-services/plan-display-name.png)

3. Välj sedan tjänsterna som ska ingå i planen. Om du vill erbjuda IaaS-tjänster, klickar du på **Services**väljer **Microsoft.Compute**, **Microsoft.Network**, och **Microsoft.Storage**, och sedan Klicka på **Välj**.

   ![Planera services](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>Ange kvoter
Nu när erbjudandet har en plan som innehåller tjänster, måste du ange kvoter för dem. 

**Kvoter** bestämmer du mängden resurser som en användare kan använda för varje tjänst som ingår i planen som erbjuds.

1. Klicka på **kvoter**, och välj sedan tjänsten som du vill skapa en kvot. 

   Kom igång genom att först skapa en kvot för beräknings-tjänsten. Välj i listan namnområde i **Microsoft.Compute** namnområde och klicka sedan på **skapa nya kvoten**.
   
   ![Skapa ny kvot](media/asdk-offer-services/create-quota.png)

2. På den **Skapa kvot** avsnittet, Skriv ett namn för kvoten, ange de önskade parametrarna för kvoten och klicka på **OK**.

   ![Kvoten namn](media/asdk-offer-services/quota-properties.png)

3. Välj den kvot som du skapade för den **Microsoft.Compute** service.

   ![Välj kvot](media/asdk-offer-services/set-quota.png)

4. Upprepa steg 1 – 3 för att ange kvoter för nätverk och lagring och klicka sedan på OK i avsnittet kvoter. Klicka sedan på **OK** på den **ny plan** avsnittet för att slutföra installationen plan. 

   ![Ange alla kvoter](media/asdk-offer-services/all-quotas-set.png)

5. Skapa erbjudandet genom att klicka på **skapa** i avsnittet planera. Ett meddelande visas när erbjudandet har skapats och visas som tillgängliga erbjudanden.

   ![Erbjudande skapas](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>Ange erbjudande till offentligt
Erbjudanden måste offentliggöras för att användarna ska se dem när du väljer ett erbjudande att prenumerera på. 

Erbjudanden kan vara:
- **Offentliga**: visas för alla användare.
- **Privata**: endast synliga moln-administratörer. Användbart vid utformning plan eller erbjudande eller om molnadministratören vill skapa varje prenumeration för användare.
- **Inaktiverad**: Stängda för nya prenumeranter. Molnadministratören kan använda inaktiverade för att förhindra framtida prenumerationer, men lämna aktuella prenumeranter orörd.

> [!TIP]
> Ändringar av erbjudandet är inte direkt synliga för användare. Om du vill se ändringarna som användare kan behöva logga ut och logga in igen på den [användarportalen](https://portal.local.azurestack.external) vill se nya erbjudandet.

Ange nya erbjudandet till offentligt: 

1. Klicka på menyn instrumentpanelen **erbjuder** och klicka sedan på det erbjudande som du skapade.

2. Klicka på **Ändra status** och klicka därefter på **Offentlig**.

   ![Offentliga tillstånd](media/asdk-offer-services/set-public.png)

3. Erbjudandet att vara tillgängliga i Azure-stacken användarportalen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett erbjudande
> * Skapa en plan
> * Ange kvoter
> * Ange erbjudande till offentligt

Gå vidare till nästa kurs information om hur du prenumererar på det erbjudande som du just har skapat som en användare i Azure-stacken.

> [!div class="nextstepaction"]
> [Prenumerera på ett erbjudande](asdk-subscribe-services.md)