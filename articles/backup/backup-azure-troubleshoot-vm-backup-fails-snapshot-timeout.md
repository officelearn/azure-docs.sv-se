---
title: Felsöka agent- och tilläggsproblem
description: Symptom, orsaker och lösningar på Azure Backup-fel relaterade till agent, tillägg och diskar.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 4583c02b52ab6b3a4e5056a47db096d4e34399ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248026"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Felsöka Azure Backup-fel: Problem med agenten eller tillägget

Den här artikeln innehåller felsökningssteg som kan hjälpa dig att lösa Azure Backup-fel relaterade till kommunikation med VM-agenten och tillägget.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – Den virtuella datoragenten kunde inte kommunicera med Azure Backup

**Felkod**: UserErrorGuestAgentStatusUnavailable <br>
**Felmeddelande:** VM-agenten kan inte kommunicera med Azure Backup<br>

Azure VM-agenten kan stoppas, inaktuell, i ett inkonsekvent tillstånd eller inte installeras. Dessa tillstånd förhindrar azure backup-tjänsten från att utlösa ögonblicksbilder.

- **Öppna Azure-portalen > VM > inställningar > egenskaper >** se till att VM-status **körs** och **agentstatus** är **klar**. **Status** Om VM-agenten stoppas eller är i ett inkonsekvent tillstånd startar du om agenten<br>
  - För virtuella Windows-datorer följer du dessa [steg](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) för att starta om gästagenten.<br>
  - För virtuella Linux-datorer följer du dessa [steg](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) för att starta om gästagenten.
- **Öppna Azure-portalen > VM > inställningar > tillägg** > Se till att alla tillägg **etablerar efterföljande** tillstånd. Om inte, följ dessa [steg](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) för att lösa problemet.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – Det gick inte att kommunicera med VM-agenten för ögonblicksbild av status

**Felkod**: GuestAgentSnapshotTaskStatusError<br>
**Felmeddelande:** Det gick inte att kommunicera med VM-agenten för ögonblicksbildstatus <br>

När du har registrerat och schemalägg en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med tillägget för säkerhetskopiering av virtuella datorer för att ta en ögonblicksbild i tid. Något av följande villkor kan förhindra att ögonblicksbilden utlöses. Om ögonblicksbilden inte utlöses kan ett säkerhetskopieringsfel uppstå. Slutför följande felsökningssteg i den ordning som anges och försök sedan igen:  

