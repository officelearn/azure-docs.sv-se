---
title: "Skapa ett valv för Hyper-V-replikering till en sekundär plats med Azure Site Recovery | Microsoft Docs"
description: "Beskriver hur du skapar ett valv vid replikering av Hyper-V virtuella datorer till en sekundär plats för System Center VMM med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ff65dbfb-cb26-410e-ab48-76971625db08
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 28cfcf12b2e369f96664c163c0b6f2aa8a6ddcb9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="step-5-create-a-vault-for-hyper-v-replication-to-a-secondary-site"></a>Steg 5: Skapa ett valv för Hyper-V-replikering till en sekundär plats

När du har förberett lokalt [System Center Virtual Machine Manager (VMM) servrar och Hyper-V-värdar/kluster](vmm-to-vmm-walkthrough-vmm-hyper-v.md) för Hyper-V-replikering till en sekundär plats med hjälp av [Azure Site Recovery](site-recovery-overview.md), du kan skapa ett Recovery Services-valvet och välj scenario för replikering.

När du har läst den här artikeln kan du lämna kommentarer längst ned på sidan eller på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Välj en skyddsmål

Välj vad och vart du vill replikera.

1. Klicka på **Site Recovery** > **steg 1: Förbered infrastrukturen** > **skyddsmål**.
2. Välj **till återställningsplatsen**, och välj **Ja, med Hyper-V**.
3. Välj **Ja** att indikera att du använder VMM för att hantera Hyper-V-värdar.
4. Välj **Ja** om du har en sekundär VMM-server. Om du distribuerar replikering mellan moln på en VMM-server, klickar du på **nr**. Klicka sedan på **OK**.

    ![Välja mål](./media/vmm-to-vmm-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Nästa steg

Gå till [steg 6: Konfigurera replikering käll- och](vmm-to-vmm-walkthrough-source-target.md).