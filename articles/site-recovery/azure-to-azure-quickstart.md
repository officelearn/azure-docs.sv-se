---
title: Konfigurera haveriberedskap för Azure VM till en sekundär region med Azure Site Recovery
description: Konfigurera snabbt haveriberedskap till en annan Azure-region för en Virtuell Azure-dator med hjälp av Azure Site Recovery-tjänsten.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: e26c2a1f24a88dc979f4ec68de65afc618740c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371885"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Snabbstart: Konfigurera haveriberedskap till en sekundär Azure-region för en virtuell Azure-dator

[Azure Site Recovery-tjänsten](site-recovery-overview.md) bidrar till din strategi för affärskontinuitet och haveriberedskap (BCDR) genom att hålla dina affärsprogram online under planerade och oplanerade avbrott. Site Recovery hanterar och dirigerar haveriberedskap av lokala datorer och virtuella Azure-datorer (VM), inklusive replikering, redundans och återställning.

Den här snabbstarten beskriver hur du konfigurerar haveriberedskap för en Azure-dator genom att replikera den till en sekundär Azure-region. I allmänhet används standardinställningar för att aktivera replikering.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du en Azure-prenumeration och en virtuell dator.

- Om du inte har ett Azure-konto med en aktiv prenumeration kan du [skapa ett konto kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En virtuell dator med minst 1 GB RAM rekommenderas. [Läs mer](/azure/virtual-machines/windows/quick-create-portal) om hur du skapar en virtuell dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Aktivera replikering för virtuella Azure-datorer

Följande steg aktiverar VM-replikering till en sekundär plats.

1. På Azure-portalen, från **Hem** > **virtuella datorer** menyn, välj en virtuell dator för att replikera.
1. I **Operationer** väljer du **Haveriberedskap**.
1. Välj målregionen i**målområdet** **Basics.** > 
1. Om du vill visa replikeringsinställningarna väljer du **Granska + Starta replikering**. Om du behöver ändra några standardinställningar väljer du **Avancerade inställningar**.
1. Om du vill starta jobbet som aktiverar VM-replikering väljer **du Starta replikering**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Aktivera replikering.":::

## <a name="verify-settings"></a>Kontrollera inställningarna

När replikeringsjobbet är klart kan du kontrollera replikeringsstatus, ändra replikeringsinställningar och testa distributionen.

1. På Azure-portalmenyn väljer du **Virtuella datorer** och väljer den virtuella datorn som du replikerade.
1. I **Operationer** väljer du **Haveriberedskap**.
1. Om du vill visa replikeringsinformationen från **översikten** väljer du **Essentials**. Mer information visas i **hälso- och status,** **redundansberedskap**och kartan **Infrastrukturvy.**

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Replikeringsstatus.":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill stoppa replikering av den virtuella datorn i den primära regionen måste du inaktivera replikering:

- Källans replikeringsinställningar rensas automatiskt.
- Tillägget Site Recovery som är installerat på den virtuella datorn under replikeringen tas inte bort.
- Site Recovery fakturering för den virtuella datorn stoppas.

Så här inaktiverar du replikering:

1. På Azure-portalmenyn väljer du **Virtuella datorer** och väljer den virtuella datorn som du replikerade.
1. I **Operationer** väljer du **Haveriberedskap**.
1. Välj **Inaktivera replikering**i **översikten**.
1. Om du vill avinstallera tillägget Site Recovery går du till den virtuella datorns **tillägg för inställningar** > **.**

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Inaktivera replikering.":::

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten replikerar du en enda virtuell dator till en sekundär region. Konfigurera sedan replikering för flera virtuella Azure-datorer.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap för virtuella Azure-datorer](azure-to-azure-tutorial-enable-replication.md)
