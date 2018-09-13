---
title: Aktivera säkerhetskopiering av Azure virtuella datorer under skapandet
description: Se hur du aktiverar säkerhetskopiering av Azure virtuella datorer under skapandeprocessen.
services: backup, virtual-machines
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 9fd4707a201163002cc15cc9cf97e544e76cf7c6
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35756285"
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Aktivera säkerhetskopiering under skapande av Azure virtuella datorer 

Azure Backup-tjänsten tillhandahåller ett gränssnitt för att skapa och konfigurera säkerhetskopieringar till molnet. Skydda dina data genom att säkerhetskopiera, kallas återställningspunkter, med jämna mellanrum. Med Azure Backup skapas återställningspunkter som kan lagras i geo-redundanta återställningsvalv. Den här artikeln beskriver hur du aktiverar säkerhetskopiering när du skapar en virtuell dator (VM) i Azure-portalen.  

## <a name="log-in-to-azure"></a>Logga in på Azure 

Om du inte redan är i loggat in på ditt konto, logga in på den [Azure-portalen](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Skapa virtuell dator med säkerhetskopiering har konfigurerats 

1. I det övre vänstra hörnet i Azure Portal klickar du på **New**. 

2. Välj **Compute**, och välj sedan en avbildning av den virtuella datorn.   

3. Ange informationen för den virtuella datorn. Användarnamnet och lösenordet används för att logga in på den virtuella datorn. När du är klar klickar du på **OK**. 

4. Välj en storlek för den virtuella datorn.  

5. Under **Inställningar > Backup**, klickar du på **aktiverad** att ta fram inställningar för säkerhetskopiering. Du kan acceptera standardvärdena och klicka på **OK** på inställningssidan för att komma till sammanfattningssidan att skapa den virtuella datorn. Följ nästa steg om du vill ändra värdena.  

6. Skapa eller välj ett Recovery Services-valv som innehåller säkerhetskopior av den virtuella datorn. Om du skapar ett recovery services-valv, kan du välja en resursgrupp för valvet.  

    ![Skapa sida för konfiguration av säkerhetskopiering på den virtuella datorn](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Resursgrupp för Recovery Services-valvet kan skilja sig från resursgruppen för den virtuella datorn.  
    > 
    > 

7. Som standard väljs en säkerhetskopieringsprincip för dig att snabbt skydda den virtuella datorn. En princip för säkerhetskopiering anger hur ofta du vill ta ögonblicksbilder av säkerhetskopior, och hur länge dessa säkerhetskopior ska behållas. Du kan acceptera standardprincipen, eller du kan skapa eller välj en annan säkerhetskopieringspolicy. Om du vill redigera principen för säkerhetskopiering, Välj **Säkerhetskopieringsprincipen** och ändra värdena för principen.  

8. När du är nöjd med konfigurationen av säkerhetskopiering värden, på sidan inställningar klickar du på **OK**.  

9. På sidan sammanfattning när verifiering har gått ut, klickar du på **skapa** att skapa en virtuell dator som använder de konfigurerade inställningarna för säkerhetskopiering. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Starta en säkerhetskopiering när du har skapat den virtuella datorn 

Även om säkerhetskopieringspolicyn har skapats är det bra att skapa någon säkerhetskopia. Visa säkerhetskopierade informationen för den virtuella datorn när virtuella datorn skapas mallen har slutförts från den **Operations** ange i den vänstra menyn klickar du på **säkerhetskopiering**. Du kan använda detta för att utlösa en säkerhetskopiering på begäran, återställa en fullständig virtuell dator eller alla diskar, återställa filer från säkerhetskopiering av virtuella datorer eller ändra principen för säkerhetskopiering som är associerade med den virtuella datorn.  

## <a name="using-a-resource-manager-template-to-deploy-a-protected-vm"></a>Med en Resource Manager-mall för att distribuera en skyddad virtuell dator

Föregående steg beskriver hur du använder Azure-portalen för att skapa en virtuell dator och skydda dem till ett Recovery Services-valv. Om du vill att snabbt distribuera en eller flera virtuella datorer och skydda dem till ett Recovery Services-valv, finns i mallen, [distribuera en virtuell Windows-dator och aktivera säkerhetskopiering](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Vilka VM-avbildningar kan du aktivera säkerhetskopiering vid tidpunkten för skapandet av VM? 

I följande lista över core-avbildningar som publicerats av Microsoft har stöd för att aktivera säkerhetskopiering under skapandet av VM. För andra virtuella datorer kan aktivera du säkerhetskopiering när den virtuella datorn har skapats. Läs mer [Aktivera säkerhetskopiering när datorn har skapats](quick-backup-vm-portal.md) 

- **Windows** -Windows Server 2016 datacenter, Windows Server 2016 Datacenter-kärna, Windows Server 2012 DataCenter, Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 SP1 
- **Ubuntu** – Ubuntu Server 1710, Ubuntu Server 1704, UUbuntu Server 1604(LTS) Ubuntu Server 1404(LTS) 
- **Red Hat** -RHEL 6.7, 6.8, 6,9, 7.2, 7.3, 7.4 
- **SUSE** -SP4 för SUSE Linux Enterprise Server 11, 12 SP2, 12 SP3 
- **Debian** – Debian 8, Debian 9 
- **CentOS** -CentOS 6,9, CentOS 7.3 
- **Oracle Linux** -Oracle Linux 6.7, 6.8, 6,9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>Är kostnaden för säkerhetskopiering som ingår i kostnaden för virtuella datorer? 

Nej, kostnaderna för säkerhetskopiering kan separat, eller distinkt av kostnaderna för virtuella datorer. Mer information om prissättning för backup finns i den [prissättning för Backup plats](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Vilka behörigheter krävs för att aktivera säkerhetskopiering på en virtuell dator? 

Om du är en virtual machine-deltagare kan aktivera du säkerhetskopiering på den virtuella datorn. Om du använder en anpassad roll kan behöver du följande behörigheter att lyckas Aktivera säkerhetskopiering på den virtuella datorn. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Om dina recovery services-valv och en virtuell dator har olika resursgrupper kan vara säker på att du har skrivbehörighet i recovery services vault-resursgrupp.  

## <a name="next-steps"></a>Nästa steg 

Nu när du har skyddat den virtuella datorn, se följande artiklar för att lära dig om uppgifter för hantering av virtuell dator och återställa virtuella datorer. 

- [Hantera och övervaka dina virtuella datorer](backup-azure-manage-vms.md) 
- [Återställa virtuella datorer](backup-azure-arm-restore-vms.md) 
