---
title: Skapa en plan i Azure Stack | Microsoft Docs
description: Skapa en plan som låter prenumeranter etablera virtuella datorer som en molnadministratör.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 7a3a87c70402374bea6357d1b89a19938ca08868
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243533"
---
# <a name="create-a-plan-in-azure-stack"></a>Skapa en plan i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

[Planer](azure-stack-key-features.md) är grupperingar av en eller flera tjänster. Som en provider, kan du skapa planer för att erbjuda användarna. Användarna prenumererar i sin tur dina erbjudanden för att använda de planer och tjänster som ingår. Det här exemplet visar hur du skapar en plan som innehåller beräknings-, nätverks- och lagringsresurs-providers. Den här planen kan prenumeranter att etablera virtuella datorer.

1. Logga in på den [Azure Stack-administratörsportalen](https://adminportal.local.azurestack.external).

2. Skapa en plan och erbjudande som användare kan prenumerera på, välja **+ skapa en resurs**, sedan **erbjuder + planer**, sedan **Plan**.
  
   ![Välj en plan](media/azure-stack-create-plan/select-plan.png)

3. Under **ny plan**, ange en **visningsnamn** och en **resursnamn**. Visningsnamnet är planens eget namn som användarna kan se. Endast administratören kan se resursnamnet som administratörer använder för att arbeta med planen som en Azure Resource Manager-resurs.

   ![Ange information om](media/azure-stack-create-plan/plan-name.png)

4. Skapa en ny **resursgrupp**, eller välj en befintlig som en behållare för planen.

   ![Ange resursgrupp](media/azure-stack-create-plan/resource-group.png)

5. Välj **Services** och markera sedan kryssrutan för **Microsoft.Compute**, **Microsoft.Network**, och **Microsoft.Storage**. Välj sedan **Välj** att spara konfigurationen. Kryssrutorna visas när muspekaren hålls över varje alternativ.
  
   ![Välj tjänster](media/azure-stack-create-plan/services.png)

6. Välj **kvoter**, **Microsoft.Storage (lokal)**, och välj sedan den standardkvot eller välj **Skapa ny kvot** att skapa en anpassad kvot.
  
   ![Kvoter](media/azure-stack-create-plan/quotas.png)

7. Om du skapar en ny kvot, ange en **namn** för kvoten > Ange kvotvärden > Välj **OK**. Den **Skapa kvot** ruta stängs.

   ![Ny kvot](media/azure-stack-create-plan/new-quota.png)

   Sedan kan du välja den nya kvoten som du skapade. Att välja kvoten tilldelar den och stänger dialogrutan för val av.
  
   ![Tilldela kvoten](media/azure-stack-create-plan/assign-quota.png)

8. Upprepa steg 6 och 7 för att skapa och tilldela kvoter för **Microsoft.Network (lokal)** och **Microsoft.Compute (lokal)**. När alla tre tjänster har tilldelningar, får de se ut som i nästa exempel.

   ![Fullständig kvot tilldelningar](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Under **kvoter**, Välj **OK**, och sedan under **ny plan**, Välj **skapa** för att skapa planen.

    ![Skapa planen](media/azure-stack-create-plan/create.png)

10. Om du vill se din nya plan **alla resurser**, Sök sedan efter planen och markerar du dess namn. Om din lista över resurser är lång använder **Search** söka efter din plan efter namn.

   ![Granska planen](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett erbjudande](azure-stack-create-offer.md)
