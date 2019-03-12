---
title: Skapa ett erbjudande i Azure Stack | Microsoft Docs
description: Lär dig hur du skapar ett erbjudande för dina användare i Azure Stack som en molnadministratör.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 42336205726823dd04e0412f29c3e7a23d134d39
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764001"
---
# <a name="create-an-offer-in-azure-stack"></a>Skapa ett erbjudande i Azure Stack

[Erbjuder](azure-stack-key-features.md) är grupper med en eller flera planer som leverantörer presenterar för användare som användarna kan köpa eller prenumerera på. Den här artikeln beskriver hur du skapar ett erbjudande som innehåller den [plan som du skapade](azure-stack-create-plan.md). Det här erbjudandet ger prenumeranter möjlighet att konfigurera virtuella datorer (VM).

## <a name="create-an-offer-1902-and-later"></a>Skapa ett erbjudande (1902 och senare)

1. Logga in på den [Azure Stack-administratörsportalen](https://adminportal.local.azurestack.external) och välj **+ skapa en resurs**, sedan **erbjuder + planer**, och sedan **erbjuder**.

   ![Skapa ett erbjudande](media/azure-stack-create-offer/offers.png)

2. Ett kodfragment med användargränssnitt visas där du kan definiera namnet på erbjudandet, och Lägg till befintlig eller skapa nya basplaner och tilläggsplaner. Viktigast av allt, kan du granska informationen i erbjudandet om du skapar, innan du bestämmer dig att skapa den.

   I den **grunderna** fliken, under **nytt erbjudande**, ange en **visningsnamn** och en **resursnamn**, och sedan under **resurs Gruppen**väljer **Skapa nytt** eller **Använd befintlig**. Visningsnamnet är det egna namnet för erbjudandet. Detta egna namn är den enda informationen om erbjudandet som användarna ser när de prenumererar på ett erbjudande i användarportalen. Använda en intuitiv som hjälper användarna att förstå vad som ingår i erbjudandet. Endast administratören kan se resursnamnet. Det är det namn som administratörer använder för att arbeta med erbjudandet som en Azure Resource Manager-resurs. I den här fliken kan välja du också att göra det här erbjudandet offentliga eller behåll dem privat, vilket är standard. Du kan [ändrar status för offentliga eller privata för erbjudandet](#change-the-state-of-an-offer) senare, och även.

   ![Nytt erbjudande](media/azure-stack-create-offer/new-offer.png)
  
3. Välj den **Basplaner** fliken. Välj den plan eller de planer som du vill inkludera i erbjudandet.

   ![Välj plan](media/azure-stack-create-offer/select-plan.png)

4. Nu kan du skapa en tilläggsplan för att ändra den grundläggande planen, men det här är valfritt. Vi kommer att skapa en tilläggsplanen i nästa artikel, [tilläggsplaner för Azure Stack](create-add-on-plan.md).

5. Välj den **granska + skapa** fliken. Granska sammanfattningen erbjudande för att säkerställa att alla värden är korrekta. Gränssnittet kan du expandera kvoter i de valda planerna, en i taget för att visa information om varje kvot i en plan och gå tillbaka för att göra alla nödvändiga ändringar.

6. Välj **skapa** att skapa erbjudandet.

   ![Granska och skapa](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Ändra tillståndet för ett erbjudande

Du kan ändra tillståndet när du har skapat erbjudandet. Erbjudanden måste göras **offentliga** för användare att få en fullständig vy när de prenumererar. Erbjudanden kan vara:

   - **Offentliga**: Synligt för användarna.
   - **Privata**: Endast synliga för administratörer i molnet. Den här inställningen är användbar vid utformningen av planen eller erbjudandet, eller om molnadministratören vill [skapa varje prenumeration för användare](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Ur**: Stängda för nya prenumeranter. Molnadministratören inaktivera erbjudanden för att förhindra framtida prenumerationer, men lämna befintliga prenumeranter påverkas inte.

   > [!TIP]  
   > Ändringar i erbjudandet är inte omedelbart synliga för användaren. Om du vill se ändringarna måste behöva användare logga ut och logga in igen på användarportalen att se det nya erbjudandet.

Det finns två sätt att ändra tillståndet för ett erbjudande:

1. I **alla resurser**, Välj namnet på erbjudandet. På den **översikt** skärmen för erbjudandet, väljer **ändra tillståndet**. Välj stat som du vill använda (till exempel **offentliga**).

   ![Välj stat](media/azure-stack-create-offer/change-state.png)

2. I **alla resurser**, Välj namnet på erbjudandet. Välj sedan **Erbjudandeinställningar**. Välj stat som du vill använda (till exempel **offentliga**) och välj sedan **spara**.

   ![Välj tillstånd för hjälpmedel](media/azure-stack-create-offer/offer-settings.png)

## <a name="create-an-offer-1901-and-earlier"></a>Skapa ett erbjudande (1901 och tidigare)

1. Logga in på den [Azure Stack-administratörsportalen](https://adminportal.local.azurestack.external) och välj **+ skapa en resurs**, sedan **Klienterbjudanden och planer**, och sedan **erbjuder**.

   ![Skapa ett erbjudande](media/azure-stack-create-offer/image01.png)
  
2. Under **nytt erbjudande**, ange en **visningsnamn** och en **resursnamn**, och sedan under **resursgrupp**väljer **skapa ny** eller **Använd befintlig**. Visningsnamnet är det egna namnet för erbjudandet. Detta egna namn är den enda informationen om erbjudandet som användarna ser när de prenumererar på ett erbjudande. Använda en intuitiv som hjälper användarna att förstå vad som ingår i erbjudandet. Endast administratören kan se resursnamnet. Det är det namn som administratörer använder för att arbeta med erbjudandet som en Azure Resource Manager-resurs.

   ![Nytt erbjudande](media/azure-stack-create-offer/image01a.png)
  
3. Välj **Basplaner** att öppna den **planera**. Välj planer som du vill inkludera i erbjudandet och välj sedan **Välj**. Att skapa erbjudandet väljer **skapa**.

   ![Välj plan](media/azure-stack-create-offer/image02.png)
  
4. Du kan ändra tillståndet när du har skapat erbjudandet. Erbjudanden måste göras **offentliga** för användare att få en fullständig vy när de prenumererar. Erbjudanden kan vara:

   - **Offentliga**: Synligt för användarna.
   - **Privata**: Endast synliga för administratörer i molnet. Den här inställningen är användbar vid utformningen av planen eller erbjudandet, eller om molnadministratören vill [skapa varje prenumeration för användare](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Ur**: Stängda för nya prenumeranter. Molnadministratören inaktivera erbjudanden för att förhindra framtida prenumerationer, men lämna befintliga prenumeranter påverkas inte.

   > [!TIP]  
   > Ändringar i erbjudandet är inte omedelbart synliga för användaren. Om du vill se ändringarna måste behöva användare logga ut och logga in igen på användarportalen att se det nya erbjudandet.

   På sidan Översikt för erbjudandet **tillstånd för hjälpmedel**. Välj stat som du vill använda (till exempel **offentliga**), och välj sedan **spara**.

     ![Välj stat](media/azure-stack-create-offer/change-stage-1807.png)

     Alternativt kan du välja **ändra tillståndet** och välj sedan ett tillstånd.

    ![Välj tillstånd för hjälpmedel](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> Du kan också använda PowerShell för att skapa standarderbjudanden, planer och kvoter. Mer information finns i [Azure Stack PowerShell-modulen 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).

## <a name="next-steps"></a>Nästa steg

- [Skapa prenumerationer](azure-stack-subscribe-plan-provision-vm.md)
- [Etablera en virtuell dator](azure-stack-provision-vm.md)
