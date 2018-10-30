---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227146"
---
## <a name="use-the-azure-portal"></a>Använda Azure-portalen
1. Välj den virtuella datorn som du vill distribuera om, och välj sedan den *omdistribuera* knappen i den *inställningar* bladet. Du kan behöva rulla ned för att se den **Support och felsökning** avsnitt som innehåller knappen ”distribuera om, som i följande exempel:
   
    ![Azure VM-bladet](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. För att bekräfta åtgärden, Välj den *omdistribuera* knappen:
   
    ![Distribuera om en VM-bladet](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. Den **Status** för den virtuella datorn ändras till *uppdaterar* som den virtuella datorn förbereder att distribuera om, som visas i följande exempel:
   
    ![Uppdatering av virtuell dator](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. Den **Status** ändras till *startar* som den virtuella datorn som startas på en ny Azure-värd som du ser i följande exempel:
   
    ![Virtuell dator startar](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. När den virtuella datorn är klar startprocessen, den **Status** återgår sedan till *kör*, som anger den virtuella datorn har varit distribuerades om:
   
    ![Virtuell dator körs](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

