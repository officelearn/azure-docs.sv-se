---
title: Skapa en test VM i Azure-stacken | Microsoft Docs
description: "Lär dig hur du etablerar ett test VM i Azure-stacken som en moln-operator."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: erikje
ms.openlocfilehash: 233cf4df53af6a49e5fe4c5d51e112d8196a7530
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>Skapa en virtuell testdator i Azure-stacken

*Gäller för: Azure stacken Development Kit*

Som operatör Azure Stack du kan skapa en virtuell testdator för att verifiera din [Azure Stack](azure-stack-poc.md) Developer Kit distribution.

> [!NOTE]
> Innan du kan distribuera virtuella datorer, måste du [lägga till utvärdering av Windows Server 2016-avbildningen till Azure-stacken marketplace](azure-stack-add-default-image.md).
> 
> 

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
1. På Azure-stacken Development Kit-värden [logga in](azure-stack-connect-azure-stack.md) till administratörsportalen (`https://adminportal.local.azurestack.external`), och klicka sedan på **ny** > **Compute**  >  **Windows Server 2016 Datacenter Eval** > **skapa**.  
2. I den **grunderna** bladet typ a **namn**, **användarnamn**, och **lösenord**. Välj en **prenumeration**. Skapa en **resursgruppen**, eller välj en befintlig och klicka sedan på **OK**.  
3. I den **välja en storlek** bladet, klickar du på **A1 Standard**, och klicka sedan på **Välj**.  
4. I den **inställningar** bladet acceptera standardinställningarna och klickar på **OK**
5. Klicka **OK** på bladet **Sammanfattning** för att skapa den virtuella datorn.  
6. Klicka för att visa den nya virtuella datorn **alla resurser**, Sök sedan efter den virtuella datorn och klickar på dess namn.
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>Nästa steg
[Med hjälp av administratörs- och portaler i Azure-stacken](azure-stack-manage-portals.md)
