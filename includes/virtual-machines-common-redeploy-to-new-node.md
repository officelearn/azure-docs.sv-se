---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187348"
---
## <a name="use-the-azure-portal"></a>Använda Azure-portalen
1. Välj den virtuella dator som du vill distribuera om och välj sedan knappen *Distribuera om* i bladet *Inställningar.* Du kan behöva rulla nedåt för att se avsnittet **Support och felsökning** som innehåller knappen "Distribuera om" som i följande exempel:
   
    ![Azure VM-blad](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Om du vill bekräfta åtgärden väljer du knappen *Distribuera om:*
   
    ![Distribuera om ett VM-blad](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. **Status för** den virtuella datorn *ändras* till Uppdatering när den virtuella datorn förbereder omdedeling, vilket visas i följande exempel:
   
    ![Uppdatering av den virtuella datorn](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. **Status ändras** sedan till *Start* som den virtuella datorn startar på en ny Azure-värd, vilket visas i följande exempel:
   
    ![Start av virtuella datorer](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. När den virtuella datorn har slutfört startprocessen återgår **statusen** sedan till *Löpning*, vilket indikerar att den virtuella datorn har distribuerats om:
   
    ![Vm körs](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

