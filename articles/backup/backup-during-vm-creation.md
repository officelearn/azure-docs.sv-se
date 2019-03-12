---
title: Aktivera säkerhetskopiering av Azure virtuella datorer under skapandet
description: Hur du aktiverar säkerhetskopiering av Azure virtuella datorer under skapandeprocessen.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780451"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>Aktivera säkerhetskopiering när du skapar en Azure virtuell dator

Använda Azure Backup-tjänsten för att säkerhetskopiera virtuella Azure-datorer (VM). Virtuella datorer säkerhetskopieras enligt ett schema som anges i en princip för säkerhetskopiering och återställningspunkterna har skapats från säkerhetskopior. Återställningspunkter som lagras i Recovery Services-valv.

Den här artikeln beskriver hur du aktiverar säkerhetskopiering medan du skapar en virtuell dator (VM) i Azure-portalen.  

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du inte redan loggat in på ditt konto, logga in på den [Azure-portalen](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Skapa en virtuell dator med säkerhetskopiering har konfigurerats 

1. I det övre vänstra hörnet i Azure Portal, Välj **New**.

1. Välj **Compute**, och välj sedan en avbildning av den virtuella datorn.

1. Ange informationen för den virtuella datorn. Användarnamnet och lösenordet som du anger används för att logga in på den virtuella datorn. När du är klar väljer du **OK**. 

1. Välj en storlek för den virtuella datorn.  

1. Under **inställningar** > **säkerhetskopiering**väljer **aktiverad** att öppna inställningarna för konfiguration av säkerhetskopiering.

   - Om du vill acceptera standardvärdena, Välj **OK** på den **inställningar** sidan. Går du sedan till den **sammanfattning** sidan för att skapa den virtuella datorn. Hoppa över steg 6 – 8.
   - Följ stegen nedan om du vill ändra konfigurationen för säkerhetskopiering-värden.  

1. Skapa eller välj ett Recovery Services-valv för att lagra säkerhetskopior av den virtuella datorn. Om du skapar ett Recovery Services-valv, kan du välja en resursgrupp för valvet.  

    ![Konfiguration av säkerhetskopiering inställningar på sidan Skapa en virtuell dator](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Resursgrupp för Recovery Services-valvet kan skilja sig från resursgruppen för den virtuella datorn.  

1. Som standard väljs en säkerhetskopieringsprincip för dig att skydda den virtuella datorn. Anger hur ofta du vill ta ögonblicksbilder av säkerhetskopior för en princip för säkerhetskopiering och hur lång tid att göra dessa säkerhetskopior. 

   - Du kan acceptera standardprincipen, eller du kan skapa eller välj en annan säkerhetskopieringspolicy. 
   - Om du vill redigera principen för säkerhetskopiering, Välj **säkerhetskopieringspolicy** och ändra värdena.  

1. När du är klar med att ange värden för konfiguration av säkerhetskopiering väljer **OK** på den **inställningar** sidan.  

1. På den **sammanfattning** efter verifiering har passerat, Välj **skapa** skapa en virtuell dator som använder de konfigurerade inställningarna för säkerhetskopiering. 

## <a name="start-a-backup-after-creating-the-vm"></a>Starta en säkerhetskopiering när du har skapat den virtuella datorn 

Även om du har konfigurerat en princip för säkerhetskopiering för den virtuella datorn, är det en bra idé att skapa någon säkerhetskopia. 

När mallen för virtuella datorer skapas är klar går du till **Operations** i den vänstra menyn och välj **säkerhetskopiering** visa säkerhetskopierade informationen för den virtuella datorn. Du kan använda den här sidan:

- Utlösa en säkerhetskopiering på begäran.
- Återställa en fullständig virtuell dator eller alla diskar.
- Återställa filer från en säkerhetskopiering av virtuella datorer.
- Ändra principen för säkerhetskopiering som är associerade med den virtuella datorn.  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Använda Resource Manager-mall för att distribuera en skyddad virtuell dator

Föregående steg beskriver hur du använder Azure-portalen för att skapa en virtuell dator och skydda den i ett Recovery Services-valv. Se mallen för att snabbt distribuera en eller flera virtuella datorer och skydda dem i ett Recovery Services-valv, [distribuera en virtuell Windows-dator och aktivera säkerhetskopiering](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>Vilka VM-avbildningar stöd för konfiguration av säkerhetskopiering under skapandet av VM?

Följande core avbildningar som publicerats av Microsoft har stöd för att aktivera säkerhetskopiering under skapandet av VM. För andra virtuella datorer kan aktivera du säkerhetskopiering när den virtuella datorn har skapats. Mer information finns i [Aktivera säkerhetskopiering när datorn har skapats](quick-backup-vm-portal.md).

- **Windows** -Windows Server 2016 Datacenter, kärna för Windows Server 2016 Datacenter, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu** – Ubuntu Server 17.10, Ubuntu Server nr 17.04 från, Ubuntu Server 16.04 (LTS), Ubuntu Server 14.04 (LTS) 
- **Red Hat** - RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** -SP4 för SUSE Linux Enterprise Server 11, 12 SP2, 12 SP3 
- **Debian** - Debian 8, Debian 9 
- **CentOS** -CentOS 6,9, CentOS 7.3 
- **Oracle Linux** -Oracle Linux 6.7, 6.8, 6,9, 7.2, 7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Är kostnaden för säkerhetskopiering i VM-kostnad? 

Nej. Kostnader för Backup skiljer sig från en virtuell dators kostnader. Läs mer om prissättning för backup [prissättning för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Vilka behörigheter krävs för att aktivera säkerhetskopiering på en virtuell dator? 

Om du är deltagare VM, kan du aktivera säkerhetskopiering på den virtuella datorn. Om du använder en anpassad roll, behöver du följande behörigheter till Aktivera säkerhetskopiering på den virtuella datorn: 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Om dina Recovery Services-valv och en virtuell dator har olika resursgrupper kan du kontrollera att du har skrivbehörighet i resursgruppen för Recovery Services-valvet.  

## <a name="next-steps"></a>Nästa steg 

Nu när du har skyddat den virtuella datorn finns i följande artiklar om hur du hanterar och återställa virtuella datorer:

- [Hantera och övervaka dina virtuella datorer](backup-azure-manage-vms.md) 
- [Återställa virtuella datorer](backup-azure-arm-restore-vms.md) 
