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
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 230415b84f06beac549693d49055d7cb998163d8
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251460"
---
# <a name="create-an-offer-in-azure-stack"></a>Skapa ett erbjudande i Azure Stack

[Erbjuder](azure-stack-key-features.md) är grupper med en eller flera planer som leverantörer presenterar för användare som användarna kan köpa eller prenumerera på. Den här artikeln beskriver hur du skapar ett erbjudande som innehåller den [plan som du skapade](azure-stack-create-plan.md). Det här erbjudandet ger prenumeranter möjlighet att konfigurera virtuella datorer (VM).

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
