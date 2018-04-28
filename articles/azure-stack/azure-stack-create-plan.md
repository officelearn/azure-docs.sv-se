---
title: Skapa en plan i Azure-stacken | Microsoft Docs
description: Skapa en plan som låter prenumeranter etablera virtuella datorer som en moln-administratör.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: b1bfff16c4f51a9fa53204930df78cbd2cf19b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Skapa en plan i Azure Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

[Planer](azure-stack-key-features.md) är grupperingar av en eller flera tjänster. Du kan skapa planer för att erbjuda användarna som en provider. I sin tur prenumerera användarna på dina erbjudanden använda planer och tjänster som de innehåller. Det här exemplet visar hur du skapar en plan som innehåller beräknings-, nätverks- och lagringsresurs-providers. Den här planen kan prenumeranter att etablera virtuella datorer.

1. Logga in på Azure Stack-administratörsportalen (https://adminportal.local.azurestack.external).

2. Skapa en plan och som användare kan prenumerera på, välja **ny** > **erbjuder + planer** > **Plan**.  
   ![Välj en plan](media/azure-stack-create-plan/select-plan.png)

3. I den **ny plan** rutan, fyller du i **visningsnamn** och **resursnamnet**. Visningsnamnet är planens eget namn som visas för användarna. Endast administratören kan se resursnamnet är det namn som administratörer använder för att arbeta med planen som en Azure Resource Manager-resurs.  
   ![Ange information](media/azure-stack-create-plan/plan-name.png)

4. Skapa en ny **resursgruppen**, eller välj en befintlig som en behållare för planen.  
   ![Ange resursgrupp](media/azure-stack-create-plan/resource-group.png)

5. Välj **Services** och markera sedan kryssrutan för **Microsoft.Compute**, **Microsoft.Network**, och **Microsoft.Storage**. Därefter väljer du **Välj** att spara konfigurationen. Kryssrutorna visas när muspekaren hålls över varje alternativ.  
   ![Välj tjänster](media/azure-stack-create-plan/services.png)

6. Välj **kvoter**, **Microsoft.Storage (lokal)**, och välj sedan den standardkvot eller välj **skapa nya kvoten** att anpassa kvoten.  
   ![Kvoter](media/azure-stack-create-plan/quotas.png)

7. Om du skapar en ny kvot, ange en **namn** för kvoten > Ange kvotvärden > Välj **OK**. Den **Skapa kvot** fönstret stängs.
   ![Nya kvoten](media/azure-stack-create-plan/new-quota.png)

   Sedan kan du välja den nya kvoten som du skapade. Om du väljer kvoten tilldelar den och stänger fönstret val.  
   ![Tilldela kvoten](media/azure-stack-create-plan/assign-quota.png)

8. Upprepa steg 6 och 7 för att skapa och tilldela kvoter för **Microsoft.Network (lokal)** och **Microsoft.Compute (lokal)**.  När alla tre tjänster har tilldelade kvoter, visas de liknar följande bild.  
   ![Fullständig kvoten tilldelningar](media/azure-stack-create-plan/all-quotas-assigned.png)

9. I den **kvoter** fönstret Välj **OK**, och klicka sedan på den **ny plan** fönstret Välj **skapa** att skapa planen.  
    ![Skapa planen](media/azure-stack-create-plan/create.png)
10. Om du vill se nya planen väljer **alla resurser**, Sök sedan efter planen och markera dess namn. Om din lista över resurser är lång använder **Sök** söka efter din plan efter namn.  
   ![Granska planen](media/azure-stack-create-plan/plan-overview.png)

### <a name="next-steps"></a>Nästa steg
[Skapa ett erbjudande](azure-stack-create-offer.md)
