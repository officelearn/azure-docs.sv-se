---
title: "Aktivera Virtuella Azure-säkerhetskopiering vid skapandet | Microsoft Docs"
description: "Se stegen för att aktivera säkerhetskopiering av Azure virtuella datorer under skapandeprocessen."
services: backup, virtual-machines
documentationcenter: 
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 4041fc555fe4b61d10f84236dcae5156c6282fd3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Aktivera säkerhetskopiering under skapande av virtuell Azure-dator 

Azure Backup-tjänsten tillhandahåller ett gränssnitt för att skapa och konfigurera säkerhetskopieringar till molnet. Skydda dina data genom att ta säkerhetskopior, återställningspunkter, anropas med jämna mellanrum. Med Azure Backup skapas återställningspunkter som kan lagras i geo-redundanta återställningsvalv. Den här artikeln beskriver hur du aktiverar säkerhetskopiering när du skapar en virtuell dator (VM) i Azure-portalen.  

## <a name="log-in-to-azure"></a>Logga in på Azure 

Om du inte är redan i loggat in på ditt konto kan logga in på den [Azure-portalen](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Skapa virtuell dator med säkerhetskopiering har konfigurerats 

1. I det övre vänstra hörnet i Azure-portalen klickar du på **ny**. 

2. Välj **Compute**, och välj sedan en avbildning av den virtuella datorn.   

3. Ange informationen för den virtuella datorn. Användarnamn och lösenord som används för att logga in på den virtuella datorn. När du är klar klickar du på **OK**. 

4. Välj en storlek för den virtuella datorn.  

5. Under **Inställningar > säkerhetskopiering**, klickar du på **aktiverad** så att inställningar för säkerhetskopiering. Du kan acceptera standardvärdena och klicka på **OK** på inställningssidan för att komma till sammanfattningssida för att skapa den virtuella datorn. Följ nästa steg om du vill ändra värdena.  

6. Skapa eller välj ett Recovery Services-valv som innehåller säkerhetskopior av den virtuella datorn. Om du skapar en återställningstjänstvalvet kan välja du en resursgrupp för valvet.  

    ![Konfigurationen för säkerhetskopiering i vm Skapa sida](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Resursgruppen för Recovery Services-valvet kan skilja sig från resursgruppen för den virtuella datorn.  
    > 
    > 

7. Som standard väljs en princip för säkerhetskopiering att snabbt skydda den virtuella datorn. En princip för säkerhetskopiering anger hur ofta du vill ta ögonblicksbilder av säkerhetskopior, och hur lång tid att behålla dessa säkerhetskopior. Du kan acceptera standardprincipen eller du kan skapa eller välj en annan princip för säkerhetskopiering. Om du vill redigera principen för säkerhetskopiering, Välj **säkerhetskopieringsprincip** och ändra värdena för principen.  

8. När du är nöjd med konfigurationen för säkerhetskopiering-värden inställningen på sidan klickar du på **OK**.  

9. På sidan sammanfattning när valideringen har gått ut, klickar du på **skapa** att skapa en virtuell dator som använder de konfigurerade inställningarna för säkerhetskopiering. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Starta en säkerhetskopiering när du har skapat den virtuella datorn 

Om principen för säkerhetskopiering har skapats, är det bra att skapa en första säkerhetskopiering. Visa säkerhetskopiering information för den virtuella datorn när VM skapa mallen slutförs, från den **Operations** ange på den vänstra menyn klickar du på **säkerhetskopiering**. Du kan använda detta för att utlösa en säkerhetskopiering på begäran, Återställ en fullständig virtuell eller alla diskar, återställa filer från säkerhetskopiering eller ändra principen för säkerhetskopiering som är kopplade till den virtuella datorn.  

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Vilka VM-avbildningar Aktivera säkerhetskopiering vid tidpunkten för att skapa en virtuell dator? 

Följande lista över core avbildningar publicerade av Microsoft har stöd för att aktivera säkerhetskopiering under Skapa Virtuella. Du kan aktivera säkerhetskopiering när den virtuella datorn har skapats för andra virtuella datorer. Lär dig mer [Aktivera säkerhetskopiering när VM har skapats](quick-backup-vm-portal.md) 

- **Windows** -Windows Server 2016 datacenter, Windows Server 2016 Data Center core Windows Server 2012 DataCenter, Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 SP1 
- **Ubuntu** -Ubuntu Server 1710, Ubuntu Server 1704 UUbuntu Server 1604(LTS) Ubuntu Server 1404(LTS) 
- **Redhat** -RHEL 6.7, 6.8, 6,9, 7.2, 7.3, 7.4 
- **SUSE** -SP4 för SUSE Linux Enterprise Server 11, 12 SP2, 12 SP3 
- **Debian** -Debian 8, Debian 9 
- **CentOS** -CentOS 6,9, CentOS 7.3 
- **Oracle Linux** -Oracle Linux 6.7, 6.8, 6,9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>Kostar Säkerhetskopiering ingår i VM-kostnad? 

Nej, säkerhetskopiering kostnader som är separat, eller distinkt från kostnaderna för virtuella datorer. Mer information om säkerhetskopiering priser finns i [säkerhetskopiering priser plats](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Vilka behörigheter som krävs för att aktivera säkerhetskopiering på en virtuell dator? 

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
 
Om återställningstjänstvalvet och den virtuella datorn har olika resursgrupper, måste du ha skrivbehörighet i resursgruppen recovery services-valvet.  

## <a name="next-steps"></a>Nästa steg 

Nu när du har skyddat den virtuella datorn finns i följande artiklar om VM hanteringsuppgifter och återställa virtuella datorer. 

- [Hantera och övervaka dina virtuella datorer](backup-azure-manage-vms.md) 
- [Återställa virtuella datorer](backup-azure-arm-restore-vms.md) 