**Orsak 1: [Agenten är installerad i den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Orsak 2: [Agenten som är installerad i den virtuella datorn är föråldrad (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Orsak 3: [Ögonblicksbildstatusen kan inte hämtas eller så går det inte att ta en ögonblicksbild](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Orsak 4: [Konfigurationsalternativ för VM-Agent har inte angetts (för virtuella Linux-datorer)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed - Den virtuella datorn är i ett tillstånd av misslyckad etablering

**Felkod**: UserErrorVmProvisioningStateFailed<br>
**Felmeddelande:** Den virtuella datorn är i ett tillstånd som inte kunde etableras<br>

Det här felet uppstår när ett av tilläggsfelen placerar den virtuella datorn i etableringstillståndet.<br>**Öppna Azure-portalen > VM > inställningar > tillägg > tillägg status** och kontrollera om alla tillägg är i etablering **efterföljande** tillstånd. Mer information finns i [Etableringstillstånd](https://docs.microsoft.com/azure/virtual-machines/windows/states-lifecycle#provisioning-states).

- Om VMSnapshot-tillägget är i ett misslyckat tillstånd högerklickar du på det misslyckade tillägget och tar bort det. Utlösa en säkerhetskopiering på begäran. Den här åtgärden installerar om tilläggen och kör säkerhetskopieringsjobbet.  <br>
- Om något annat tillägg är i ett misslyckat tillstånd kan det störa säkerhetskopian. Kontrollera att dessa tilläggsproblem är lösta och försök igen säkerhetskopieringen.
- Om etableringstillståndet för den virtuella datorn är i ett uppdateringstillstånd kan det störa säkerhetskopian. Se till att det är felfritt och försök igen säkerhetskopieringen.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – Maxgränsen för samling med återställningspunkter har uppnåtts

**Felkod:** UserErrorRpCollectionLimitReached <br>
**Felmeddelande:** Maxgränsen för återställningspunktsamlingen har nåtts. <br>

- Det här problemet kan inträffa om det finns ett lås på resursgruppen för återställningspunkt som förhindrar automatisk rensning av återställningspunkter.
- Det här problemet kan också inträffa om flera säkerhetskopior utlöses per dag. För närvarande rekommenderar vi bara en säkerhetskopia per dag, eftersom omedelbara återställningspunkter behålls i 1-5 dagar per den konfigurerade ögonblicksbildkvarhållningen och endast 18 omedelbara RPs kan associeras med en virtuell dator vid en given tidpunkt. <br>
- Antalet återställningspunkter över återställningspunktsamlingar och resursgrupper för en virtuell dator får inte överstiga 18. Om du vill skapa en ny återställningspunkt tar du bort befintliga återställningspunkter.

Rekommenderad åtgärd:<br>
Lös problemet genom att ta bort låset på resursgruppen för den virtuella datorn och försöka utföra åtgärden igen för att utlösa rensning.
> [!NOTE]
> Säkerhetskopieringstjänsten skapar en separat resursgrupp än resursgruppen för den virtuella datorn för att lagra återställningsplatssamling. Kunder rekommenderas att inte låsa resursgruppen som skapats för användning av säkerhetskopieringstjänsten. Namnformatet för resursgruppen som skapats av tjänsten`<Geo>`Säkerhetskopiering är: AzureBackupRG_ _`<number>` T.ex AzureBackupRG_northeurope_1.

**Steg 1: [Ta bort lås från resursgruppen för återställningspunkt](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Steg 2: [Rensa insamling av återställningspunkt](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - Säkerhetskopiering har inte tillräcklig behörighet till nyckelvalvet för säkerhetskopiering av krypterade virtuella datorer

**Felkod**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Felmeddelande:** Säkerhetskopiering har inte tillräcklig behörighet till nyckelvalvet för säkerhetskopiering av krypterade virtuella datorer. <br>

För att en säkerhetskopiering ska lyckas med krypterade virtuella datorer måste den ha behörighet att komma åt nyckelvalvet. Behörigheter kan anges via [Azure-portalen](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) eller via [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork – Det gick inte att utföra åtgärden för ögonblicksbilden eftersom den virtuella datorn saknar nätverksanslutning

**Felkod:** ExtensionSnapshotFailedNoNetwork<br>
**Felmeddelande:** Åtgärden Ögonblicksbild misslyckades på grund av ingen nätverksanslutning på den virtuella datorn<br>

När du har registrerat och schemalägg en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med tillägget för säkerhetskopiering av virtuella datorer för att ta en ögonblicksbild i tid. Något av följande villkor kan förhindra att ögonblicksbilden utlöses. Om ögonblicksbilden inte utlöses kan ett säkerhetskopieringsfel uppstå. Slutför följande felsökningssteg och försök sedan igen:

**[Det går inte att hämta ögonblicksbildstatusen eller så kan en ögonblicksbild inte tas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - VMSnapshot-tilläggsåtgärden misslyckades

**Felkod**: ExtensionOperationFailedForManagedDisks <br>
**Felmeddelande:** VMSnapshot-tilläggsåtgärden misslyckades<br>

När du har registrerat och schemalägg en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med tillägget för säkerhetskopiering av virtuella datorer för att ta en ögonblicksbild i tid. Något av följande villkor kan förhindra att ögonblicksbilden utlöses. Om ögonblicksbilden inte utlöses kan ett säkerhetskopieringsfel uppstå. Slutför följande felsökningssteg i den ordning som anges och försök sedan igen:  
**Orsak 1: [Ögonblicksbildstatusen kan inte hämtas eller så går det inte att ta en ögonblicksbild](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 2: [Agenten är installerad i den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 3: [Agenten som är installerad i den virtuella datorn är föråldrad (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 – Backup kan inte köras, med ett internt fel

**Felkod**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Felmeddelande:** Säkerhetskopieringen misslyckades med ett internt fel - Försök igen på några minuter <br>

När du har registrerat och schemalägg en virtuell dator för Azure Backup-tjänsten initierar Säkerhetskopiering jobbet genom att kommunicera med tillägget för säkerhetskopiering av virtuella datorer för att ta en ögonblicksbild i tid. Något av följande villkor kan förhindra att ögonblicksbilden utlöses. Om ögonblicksbilden inte utlöses kan ett säkerhetskopieringsfel uppstå. Slutför följande felsökningssteg i den ordning som anges och försök sedan igen:  
**Orsak 1: [Agenten som är installerad i den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 2: [Agenten som är installerad i den virtuella datorn är föråldrad (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [Ögonblicksbildstatusen kan inte hämtas eller så går det inte att ta en ögonblicksbild](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 4: [Säkerhetskopieringstjänsten har inte behörighet att ta bort de gamla återställningspunkterna på grund av ett resursgrupplås](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize - Den konfigurerade diskstorleken/diskstorlekarna stöds för närvarande inte av Azure Backup

**Felkod**: UserErrorUnsupportedDiskSize <br>
**Felmeddelande:** Den konfigurerade diskstorleken/diskstorlekarna stöds för närvarande inte av Azure Backup. <br>

Säkerhetskopieringen kan misslyckas när du säkerhetskopierar en virtuell dator med en diskstorlek som är större än 32 TB. Säkerhetskopiering av krypterade diskar som är större än 4 TB i storlek stöds för närvarande inte. Kontrollera att diskstorleken/diskstorlekarna är mindre än eller lika med den gräns som stöds genom att dela diskarna.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - Det går inte att initiera säkerhetskopiering eftersom en annan säkerhetskopiering pågår

**Felkod:** UserErrorBackupOperationInProgress <br>
**Felmeddelande:** Det går inte att initiera säkerhetskopiering eftersom en annan säkerhetskopiering pågår<br>

Det senaste säkerhetskopieringsjobbet misslyckades eftersom det pågår ett befintligt säkerhetskopieringsjobb. Du kan inte starta ett nytt säkerhetskopieringsjobb förrän det aktuella jobbet är klart. Kontrollera att den pågående säkerhetskopieringsåtgärden är slutförd innan du utlöser eller schemalägger en annan säkerhetskopiering. Så här kontrollerar du statusen för säkerhetskopieringsjobb:

1. Logga in på Azure-portalen, klicka på **Alla tjänster**. Skriv Recovery Services och klicka på **Recovery Services-valv**. Listan över Recovery Services-valv visas.
2. Välj ett valv där säkerhetskopian har konfigurerats i listan över valv för återställningstjänster.
3. Klicka på **Säkerhetskopieringsjobb** på instrumentpanelsmenyn för valvet.
   - Om ett säkerhetskopieringsjobb pågår väntar du tills det slutförs eller avbryter säkerhetskopieringsjobbet.
     - Om du vill avbryta säkerhetskopieringsjobbet högerklickar du på säkerhetskopieringsjobbet och klickar på **Avbryt** eller använder [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
   - Om du har konfigurerat om säkerhetskopian i ett annat valv, se till att det inte finns några säkerhetskopieringsjobb som körs i det gamla valvet. Om den finns avbryter du säkerhetskopieringsjobbet.
     - Om du vill avbryta säkerhetskopieringsjobbet högerklickar du på säkerhetskopieringsjobbet och klickar på **Avbryt** eller använder [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Försök med säkerhetskopiering igen.

Om den schemalagda säkerhetskopieringen tar längre tid, i konflikt med nästa konfiguration för säkerhetskopiering, granska sedan [metodtips](backup-azure-vms-introduction.md#best-practices), [säkerhetskopieringsprestanda](backup-azure-vms-introduction.md#backup-performance)och [återställ övervägande](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - Säkerhetskopiering misslyckades på grund av ett fel. Mer information finns i Information om jobbfelsmeddelande

**Felkod**: UserErrorCrpReportedUserError <br>
**Felmeddelande:** Säkerhetskopieringen misslyckades på grund av ett fel. Mer information finns i Information om jobbfel.

Det här felet rapporteras från IaaS VM. Om du vill identifiera orsaken till problemet går du till inställningarna för Återställningstjänster. Under avsnittet **Övervakning** väljer du **Säkerhetskopieringsjobb** för att filtrera och visa status. Klicka på **Fel** om du vill granska den underliggande felmeddelandeinformationen. Vidta ytterligare åtgärder enligt rekommendationerna på felinformationssidan.

## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten är installerad i den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)

#### <a name="solution"></a>Lösning

Vm-agenten kan ha skadats eller så kan tjänsten ha stoppats. Om du installerar om VM-agenten får du den senaste versionen. Det hjälper också starta om kommunikationen med tjänsten.

1. Ta reda på om Tjänsten Windows Azure Guest Agent körs i VM-tjänsterna (services.msc). Försök att starta om Tjänsten Windows Azure Guest Agent och starta säkerhetskopian.
2. Om Tjänsten Windows Azure Guest Agent inte visas i tjänster går du till **Program och funktioner** på Kontrollpanelen för att avgöra om Tjänsten Windows Azure Guest Agent är installerad.
3. Om Windows Azure-gästagenten visas i **Program och funktioner**avinstallerar du Windows Azure-gästagenten.
4. Hämta och installera den [senaste versionen av agenten MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen.
5. Kontrollera att Windows Azure Guest Agent-tjänsterna visas i tjänster.
6. Kör en säkerhetskopiering på begäran:
   - Välj **Säkerhetskopiering nu**i portalen .

Kontrollera också att [Microsoft .NET 4.5 är installerat](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) i den virtuella datorn. .NET 4.5 krävs för att VM-agenten ska kunna kommunicera med tjänsten.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten som är installerad i den virtuella datorn är föråldrad (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning

De flesta agentrelaterade eller tilläggsrelaterade fel för virtuella Linux-datorer orsakas av problem som påverkar en inaktuell VM-agent. Så här felsöker du problemet:

1. Följ instruktionerna för [uppdatering av Linux VM-agenten](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Vi *rekommenderar starkt* att du uppdaterar agenten endast via en distributionsdatabas. Vi rekommenderar inte att du hämtar agentkoden direkt från GitHub och uppdaterar den. Om den senaste agenten för distributionen inte är tillgänglig kontaktar du distributionssupporten för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten går du till windows [Azure Linux-agentsidan](https://github.com/Azure/WALinuxAgent/releases) i GitHub-databasen.

2. Kontrollera att Azure-agenten körs på den virtuella datorn genom att köra följande kommando:`ps -e`

   Om processen inte körs startar du om den med hjälp av följande kommandon:

   - För Ubuntu:`service walinuxagent start`
   - För andra distributioner:`service waagent start`

3. [Konfigurera den automatiska omstartsagenten](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Kör en ny testsäkerhetskopiering. Om felet kvarstår samlar du in följande loggar från den virtuella datorn:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Om du behöver utförlig loggning för waagent gör du så här:

1. Leta upp följande rad i filen /etc/waagent.conf: **Aktivera utförlig loggning (y|n)**
2. Ändra värdet **Logs.Verbose** från *n* till *y*.
3. Spara ändringen och starta sedan om waagent genom att slutföra stegen som beskrivs tidigare i det här avsnittet.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>Konfigurationsalternativ för VM-Agent har inte angetts (för virtuella Linux-datorer)

En konfigurationsfil (/etc/waagent.conf) styr waagentens åtgärder. Configuration File Options **Extensions.Enable** och **Provisioning.Agent** bör ställas in på **y** för säkerhetskopiering att fungera.
En fullständig lista över konfigurationsfilalternativ för VM-Agent finns i<https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Det går inte att hämta ögonblicksbildstatusen eller så kan en ögonblicksbild inte tas

Den virtuella säkerhetskopian är beroende av att utfärda ett kommandot ögonblicksbild till det underliggande lagringskontot. Säkerhetskopiering kan misslyckas antingen för att den inte har åtkomst till lagringskontot eller på grund av att körningen av ögonblicksbildaktiviteten är försenad.

#### <a name="solution"></a>Lösning

Följande villkor kan leda till att ögonblicksbildaktiviteten misslyckas:

| Orsak | Lösning |
| --- | --- |
| Vm-statusen rapporteras felaktigt eftersom den virtuella datorn stängs av i RDP (Remote Desktop Protocol). | Om du stänger av den virtuella datorn i RDP kontrollerar du portalen för att avgöra om vm-statusen är korrekt. Om den inte är korrekt stänger du av den virtuella datorn i portalen med hjälp av alternativet **Avstängning** på instrumentpanelen för den virtuella datorn. |
| Den virtuella datorn kan inte hämta värd- eller infrastrukturadressen från DHCP. | DHCP måste vara aktiverat inuti gästen för att IaaS VM-säkerhetskopiering ska fungera. Om den virtuella datorn inte kan hämta värd- eller infrastrukturadressen från DHCP-svar 245 kan den inte hämta eller köra några tillägg. Om du behöver en statisk privat IP bör du konfigurera den via **Azure-portalen** eller **PowerShell** och se till att DHCP-alternativet i den virtuella datorn är aktiverat. [Läs mer](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) om hur du konfigurerar en statisk IP-adress med PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Ta bort lås från resursgruppen för återställningspunkt

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till **Alternativet Alla resurser**och välj resursgruppen för återställningsplatssamling i följande format AzureBackupRG_`<Geo>`_`<number>`.
3. I avsnittet **Inställningar** väljer du **Lås** för att visa låsen.
4. Om du vill ta bort låset markerar du ellipsen och klickar på **Ta bort**.

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>Rensa upp återställningspunkt samling

När låset har tagit bort måste återställningspunkterna rensas.

Om du tar bort resursgruppen för den virtuella datorn, eller själva den virtuella datorn, förblir ögonblicksbilder av snabb återställning av hanterade diskar aktiva och upphör att gälla enligt bevarandeuppsättningen. Om du vill ta bort ögonblicksbilder av omedelbar återställning (om du inte behöver dem längre) som lagras i samlingen för återställningspunkt rensar du rensningen av återställningspunktsamlingen enligt stegen nedan.

Så här rensar du återställningspunkterna:<br>

- [Rensa återställningsplatsinsamling genom att köra säkerhetskopiering på begäran](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Rensa återställningsplatsinsamling från Azure-portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Rensa återställningsplatsinsamling genom att köra säkerhetskopiering på begäran

När du har tagit bort låset utlöser du en säkerhetskopiering på begäran. Den här åtgärden säkerställer att återställningspunkterna rensas automatiskt. Förvänta dig att den här åtgärden på begäran misslyckas första gången. Det kommer dock att säkerställa automatisk rensning i stället för manuell borttagning av återställningspunkter. Efter rensningen bör nästa schemalagda säkerhetskopiering lyckas.

> [!NOTE]
> Automatisk rensning sker efter några timmars utlösande av säkerhetskopieringen på begäran. Om den schemalagda säkerhetskopian fortfarande misslyckas kan du prova att manuellt ta bort samlingen återställningspunkt med hjälp av stegen i listan [här](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Rensa återställningsplatsinsamling från Azure-portal <br>

Prova följande steg om du vill rensa samlingen för återställningspunkter manuellt, som inte rensas på grund av låset i resursgruppen:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på Alla **resurser**på **Hub-menyn** och välj gruppen Resurs`<Geo>``<number>` med följande format AzureBackupRG_ _ där den virtuella datorn finns.

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klicka på Resursgrupp visas **fönstret Översikt.**
4. Välj **Alternativet Visa dolda typer** om du vill visa alla dolda resurser. Markera samlingarna för återställningspunkt med`<VMName>``<number>`följande format AzureBackupRG_ _ .

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Klicka på **Ta bort** om du vill rensa samlingen för återställningspunkt.
6. Försök igen.

> [!NOTE]
 >Om resursen (RP-samling) har ett stort antal återställningspunkter kan det hända att du tar bort dem från portalen och misslyckas. Detta är ett känt CRP-problem, där alla återställningspunkter inte tas bort under den angivna tiden och driften time out; emellertid lyckas den bort borttagningfunktionen vanligt efter 2 eller 3 retries.
