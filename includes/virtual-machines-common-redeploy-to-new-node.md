---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187348"
---
## <a name="use-the-azure-portal"></a>Använda Azure-portalen
1. Välj den virtuella dator som du vill distribuera igen och välj sedan knappen *distribuera* på bladet *Inställningar* . Du kan behöva rulla nedåt för att se avsnittet **support och fel sökning** som innehåller knappen distribuera igen som i följande exempel:
   
    ![Bladet virtuell Azure-dator](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Bekräfta åtgärden genom att välja knappen *distribuera* om:
   
    ![Distribuera om ett virtuellt dator blad](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. **Status** för den virtuella datorn ändras till att *uppdateras* när den virtuella datorn förbereds omdistribueras, vilket visas i följande exempel:
   
    ![VM-uppdatering](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. **Statusen** ändras sedan till att *börja* när den virtuella datorn startas på en ny Azure-värd, vilket visas i följande exempel:
   
    ![Virtuell dator startas](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. När den virtuella datorn har slutfört start processen återgår **statusen** till att *köras*, vilket indikerar att den virtuella datorn har omdistribuerats:
   
    ![Virtuell dator körs](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

