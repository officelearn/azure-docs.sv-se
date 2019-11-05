---
title: Konfigurera haveriberedskap för en virtuell Azure-dator till en sekundär Azure-region
description: Den här snabbstarten beskriver de steg som krävs för haveriberedskap för en virtuell Azure IaaS-dator mellan Azure-regioner, med hjälp av Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b06cae5bcb07831ba79b805802a7851c2b6ad2b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467998"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Konfigurera haveriberedskap för en sekundär Azure-region för en virtuell Azure-dator        

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra dem vid planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

I den här snabb starten beskrivs hur du konfigurerar haveri beredskap för en virtuell Azure-dator genom att replikera den till en annan Azure-region.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Den här artikeln är en snabb genom gång för nya användare. Den använder den enklaste sökvägen, med standard alternativ och minimal anpassning.  En fullständig genom gång finns i [vår självstudie](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Aktivera replikering för virtuella Azure-datorer

1. Välj **virtuella datorer**på Azure Portal-menyn, eller Sök efter och välj *virtuella datorer* på vilken sida som helst. Välj den virtuella dator som du vill replikera.
2. I **Åtgärder** väljer du **Haveriberedskap**.
3. I **Konfigurera haveriberedskap** > **Målregion** väljer du den målregion som du ska replikera till.
4. Acceptera de andra standardinställningarna för denna Snabbstart.
5. Välj **Granska + starta replikering**. Välj sedan **starta replikering** för att starta ett jobb för att aktivera replikering för den virtuella datorn.

    ![aktivera replikering](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Kontrollera inställningarna

När replikeringen har slutförts kan du kontrollera replikeringsstatus, ändra inställningar för replikering och testa distributionen.

1. Välj **virtuella datorer**på Azure Portal-menyn, eller Sök efter och välj *virtuella datorer* på vilken sida som helst. Välj den virtuella dator som du vill verifiera.
2. I **Åtgärder** väljer du **Haveriberedskap**.

   Du kan kontrollera replikeringshälsan, återställningspunkter som har skapats samt käll- och målregioner på kartan.

   ![Replikeringsstatus](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Rensa resurser

Den virtuella datorn i den primära regionen slutar replikera när du inaktiverar replikering för den:

- Källans replikeringsinställningar rensas automatiskt. Site Recovery tillägget som är installerat på den virtuella datorn som en del av replikeringen tas inte bort och måste tas bort manuellt. 
- Site Recovery faktureringen för den virtuella datorn stoppas.

Stoppa replikering på följande sätt

1. Välj **virtuella datorer**på Azure Portal-menyn, eller Sök efter och välj *virtuella datorer* på vilken sida som helst. Välj den virtuella dator som du vill ändra.
2. I **haveri beredskap**väljer du **inaktivera replikering**.

   ![Inaktivera replikering](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten replikerar du en enda virtuell dator till en sekundär region. Försök nu att replikera flera virtuella Azure-datorer med en återställnings plan.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap för virtuella Azure-datorer](azure-to-azure-tutorial-enable-replication.md)
