---
title: Skapa ett erbjudande på Azure-stacken | Microsoft Docs
description: Som en molnadministratören lär du dig hur du skapar ett erbjudande för dina användare i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.openlocfilehash: e5b96a9464bf4d0e3b69d2f635da32c6648ce793
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247525"
---
# <a name="create-an-offer-in-azure-stack"></a>Skapa ett erbjudande i Azure Stack

[Erbjuder](azure-stack-key-features.md) är grupper med en eller flera serviceplaner providers presentera för användare att köpa eller prenumerera på. Det här dokumentet beskrivs hur du skapar ett erbjudande som innehåller den [plan som du skapade](azure-stack-create-plan.md). Det här erbjudandet ger prenumeranter möjlighet att konfigurera virtuella datorer.

1. Logga in på Azure Stack-administratörsportalen (https://adminportal.local.azurestack.external) och välj **ny** > **klient erbjuder + planer** > **erbjuder**.

   ![Skapa ett erbjudande](media/azure-stack-create-offer/image01.png)
  
2. Under **nya erbjuder**, ange en **visningsnamn** och en **resursnamnet**, och sedan under **resursgruppen**väljer **skapa nya** eller **använda befintliga**. Visningsnamnet är det egna namnet för erbjudande. Detta egna namn är den enda informationen om erbjudandet som användarna ser när de prenumererar på ett erbjudande. Använda en intuitiv som hjälper användarna att förstå vad medföljer erbjudandet. Endast administratören kan se resursnamnet. Det är det namn som administratörer använder för att arbeta med erbjudandet som en Azure Resource Manager-resurs.

   ![Nya erbjudande](media/azure-stack-create-offer/image01a.png)
  
3. Välj **basera planer** att öppna den **planera**. Välj de planer som du vill inkludera i erbjudandet och välj sedan **Välj**. Att skapa erbjudande Välj **skapa**.

   ![Välj plan](media/azure-stack-create-offer/image02.png)
  
4. När du har skapat erbjudandet kan du ändra dess tillstånd. Erbjudanden måste göras *offentliga* för användare att få fullständig vy när man prenumererar. Erbjudanden kan vara:

   - **Offentliga**: synliga för användare.
   - **Privata**: endast synliga till administratörer i molnet. Den här inställningen är användbar när du utformar plan eller erbjudande eller om molnadministratören vill [skapa varje prenumeration för användare](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Inaktiverad**: Stängda för nya prenumeranter. Molnadministratören kan använda inaktiverade för att förhindra framtida prenumerationer, men lämna aktuella prenumeranter påverkas inte.

   > [!TIP]  
   > Ändringar av erbjudandet är inte direkt synliga för användare. Om du vill se ändringarna behöva användare logga ut och logga in på användarportalen att se nya erbjudandet.

   Att ändra tillståndet för erbjudandet:

   - **Version 1803 och senare**:  
     Markera på Översikt för erbjudande **hjälpmedel tillstånd**. Välj det tillstånd som du vill använda (till exempel *offentliga*) och välj sedan **spara**.
 
     ![Välj hjälpmedel tillstånd](media/azure-stack-create-offer/change-state.png)

     Som ett alternativ när du har åtkomst till ett erbjudande går du till **erbjuder inställningar**. Välj **hjälpmedel tillstånd** att ändra tillståndet.

   - **Före version 1803**:  
     Välj **alla resurser**, söka efter nya erbjudandet och välj sedan det nya erbjudanden. Välj **ändring av tillstånd**, och välj sedan **offentliga**.

   > [!NOTE]
   > Du kan också använda PowerShell för att skapa standarderbjudanden, planer och kvoter. Mer information finns i [tjänstadministratör för Azure-stacken viktigt](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).

## <a name="next-steps"></a>Nästa steg

- [Skapa prenumerationer](azure-stack-subscribe-plan-provision-vm.md)
- [Etablera en virtuell dator](azure-stack-provision-vm.md)
