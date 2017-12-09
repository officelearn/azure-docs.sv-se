---
title: "Replikera en virtuell Azure-dator till en annan Azure-region (förhandsgranskning)"
description: "Denna Snabbstart innehåller de steg som krävs för att replikera en virtuell Azure-dator i en Azure-region till en annan region."
services: site-recovery
author: rajani-janaki-ram
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 369ffed823bc76ee4273d7866935c0ddc7ffa515
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="replicate-an-azure-vm-to-another-azure-region-preview"></a>Replikera en virtuell Azure-dator till en annan Azure-region (förhandsgranskning)

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten bidrar till din affärskontinuitet och haveriberedskap (BCDR) strategi genom att hålla dina appar och köra tillgängliga under planerade och oplanerade avbrott. Site Recovery samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer (VM), inklusive replikering, redundans och återställning hanterar.

Denna Snabbstart beskriver hur du replikera en virtuell Azure-dator till en annan Azure-region.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Aktivera replikering för den virtuella Azure-datorn

1. I Azure-portalen klickar du på **virtuella datorer**, och välj den virtuella datorn som du vill replikera.

2. I **inställningar**, klickar du på **Disaster recovery (förhandsgranskning)**.
3. I **konfigurera katastrofåterställning** > **målregionen** väljer du den mål-region som du replikerar.
4. Acceptera de andra standardinställningarna för denna Snabbstart.
5. Klicka på **Aktivera replikering**. Detta startar ett jobb för att aktivera replikering för den virtuella datorn.

    ![Aktivera replikering](media/azure-to-azure-quickstart/enable-replication1.png)



## <a name="verify-settings"></a>Kontrollera inställningarna

När replikeringen jobbet har slutförts måste du kontrollera replikeringsstatus, ändra inställningar för replikering och testa distributionen.

1. VM-menyn klickar du på **Disaster recovery (förhandsgranskning)**.
2. Du kan verifiera replikeringshälsa återställningspunkter som har skapats och källa och mål-regioner på kartan.

   ![Replikeringsstatus](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Rensa resurser

Den virtuella datorn i den primära regionen stoppar replikera när du inaktivera replikering för den:

- Inställningar för datakälla replikering rensas automatiskt.
- Site Recovery-faktureringen för den virtuella datorn också stoppar.

Stoppa replikeringen på följande sätt:

1. Välj den virtuella datorn.
2. I **Disaster recovery (förhandsgranskning)**, klickar du på **mer**.
3. Klicka på **inaktivera replikering**.

   ![Inaktivera replikering](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten replikeras en enda virtuell dator till en sekundär region.

> [!div class="nextstepaction"]
> [Konfigurera katastrofåterställning för virtuella Azure-datorer](azure-to-azure-tutorial-enable-replication.md)
