---
title: "Konfigurera en replikeringsprincip för fysisk serverreplikering till Azure med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattas de steg som du måste ställa in en replikeringsprincip vid replikering av lokala fysiska servrar till Azure-lagring med hjälp av Azure Site Recovery-tjänsten"
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
ms.openlocfilehash: 9ce23382001b54e7b9b7a58b8dd9aa61b400826d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-a-replication-policy-for-physical-server-replication-to-azure"></a>Steg 8: Skapa en replikeringsprincip för fysisk serverreplikering till Azure


Den här artikeln beskriver hur du ställer in en replikeringsprincip när du replikerar fysiska Windows-/ Linux-servrar till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.


Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="configure-a-policy"></a>Konfigurera en princip

1. Klicka på **Site Recovery-infrastruktur** > **replikeringsprinciper** > **+ replikeringsprincip**.
2. I **skapa replikeringsprincip**, ange ett principnamn.
3. I **tröskelvärdet för RPO** anger du RPO (mål för återställningspunkt)-gränsen. Det här värdet anger hur ofta data återställningspunkter skapas. En avisering genereras om kontinuerlig replikering överskrider den gränsen.
4. I **kvarhållningstid för återställningspunkten**, ange (i timmar) hur lång kvarhållningsperiod är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster. Upp till 24 timmar kvarhållning har stöd för datorer som replikeras till premium-lagring och 72 timmar för standardlagring.
5. I **frekvens av programkonsekventa ögonblicksbilder**, ange hur ofta (i minuter) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Klicka på **OK** vill skapa principen.

    ![Replikeringsprincip](./media/physical-walkthrough-replication/gs-replication2.png)
8. När du skapar en ny princip associeras den automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om exempelvis replikeringsprinciper är **rep-policy** sedan principen för återställning kommer att **rep-princip-återställning**. Den här principen används inte förrän du har initierat en återställning från Azure.

## <a name="next-steps"></a>Nästa steg

Gå till [steg 9: Installera mobilitetstjänsten](physical-walkthrough-install-mobility.md)
