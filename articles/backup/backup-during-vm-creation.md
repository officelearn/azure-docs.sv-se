---
title: Aktivera säkerhets kopiering när du skapar en virtuell Azure-dator med Azure Backup
description: Beskriver hur du aktiverar säkerhets kopiering när du skapar en virtuell Azure-dator med Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: dacurwin
ms.openlocfilehash: 90f69371457bbfe37789b12971343f738ff35e8e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639727"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Aktivera säkerhetskopiering när du skapar en virtuell Azure-dator

Använd Azure Backup tjänsten för att säkerhetskopiera virtuella datorer i Azure. Virtuella datorer säkerhets kopie ras enligt ett schema som anges i en säkerhets kopierings princip och återställnings punkter skapas från säkerhets kopieringar. Återställnings punkter lagras i Recovery Services valv.

Den här artikeln beskriver hur du aktiverar säkerhets kopiering när du skapar en virtuell dator (VM) i Azure Portal.  

## <a name="before-you-start"></a>Innan du börjar

- [Kontrol lera](backup-support-matrix-iaas.md#supported-backup-actions) vilka operativ system som stöds om du aktiverar säkerhets kopiering när du skapar en virtuell dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du inte redan har loggat in på ditt konto loggar du in på [Azure Portal](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Skapa en virtuell dator med säkerhets kopia konfigurerad

1. Klicka på **skapa en resurs**i Azure Portal.

2. I Azure Marketplace klickar du på **Compute**och väljer sedan en VM-avbildning.

3. Konfigurera den virtuella datorn i enlighet med [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) -eller [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) -instruktionerna.

4. Klicka på **på**fliken **hantering** i **Aktivera säkerhets kopiering**.
5. Azure Backup säkerhets kopieringar till ett Recovery Services-valv. Klicka på **Skapa nytt** om du inte har ett befintligt valv.
6. Acceptera det föreslagna valv namnet eller ange ditt eget.
7. Ange eller skapa en resurs grupp där valvet ska placeras. Resurs grupps valvet kan skilja sig från resurs gruppen för den virtuella datorn.

    ![Aktivera säkerhets kopiering för en virtuell dator](./media/backup-during-vm-creation/enable-backup.png)

8. Godkänn standard principen för säkerhets kopiering eller ändra inställningarna.
    - En säkerhets kopierings princip anger hur ofta ögonblicks bilder av den virtuella datorn ska säkerhets kopie ras och hur länge säkerhets kopiorna ska bevaras.
    - Standard principen säkerhetskopierar den virtuella datorn en gång om dagen.
    - Du kan anpassa en egen säkerhets kopierings princip för en virtuell Azure-dator för att ta säkerhets kopior varje dag eller varje vecka.
    - [Läs mer](backup-azure-vms-introduction.md#backup-and-restore-considerations) om säkerhets kopierings överväganden för virtuella Azure-datorer.
    - [Läs mer](backup-instant-restore-capability.md) om funktionen för omedelbar återställning.

      ![Standard princip för säkerhets kopiering](./media/backup-during-vm-creation/daily-policy.png)


> [!NOTE]
> Azure Backup tjänst skapar en separat resurs grupp (förutom VM-gruppgruppen) för att lagra ögonblicks bilder med namngivnings formatet **AzureBackupRG_geography_number** (exempel: AzureBackupRG_northeurope_1). Data i den här resurs gruppen kommer att behållas under den tid i dagar som anges i avsnittet *Behåll ögonblicks bild av ögonblicks bilder* i säkerhets kopierings principen för den virtuella Azure-datorn.  Att använda ett lås till den här resurs gruppen kan orsaka säkerhets kopierings fel.<br>
Den här resurs gruppen ska också undantas från eventuella namn-och märkes begränsningar som en begränsnings princip skulle blockera skapandet av resurs plats samlingar i den igen och orsaka säkerhets kopierings problem.


## <a name="start-a-backup-after-creating-the-vm"></a>Starta en säkerhets kopiering när du har skapat den virtuella datorn

Säkerhets kopian av den virtuella datorn kommer att köras enligt säkerhets kopierings principen. Vi rekommenderar dock att du kör en första säkerhets kopiering.

När den virtuella datorn har skapats gör du följande:

1. I egenskaperna för den virtuella datorn klickar du på **säkerhetskopiera**. VM-statusen är den första säkerhets kopieringen väntar tills den första säkerhets kopieringen körs
2. Klicka på **Säkerhetskopiera nu** för att köra en säkerhets kopiering på begäran.

    ![Köra en säkerhets kopiering på begäran](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Använd en Resource Manager-mall för att distribuera en skyddad virtuell dator

I föregående steg förklaras hur du använder Azure Portal för att skapa en virtuell dator och skydda den i ett Recovery Services-valv. Om du snabbt vill distribuera en eller flera virtuella datorer och skydda dem i ett Recovery Services valv, se mallen [distribuera en virtuell Windows-dator och aktivera säkerhets kopiering](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).



## <a name="next-steps"></a>Nästa steg

Nu när du har skyddat din virtuella dator kan du läsa om hur du hanterar och återställer dem.

- [Hantera och övervaka virtuella datorer](backup-azure-manage-vms.md)
- [Återställa virtuell dator](backup-azure-arm-restore-vms.md)

Om du stöter på några problem kan du [läsa](backup-azure-vms-troubleshoot.md) fel söknings guiden.
