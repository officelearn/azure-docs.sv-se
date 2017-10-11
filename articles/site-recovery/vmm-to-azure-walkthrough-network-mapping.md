---
title: "Konfigurera nätverksmappning för att replikera virtuella Hyper-V-datorer i VMM-moln till Azure med Azure Site Recovery | Microsoft Docs"
description: "Beskriver hur du konfigurerar nätverksmappning vid replikering av Hyper-V virtuella datorer i VMM-moln till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ed6f73d8baea5af0d2aa5f0ae885f305911ccc82
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2017
---
# <a name="step-9-configure-network-mapping-for-hyper-v-replication-with-vmm-to-azure"></a>Steg 9: Konfigurera nätverksmappning för Hyper-V-replikering (med VMM) till Azure

När du har skapat den [käll- och replikeringsinställningarna](vmm-to-azure-walkthrough-source-target.md), Använd den här artikeln för att konfigurera nätverksmappning att mappa mellan lokala nätverk för virtuell dator i VMM och Azure-nätverk.

Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Innan du börjar

- Lär dig mer om [nätverksmappning](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- [Förbereda VMM för nätverksmappning](vmm-to-azure-walkthrough-network.md#prepare-vmm-for-network-mapping). 
- Kontrollera att virtuella datorer på VMM-servern är anslutna till ett virtuellt datornätverk och att du har skapat minst ett virtuellt Azure-nätverk. Flera virtuella datornätverk kan mappas till ett enda Azure-nätverk.

## <a name="configure-mapping"></a>Konfigurera mappning

Konfigurera mappning på följande sätt:

1. Under **Site Recovery-infrastruktur** > **Nätverksmappningar** > **Nätverksmappning** klickar du på ikonen **+Nätverksmappning**.

    ![Nätverksmappning](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping1.png)
2. På **Lägg till nätverksmappning** väljer du VMM-källservern och **Azure** som mål.
3. Kontrollera prenumerationen och distributionsmodellen efter redundansväxling.
4. I **Källnätverk** väljer du det lokala VM-källnätverk som du vill mappa från listan som är associerad med VMM-servern.
5. I **Målnätverk** väljer du det Azure-nätverk som de virtuella Azure-replikdatorerna ska anslutas till när de skapas. Klicka sedan på **OK**.

    ![Nätverksmappning](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping2.png)

Det här händer när nätverksmappningen börjar:

* Befintliga virtuella datorerna i VM-källnätverket ansluts till målnätverket när mappningen börjar. Nya virtuella datorer som är anslutna till VM-källnätverket ansluts till det mappade Azure-nätverket när replikeringen sker.
* Om du ändrar en befintlig nätverksmappning ansluts virtuella replikdatorer med hjälp av de nya inställningarna.
* Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns så ansluts den virtuella replikdatorn till det målundernätverket efter en redundansväxling.
* Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.



## <a name="next-steps"></a>Nästa steg

Gå till [steg 10: skapa en replikeringsprincip](vmm-to-azure-walkthrough-replication.md)
