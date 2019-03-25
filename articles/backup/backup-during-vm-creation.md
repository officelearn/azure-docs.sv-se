---
title: Aktivera säkerhetskopiering när du skapar en Azure-dator med Azure Backup
description: Beskriver hur du aktiverar säkerhetskopiering när du skapar en Azure-dator med Azure Backup.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: d96b898c8f72abd7e4eb3522ae046e9fc926f387
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403586"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Aktivera säkerhetskopiering när du skapar en virtuell Azure-dator

Använda Azure Backup-tjänsten för att säkerhetskopiera virtuella Azure-datorer (VM). Virtuella datorer säkerhetskopieras enligt ett schema som anges i en princip för säkerhetskopiering och återställningspunkterna har skapats från säkerhetskopior. Återställningspunkter som lagras i Recovery Services-valv.

Den här artikeln beskriver hur du aktiverar säkerhetskopiering när du skapar en virtuell dator (VM) i Azure-portalen.  

## <a name="before-you-start"></a>Innan du börjar

- [Kontrollera](backup-support-matrix-iaas.md#supported-backup-actions) vilka operativsystem som stöds om du aktiverar säkerhetskopiering när du skapar en virtuell dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du inte redan loggat in på ditt konto, logga in på den [Azure-portalen](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Skapa en virtuell dator med säkerhetskopiering har konfigurerats

1. I Azure-portalen klickar du på **skapa en resurs**.

2. I Azure Marketplace, klickar du på **Compute**, och välj sedan en VM-avbildning.

3. Konfigurera den virtuella datorn i enlighet med den [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) instruktioner.

4. På den **Management** fliken **Aktivera säkerhetskopiering**, klickar du på **på**.
5. Azure Backup-säkerhetskopiering till ett Recovery Services-valv. Klicka på **Skapa ny** om du inte har ett befintligt valv.
6. Acceptera det föreslagna valvnamnet eller ange en egen.
7. Ange eller skapa en resursgrupp som valvet kommer att finnas. Resource group valvet kan skilja sig från resursgrupp för virtuell dator.

    ![Aktivera säkerhetskopiering för en virtuell dator](./media/backup-during-vm-creation/enable-backup.png) 

8. Acceptera standardprincip för säkerhetskopiering eller ändra inställningarna.
    - En princip för säkerhetskopiering anger hur ofta du vill ta ögonblicksbilder av säkerhetskopior för den virtuella datorn och hur lång tid att göra dessa säkerhetskopior. 
    - Standardprincipen säkerhetskopierar den virtuella datorn en gång om dagen.
    - Du kan anpassa dina egna princip för säkerhetskopiering för en Azure virtuell dator att göra säkerhetskopior dagliga och veckovisa.
    - [Läs mer](backup-azure-vms-introduction.md#backup-and-restore-considerations) om backup-överväganden för virtuella Azure-datorer.
    - [Läs mer](backup-instant-restore-capability.md) om ögonblicket återställningsfunktionen.

      ![Standardprincip för säkerhetskopiering](./media/backup-during-vm-creation/daily-policy.png) 


## <a name="start-a-backup-after-creating-the-vm"></a>Starta en säkerhetskopiering när du har skapat den virtuella datorn 

Säkerhetskopiering av virtuella datorer körs i enlighet med din princip för säkerhetskopiering. Vi rekommenderar dock att du kör någon säkerhetskopia. 

När den virtuella datorn har skapats kan du göra följande:

1. I virtuella datorns egenskaper klickar du på **Backup**. Virtuella datorns status är inledande säkerhetskopiering väntar tills den första säkerhetskopieringen körs
2. Klicka på **Säkerhetskopiera nu** att köra en säkerhetskopiering på begäran.

    ![Köra en säkerhetskopiering på begäran](./media/backup-during-vm-creation/run-backup.png) 

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Använda Resource Manager-mall för att distribuera en skyddad virtuell dator

Föregående steg beskriver hur du använder Azure-portalen för att skapa en virtuell dator och skydda den i ett Recovery Services-valv. Se mallen för att snabbt distribuera en eller flera virtuella datorer och skydda dem i ett Recovery Services-valv, [distribuera en virtuell Windows-dator och aktivera säkerhetskopiering](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).



## <a name="next-steps"></a>Nästa steg 

Nu när du har skyddat den virtuella datorn, lär du dig hur du hanterar och återställa dem.

- [Hantera och övervaka virtuella datorer](backup-azure-manage-vms.md) 
- [Återställa virtuell dator](backup-azure-arm-restore-vms.md) 

Om du får problem, [granska](backup-azure-vms-troubleshoot.md) felsökningsguiden.
