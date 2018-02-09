---
title: "Skapa ett erbjudande på Azure-stacken | Microsoft Docs"
description: "Som en molnadministratören lär du dig hur du skapar ett erbjudande för dina användare i Azure-stacken."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 2666aacbbaf44ae9b3bcf2df480e835457e6f449
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Skapa ett erbjudande i Azure Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

[Erbjuder](azure-stack-key-features.md) är grupper med en eller flera serviceplaner providers presentera för användare att köpa eller prenumerera på. Det här dokumentet beskrivs hur du skapar ett erbjudande som innehåller den [plan som du skapade](azure-stack-create-plan.md) i det sista steget. Det här erbjudandet ger prenumeranter möjligheten att etablera virtuella datorer.

1. Logga in på Azure Stack-administratörsportalen (https://adminportal.local.azurestack.external) > klickar du på **ny** > **klient erbjuder + planer**  >  ** Erbjuder**.

   ![](media/azure-stack-create-offer/image01.png)
2. I den **nya erbjuder** rutan, fyller du i **visningsnamn** och **resursnamnet**, och välj en ny eller befintlig **resursgruppen**. Visningsnamnet är det egna namnet för erbjudande. Detta egna namn är den enda informationen om erbjudandet som användarna ser när prenumerera. Därför bör du använda ett intuitivt namn som hjälper användaren att förstå vad medföljer erbjudandet. Endast administratören kan se resursnamnet. Det är det namn som administratörer använder för att arbeta med erbjudandet som en Azure Resource Manager-resurs.

   ![](media/azure-stack-create-offer/image01a.png)
3. Klicka på **basera planer** att öppna den **planera** fönstret Välj planer som du vill inkludera i erbjudandet och klicka sedan på **Välj**. Klicka på **Skapa** för att skapa erbjudandet.

   ![](media/azure-stack-create-offer/image02.png)
4. Klicka på **alla resurser**, söka efter nya erbjudandet, klicka på ny erbjudandet, klickar du på **ändring av tillstånd**, och klicka sedan på **offentliga**.

   ![](media/azure-stack-create-offer/image03.png)

Erbjudanden måste göras offentlig för användare att få fullständig vyn när prenumerera. Erbjudanden kan vara:

* **Offentliga**: synliga för användare.
* **Privata**: endast synliga moln-administratörer. Användbart vid utformning plan eller erbjudande eller om molnadministratören vill [skapa varje prenumeration för användare](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
* **Inaktiverad**: Stängda för nya prenumeranter. Molnadministratören kan använda inaktiverade för att förhindra framtida prenumerationer, men lämna aktuella prenumeranter orörd.

Ändringar av erbjudandet visas inte direkt till användaren. Om du vill se ändringarna du kan behöva logga ut/inloggningen om du vill se den nya prenumerationen i Väljaren ”prenumeration” när du skapar resurser/resursgrupper.

> [!NOTE]
>Du kan också skapa standarderbjudanden, planer och kvoter med hjälp av PowerShell som beskrivs i den [tjänstadministratör för Azure-stacken viktigt](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).
>


### <a name="next-steps"></a>Nästa steg
[Skapa prenumerationer](azure-stack-subscribe-plan-provision-vm.md)      
[Etablera en virtuell dator](azure-stack-provision-vm.md)
