---
title: "Konfigurera en replikeringsprincip för VMware VM replikering till Azure med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattas de steg som du måste ställa in en replikeringsprincip vid replikering av virtuella VMware-datorer till Azure-lagring"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d7874bd8-6626-4668-9ec9-dbd2d26f8f81
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 3c4b7ad16e6a03fb605447def18f7475d502fdd1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="step-9-set-up-a-replication-policy-for-vmware-vm-replication-to-azure"></a>Steg 9: Konfigurera en replikeringsprincip för VMware VM replikering till Azure


Den här artikeln beskriver hur du ställer in en replikeringsprincip när du replikerar virtuella VMware-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.


Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="configure-a-policy"></a>Konfigurera en princip

Få en snabb överblick av video innan du börjar:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. Klicka för att skapa en ny replikeringsprincip **Site Recovery-infrastruktur** > **replikeringsprinciper** > **+ replikeringsprincip**.
2. I **skapa replikeringsprincip**, ange ett principnamn.
3. I **tröskelvärdet för RPO** anger du RPO (mål för återställningspunkt)-gränsen. Det här värdet anger hur ofta data återställningspunkter skapas. En avisering genereras om kontinuerlig replikering överskrider den gränsen.
4. I **kvarhållningstid för återställningspunkten**, ange (i timmar) hur lång kvarhållningsperiod är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster. Upp till 24 timmar kvarhållning har stöd för datorer som replikeras till premium-lagring och 72 timmar för standardlagring.
5. I **frekvens av programkonsekventa ögonblicksbilder**, ange hur ofta (i minuter) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Klicka på **OK** vill skapa principen.

    ![Replikeringsprincip](./media/vmware-walkthrough-replication/gs-replication2.png)
8. När du skapar en ny princip associeras den automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om exempelvis replikeringsprinciper är **rep-policy** sedan principen för återställning kommer att **rep-princip-återställning**. Den här principen används inte förrän du har initierat en återställning från Azure.

## <a name="next-steps"></a>Nästa steg

Gå till [steg 10: Installera mobilitetstjänsten](vmware-walkthrough-install-mobility.md)
