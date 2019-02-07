---
title: 'Felsöka Azure Backup-fel: Status för gäst-Agent tillgänglig'
description: Symptom, orsaker och lösningar i Azure Backup-fel relaterade till agenten, tillägg och diskar.
services: backup
author: genlin
manager: cshepard
keywords: Azure backup; VM-agenten; Nätverksanslutning;
ms.service: backup
ms.topic: troubleshooting
ms.date: 12/03/2018
ms.author: genli
ms.openlocfilehash: 5eecd750642610737d346c5c270349e285106e95
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820444"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Felsöka Azure Backup-fel: Problem med agenten eller -tillägget

Den här artikeln innehåller åtgärder för felsökning som kan hjälpa dig att lösa Azure Backup-fel som rör kommunikation med VM-agenten och tillägg.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM-agenten kunde inte kommunicera med Azure Backup

**Felkod**: UserErrorGuestAgentStatusUnavailable <br>
**Felmeddelande**: VM-agenten kunde inte kommunicera med Azure Backup<br>

När du har registrerat och schemalägga en virtuell dator för Backup-tjänsten Initierar säkerhetskopiering jobbet genom att kommunicera med VM-agenten att ta en ögonblicksbild för point-in-time. Något av följande villkor kan förhindra att ögonblicksbilden utlöses. När en ögonblicksbild inte utlöses misslyckas säkerhetskopieringen. Slutför följande felsökningssteg i angiven ordning och försök igen:<br>
**Orsak 1: [Agenten är installerad på den virtuella datorn, men det är inte svarar (för Windows virtuella datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Orsak 2: [Agenten installerad på den virtuella datorn är för gammal (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [Det går inte att hämta den ögonblicksbild av statusen eller går inte att ta en ögonblicksbild](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Orsak 4: [Säkerhetskopieringstillägget inte går att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 5: [Den virtuella datorn har inte tillgång till internet](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - kunde inte kommunicera med VM-agenten för ögonblicksbild av status

**Felkod**: GuestAgentSnapshotTaskStatusError<br>
**Felmeddelande**: Det gick inte att kommunicera med VM-agenten för ögonblicksbild av status <br>

När du har registrerat och schemalägga en virtuell dator för Azure Backup-tjänsten Initierar säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden utlöses. Om ögonblicksbilden inte utlöses, kan det uppstå en säkerhetskopieringen har misslyckats. Slutför följande felsökningssteg i angiven ordning och försök igen:  
**Orsak 1: [Agenten är installerad på den virtuella datorn, men det är inte svarar (för Windows virtuella datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 2: [Agenten installerad på den virtuella datorn är för gammal (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [Den virtuella datorn har inte tillgång till internet](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - som har nått maxgränsen för återställningspunkt insamling

**Felkod**: UserErrorRpCollectionLimitReached <br>
**Felmeddelande**: Har nått maxgränsen för återställningspunkt insamling. <br>
* Det här problemet kan inträffa om det finns ett lås på recovery point resursgruppen förhindrar automatisk rensning av återställningspunkt.
* Det här problemet kan också inträffa om flera säkerhetskopieringar utlöses per dag. För närvarande rekommenderar vi endast en säkerhetskopiering per dag som det ögonblick RPs bevaras i 7 dagar och bara 18 omedelbar RPs kan associeras med en virtuell dator vid en given tidpunkt. <br>

Rekommenderad åtgärd:<br>
Häv spärren för resursgruppen för den virtuella datorn för att lösa problemet och försök igen för att utlösa rensningen.
> [!NOTE]
    > Backup-tjänsten skapar en separat resursgrupp än resursgruppen för den virtuella datorn att lagra samling med återställningspunkter. Kunder bör inte låsa resursgruppen som skapades för användning av Backup-tjänsten. Namnformatet för resursgruppen som skapades av Backup-tjänsten är: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Steg 1: [Ta bort låset från resursgruppen återställningspunkt](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Steg 2: [Rensa samling med återställningspunkter](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - säkerhetskopiering har inte tillräckliga behörigheter till nyckelvalvet för säkerhetskopiering för krypterade virtuella datorer

**Felkod**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Felmeddelande**: Säkerhetskopiering har inte tillräckliga behörigheter till nyckelvalvet för säkerhetskopiering för krypterade virtuella datorer. <br>

För säkerhetskopieringen ska lyckas på krypterade virtuella datorer, måste den ha behörighet att komma åt nyckelvalvet. Detta kan göras med hjälp av den [Azure-portalen](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) eller via den [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - ögonblicksbildsåtgärden misslyckades på grund av den virtuella datorn saknar nätverksanslutning

**Felkod**: ExtensionSnapshotFailedNoNetwork<br>
**Felmeddelande**: Det gick inte att utföra åtgärden för ögonblicksbilden eftersom den virtuella datorn saknar nätverksanslutning<br>

När du har registrerat och schemalägga en virtuell dator för Azure Backup-tjänsten Initierar säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden utlöses. Om ögonblicksbilden inte utlöses, kan det uppstå en säkerhetskopieringen har misslyckats. Slutför följande felsökningssteg i angiven ordning och försök igen:    
**Orsak 1: [Det går inte att hämta den ögonblicksbild av statusen eller går inte att ta en ögonblicksbild](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 2: [Säkerhetskopieringstillägget inte går att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 3: [Den virtuella datorn har inte tillgång till internet](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>Det gick inte att ExtentionOperationFailedForManagedDisks - vmsnapshot-tillägget

**Felkod**: ExtentionOperationFailedForManagedDisks <br>
**Felmeddelande**: Vmsnapshot-tillägget misslyckades<br>

När du har registrerat och schemalägga en virtuell dator för Azure Backup-tjänsten Initierar säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden utlöses. Om ögonblicksbilden inte utlöses, kan det uppstå en säkerhetskopieringen har misslyckats. Slutför följande felsökningssteg i angiven ordning och försök igen:  
**Orsak 1: [Det går inte att hämta den ögonblicksbild av statusen eller går inte att ta en ögonblicksbild](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 2: [Säkerhetskopieringstillägget inte går att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 3: [Agenten är installerad på den virtuella datorn, men det är inte svarar (för Windows virtuella datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 4: [Agenten installerad på den virtuella datorn är för gammal (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - säkerhetskopieringen misslyckas med ett internt fel

**Felkod**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Felmeddelande**: Säkerhetskopieringen misslyckades med ett internt fel – försök igen om några minuter <br>

När du har registrerat och schemalägga en virtuell dator för Azure Backup-tjänsten Initierar säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden utlöses. Om ögonblicksbilden inte utlöses, kan det uppstå en säkerhetskopieringen har misslyckats. Slutför följande felsökningssteg i angiven ordning och försök igen:  
**Orsak 1: [Agenten installerad i den virtuella datorn, men den inte svarar (för Windows virtuella datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 2: [Agenten installerad på den virtuella datorn är för gammal (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [Det går inte att hämta den ögonblicksbild av statusen eller går inte att ta en ögonblicksbild](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 4: [Säkerhetskopieringstillägget inte går att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 5: Backup-tjänsten har inte behörighet att ta bort gamla återställningspunkter på grund av en grupp resurslås** <br>
**Orsak 6: [Den virtuella datorn har inte tillgång till internet](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-1023gb"></a>UserErrorUnsupportedDiskSize – för närvarande Azure Backup har inte stöd för diskar som är större än 1 023 GB

**Felkod**: UserErrorUnsupportedDiskSize <br>
**Felmeddelande**: För närvarande har Azure Backup inte stöd för diskstorlekar som är större än 1023 GB <br>

Din säkerhetskopieringen misslyckas, när du säkerhetskopierar virtuella datorer med diskstorlekar på över 1 023 GB, eftersom ditt valv inte har uppgraderats till omedelbar återställning. Uppgradera till omedelbar återställning ger stöd för upp till 4TB, finns i den här [artikeln](backup-instant-restore-capability.md#upgrading-to-instant-restore). När du uppgraderar kan ta det upp till två timmar innan prenumerationen för att kunna ta del av den här funktionen. Ange tillräckliga bufferten innan du försöker igen.  

## <a name="usererrorstandardssdnotsupported---currently-azure-backup-does-not-support-standard-ssd-disks"></a>UserErrorStandardSSDNotSupported – för närvarande Azure Backup stöder inte Standard SSD-diskar

**Felkod**: UserErrorStandardSSDNotSupported <br>
**Felmeddelande**: Azure Backup stöder för närvarande inte Standard SSD-diskar <br>

Azure Backup stöder för närvarande Standard SSD-diskar endast för valv som har uppgraderats till [omedelbar återställning](backup-instant-restore-capability.md).

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - det går inte att påbörja säkerhetskopieringen eftersom en annan säkerhetskopiering pågår just nu

**Felkod**: UserErrorBackupOperationInProgress <br>
**Felmeddelande**: Det går inte att påbörja säkerhetskopieringen eftersom en annan säkerhetskopiering pågår just nu<br>

Senaste säkerhetskopieringsjobbet misslyckades eftersom det finns en befintlig säkerhetskopiering pågår. Du kan inte starta ett nytt säkerhetskopieringsjobb tills det aktuella jobbet har slutförts. Se till att säkerhetskopieringen för närvarande pågår har slutförts innan du utlöser eller schemalägga en annan säkerhetskopieringsåtgärder. Du kan kontrollera status för säkerhetskopieringsjobb utföra i stegen nedan:

1. Logga in på Azure-portalen och klicka på **Alla tjänster**. Skriv Recovery Services och klicka på **Recovery Services-valv**. Listan över Recovery Services-valv visas.
2. Listan över recovery services-valv, Välj ett valv där säkerhetskopian har konfigurerats.
3. Klicka på valvets instrumentpanel **säkerhetskopieringsjobb** visas alla säkerhetskopieringsjobb.

    * Om ett säkerhetskopieringsjobb pågår, vänta på den för att slutföra eller avbryta säkerhetskopieringen.
        * Avbryt säkerhetskopieringsjobbet högerklickar på säkerhetskopieringsjobbet och klicka på **Avbryt** eller Använd [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.backup/stop-azurermbackupjob?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.12.0).
    * Om du har konfigurerat om säkerhetskopiering i ett annat valv, kontrollera att det finns inga säkerhetskopieringsjobb som körs i det gamla valvet. Avbryt jobbet om den finns.
        * Avbryt säkerhetskopieringsjobbet högerklickar på säkerhetskopieringsjobbet och klicka på **Avbryt** eller Använd [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.backup/stop-azurermbackupjob?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.12.0)
4. Försök att säkerhetskopiera igen.

Om den schemalagda säkerhetskopieringen tar längre tid som är i konflikt med nästa säkerhetskopieringskonfigurationen granskar den [metodtips](backup-azure-vms-introduction.md#best-practices), [säkerhetskopieringsprestanda](backup-azure-vms-introduction.md#backup-performance) och [återställa beräkningen ](backup-azure-vms-introduction.md#restore-considerations).


## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-vm-has-no-internet-access"></a>Den virtuella datorn har inte tillgång till internet
Per distributionskrav har inte den virtuella datorn Internetåtkomst. Eller så den kan ha begränsningar som förhindrar åtkomst till Azure-infrastrukturen.

För att fungera korrekt, kräver att säkerhetskopieringstillägget anslutning till Azure offentliga IP-adresser. Tillägget skickar kommandon till Azure storage-slutpunkt (HTTPs-URL) för hantering av ögonblicksbilder av den virtuella datorn. Om tillägget inte har åtkomst till det offentliga internet, misslyckas säkerhetskopieringen så småningom.

####  <a name="solution"></a>Lösning
Lös nätverksproblemet, se [nätverksanslutningen](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten är installerad på den virtuella datorn, men det är inte svarar (för Windows virtuella datorer)

#### <a name="solution"></a>Lösning
VM-agenten kan vara skadad eller tjänsten har stoppats. Installera om den Virtuella datoragenten kan hämta den senaste versionen. Det hjälper också att starta om kommunikation med tjänsten.

1. Avgör om Windows Azure-gästagenttjänsten körs i VM-tjänsterna (services.msc). Försök att starta om Windows Azure-gästagenttjänsten och påbörja säkerhetskopieringen.    
2. Om Windows Azure-gästagenttjänsten inte visas i tjänster på Kontrollpanelen, gå till **program och funktioner** att avgöra om tjänsten Windows Azure-Gästagenten är installerad.
4. Om Windows Azure-Gästagentversionen som visas i **program och funktioner**, avinstallera Windows Azure-Gästagenten.
5. Ladda ned och installera den [senaste versionen av agenten MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen.
6. Kontrollera att tjänsten Windows Azure-Gästagentversionen visas i services.
7. Köra en säkerhetskopiering på begäran:
    * I portalen, väljer **Säkerhetskopiera nu**.

Kontrollera också att [Microsoft .NET 4.5 har installerats](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) på den virtuella datorn. .NET 4.5 krävs för VM-agenten kan kommunicera med tjänsten.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten installerad på den virtuella datorn är för gammal (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning
Mest agent-relaterade eller tillägget-relaterade fel för virtuella Linux-datorer orsakas av problem som påverkar en inaktuell VM-agent. Följ dessa allmänna riktlinjer för att felsöka problemet:

1. Följ anvisningarna för [uppdaterar Linux VM-agenten](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Vi *rekommenderar* att du uppdaterar agenten endast via en lagringsplats för distribution. Vi rekommenderar inte hämta agent-kod direkt från GitHub och uppdaterar den. Om den senaste agenten för din distribution inte är tillgänglig, kontakta distribution stöd för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten, går du till den [Windows Azure Linux-agent](https://github.com/Azure/WALinuxAgent/releases) sidan i GitHub-lagringsplatsen.

2. Se till att Azure-agenten körs på den virtuella datorn genom att köra följande kommando: `ps -e`

 Om processen inte körs måste du starta om den med hjälp av följande kommandon:

 * För Ubuntu: `service walinuxagent start`
 * För andra distributioner: `service waagent start`

3. [Konfigurera automatisk omstart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Kör en ny test-säkerhetskopia. Om felet kvarstår, kan du samla in följande loggar från den virtuella datorn:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * / var/logga/azure / *

Om vi behöver utförlig loggning för waagent gör du följande:

1. Leta upp följande rad i filen /etc/waagent.conf: **Aktivera utförlig loggning (y | n)**
2. Ändra den **Logs.Verbose** värdet från *n* till *y*.
3. Spara ändringen och starta sedan om waagent genom att följa stegen som beskrivs i det här avsnittet.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Det går inte att hämta den ögonblicksbild av statusen eller går inte att ta en ögonblicksbild
Säkerhetskopiering av virtuella datorer är beroende av utfärda ett kommando för ögonblicksbild till det underliggande lagringskontot. Backup kan misslyckas eftersom den har ingen åtkomst till lagringskontot eller eftersom körningen av uppgiften ögonblicksbild är försenad.

#### <a name="solution"></a>Lösning
Följande villkor kan orsaka uppgiften ögonblicksbild misslyckas:

| Orsak | Lösning |
| --- | --- |
| Virtuella datorns status rapporteras felaktigt eftersom Virtuellt datorn stängs av i Remote Desktop Protocol (RDP). | Om du stänger av den virtuella datorn i RDP Kontrollera portalen för att avgöra om virtuella datorns status är rätt. Om det inte är korrekt, stänger du den virtuella datorn i portalen med hjälp av den **avstängning** alternativet på VM-instrumentpanelen. |
| Den virtuella datorn kan inte hämta värden eller fabric-adress från DHCP. | DHCP måste vara aktiverat på gästen för IaaS VM-säkerhetskopiering för att fungera. Om den virtuella datorn inte kan hämta värden eller fabric-adress från DHCP-svar 245, kan inte den hämta eller köra några tillägg. Om du behöver en statisk privat IP-adress kan du konfigurera det via den **Azure-portalen** eller **PowerShell** och kontrollera att DHCP-alternativ i den virtuella datorn är aktiverad. Läs mer om hur du installerar en statisk IP-adress via PowerShell [klassisk virtuell dator](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm) och [Resource Manager-VM](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface).

### <a name="the-backup-extension-fails-to-update-or-load"></a>Säkerhetskopieringstillägget inte går att uppdatera eller läsa in
Om tillägg inte kan läsas in, misslyckas säkerhetskopieringen eftersom det går inte att ta en ögonblicksbild.

#### <a name="solution"></a>Lösning

Avinstallera tillägget för att tvinga VMSnapshot-tillägget läses in på nytt. Nästa säkerhetskopiering försöket hämtar tillägget.

Avinstallera tillägget:

1. I den [Azure-portalen](https://portal.azure.com/)går du till den virtuella datorn som har drabbats av säkerhetskopieringen har misslyckats.
2. Välj **inställningar**.
3. Välj **Tillägg**.
4. Välj **Vmsnapshot-tillägget**.
5. Välj **avinstallera**.

För Linux VM, om VMSnapshot-tillägget inte visas i Azure-portalen [uppdatera Azure Linux Agent](../virtual-machines/linux/update-agent.md), och sedan köra säkerhetskopieringen.

Gör så här gör tillägget installeras under nästa säkerhetskopiering.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Ta bort låset från recovery point resursgruppen.
1. Logga in på [Azure Portal](http://portal.azure.com/).
2. Gå till **alla resurser alternativet**, väljer du resursgruppen för återställningspunkt samling i formatet AzureBackupRG_`<Geo>`_`<number>`.
3. I den **inställningar** väljer **Lås** att visa låsen.
4. Om du vill ta bort låset, Välj ellipsen och klicka på **ta bort**.

    ![Ta bort lås ](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Rensa samling med återställningspunkter
När du tar bort låset har återställningspunkterna att rensas. Följ någon av metoderna för att rensa återställningspunkterna:<br>
* [Rensa samling med återställningspunkter genom att köra ad hoc-säkerhetskopiering](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Rensa återställning samlingen från Azure-portalen med återställningspunkter](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Rensa samling med återställningspunkter genom att köra ad hoc-säkerhetskopiering
När du tar bort låset kan utlösa en manuell-ad-hoc-säkerhetskopiering. Detta säkerställer att återställningspunkterna automatiskt rensas. Förvänta dig den här ad-hoc/manuell åtgärd misslyckas första gången. men säkerställer det att automatisk rensning i stället för manuell borttagning av återställningspunkter. När rensningen ska nästa schemalagda säkerhetskopiering lyckas.

> [!NOTE]
    > Automatisk rensning sker efter några timmars aktiverar manuell-ad-hoc-säkerhetskopiering. Om din schemalagd säkerhetskopiering fortfarande misslyckas kommer försök att manuellt ta bort den samling med återställningspunkter med hjälp av stegen visas [här](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Rensa återställning samlingen från Azure-portalen med återställningspunkter <br>

För att manuellt ta bort återställningen pekar du samlingen som inte tas bort på grund av låset på resursgruppen, prova följande steg:
1. Logga in på [Azure Portal](http://portal.azure.com/).
2. På den **Hub** -menyn klickar du på **alla resurser**, väljer du resursgruppen med formatet AzureBackupRG_`<Geo>`_`<number>` där den virtuella datorn finns.

    ![Ta bort lås ](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klicka på resursgruppen, den **översikt** bladet visas.
4. Välj **Visa dolda typer** alternativet för att visa alla dolda resurser. Välj återställningspunkt samlingar med formatet AzureBackupRG_`<VMName>`_`<number>`.

    ![Ta bort lås ](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Klicka på **ta bort**, för att rensa samlingen med återställningspunkter.
6. Försök att säkerhetskopiera igen.
