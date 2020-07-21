---
title: Felsök problem med agent och tillägg
description: Symptom, orsaker och lösningar på Azure Backup fel som rör agent, tillägg och diskar.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 55af4bddb5a963a831c1438400a7a243cca20573
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538827"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Felsöka Azure Backup fel: problem med agenten eller tillägget

Den här artikeln innehåller fel söknings steg som kan hjälpa dig att lösa Azure Backup fel som rör kommunikation med VM-agenten och tillägget.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – Den virtuella datoragenten kunde inte kommunicera med Azure Backup

**Felkod**: UserErrorGuestAgentStatusUnavailable <br>
**Fel meddelande**: VM-agenten kan inte kommunicera med Azure Backup<br>

Azure VM-agenten kan vara stoppad, inaktuell, i ett inkonsekvent tillstånd eller inte installerad. Dessa tillstånd förhindrar att Azure Backups tjänsten utlöser ögonblicks bilder.

- **Öppna Azure Portal > vm > inställningar > fönstret egenskaper** > kontrol lera att VM- **status** är **igång** och att **agent status** är **klar**. Om den virtuella dator agenten har stoppats eller är i ett inkonsekvent tillstånd startar du om agenten<br>
  - För virtuella Windows-datorer följer du de här [stegen](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) för att starta om gäst agenten.<br>
  - För virtuella Linux-datorer följer du de här [stegen](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) för att starta om gäst agenten.
- **Öppna Azure Portal > VM > inställningar > tillägg** > se till att alla tillägg är i **etablerings läget lyckades** . Om inte, följer du dessa [steg](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) för att lösa problemet.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – Det gick inte att kommunicera med VM-agenten för ögonblicksbild av status

**Felkod**: GuestAgentSnapshotTaskStatusError<br>
**Fel meddelande**: det gick inte att kommunicera med VM-agenten för ögonblicks bild status <br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten startar backup jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:  

**Orsak 1: [agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Orsak 3: [ögonblicks bilds status kan inte hämtas eller så går det inte att hämta ögonblicks bilder](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Orsak 4: [konfigurations alternativ för virtuella dator agenter har inte angetts (för virtuella Linux-datorer)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**Orsak 5: [lösning för program kontroll blockerar IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed-den virtuella datorn är i ett misslyckat etablerings tillstånd

**Felkod**: UserErrorVmProvisioningStateFailed<br>
**Fel meddelande**: det gick inte att etablerings status för den virtuella datorn<br>

Felet uppstår när ett av de misslyckade tilläggen placerar den virtuella datorn i etablerings läget misslyckades.<br>**Öppna Azure Portal > VM > inställningar > tillägg > tillägg status** och kontrol lera om alla tillägg är i **etablerings läget lyckades** . Mer information finns i [etablerings tillstånd](../virtual-machines/windows/states-lifecycle.md#provisioning-states).

- Om VMSnapshot-tillägget är i ett felaktigt tillstånd högerklickar du på det misslyckade tillägget och tar bort det. Utlös en säkerhets kopiering på begäran. Med den här åtgärden installeras tilläggen om och säkerhets kopierings jobbet körs.  <br>
- Om ett annat tillägg är i ett felaktigt tillstånd kan det störa säkerhets kopieringen. Se till att de här tilläggs problemen är lösta och försök att säkerhetskopiera igen.
- Om etablerings statusen för den virtuella datorn är i ett uppdaterings tillstånd kan det störa säkerhets kopieringen. Se till att det är felfritt och försök att säkerhetskopiera igen.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – Maxgränsen för samling med återställningspunkter har uppnåtts

**Felkod**: UserErrorRpCollectionLimitReached <br>
**Fel meddelande**: Max gränsen för återställnings punkt samlingen har uppnåtts. <br>

- Det här problemet kan inträffa om en lås på återställnings punkt resurs gruppen förhindrar automatisk rensning av återställnings punkter.
- Det här problemet kan också inträffa om flera säkerhets kopieringar utlöses per dag. För närvarande rekommenderar vi bara en säkerhets kopiering per dag, eftersom återställnings punkterna för återställnings punkten behålls i 1-5 dagar per konfigurerad kvarhållning av ögonblicks bilder och endast 18 snabb RPs kan associeras med en virtuell dator vid en viss tidpunkt. <br>
- Antalet återställnings punkter i återställnings punkt samlingar och resurs grupper för en virtuell dator får inte överstiga 18. Ta bort befintliga återställnings punkter för att skapa en ny återställnings punkt.

Rekommenderad åtgärd:<br>
Lös problemet genom att ta bort låset på den virtuella datorns resurs grupp och försök igen för att utlösa rensning.
> [!NOTE]
> Backup-tjänsten skapar en separat resurs grupp än resurs gruppen för den virtuella datorn som ska lagra återställnings punkts samlingen. Kunderna uppmanas inte att låsa resurs gruppen som skapas för användning av säkerhets kopierings tjänsten. Namngivnings formatet för resurs gruppen som skapats av säkerhets kopierings tjänsten är: AzureBackupRG_ `<Geo>` _ `<number>` tex: AzureBackupRG_northeurope_1

**Steg 1: [ta bort låset från resurs gruppen för återställnings punkter](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Steg 2: [Rensa återställnings punkts samling](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured-säkerhets kopiering har inte tillräcklig behörighet till nyckel valvet för säkerhets kopiering av krypterade virtuella datorer

**Felkod**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Fel meddelande**: säkerhets kopieringen har inte tillräcklig behörighet för nyckel valvet för säkerhets kopiering av krypterade virtuella datorer. <br>

För att en säkerhets kopiering ska lyckas på krypterade virtuella datorer måste den ha behörighet att komma åt nyckel valvet. Behörigheter kan ställas in via [Azure Portal](./backup-azure-vms-encryption.md) eller via [PowerShell](./backup-azure-vms-automation.md#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork – Det gick inte att utföra åtgärden för ögonblicksbilden eftersom den virtuella datorn saknar nätverksanslutning

**Felkod**: ExtensionSnapshotFailedNoNetwork<br>
**Fel meddelande**: ögonblicks bild åtgärden misslyckades på grund av ingen nätverks anslutning på den virtuella datorn<br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten startar backup jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg och försök sedan igen:

**[Det går inte att hämta ögonblicks bild status, eller så kan en ögonblicks bild inte tas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks-VMSnapshot-tilläggs åtgärd misslyckades

**Felkod**: ExtensionOperationFailedForManagedDisks <br>
**Fel meddelande**: VMSnapshot-tilläggs åtgärd misslyckades<br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten startar backup jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:  
**Orsak 1: [ögonblicks bilds statusen kan inte hämtas eller så går det inte att hämta en ögonblicks bild](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 2: [agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 3: [agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 – Backup kan inte köras, med ett internt fel

**Felkod**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Fel meddelande**: säkerhets kopieringen misslyckades med ett internt fel. försök igen om några minuter <br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten initierar säkerhets kopieringen jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:  
**Orsak 1: [agenten som är installerad på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [ögonblicks bilds status kan inte hämtas eller så går det inte att hämta ögonblicks bilder](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 4: [säkerhets kopierings tjänsten har inte behörighet att ta bort de gamla återställnings punkterna på grund av ett resurs grupp lås](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize – den konfigurerade disk storleken/diskarna stöds inte för närvarande av Azure Backup

**Felkod**: UserErrorUnsupportedDiskSize <br>
**Fel meddelande**: den konfigurerade disk storleken/diskarna stöds för närvarande inte av Azure Backup. <br>

Säkerhets kopierings åtgärden kunde inte utföras vid säkerhets kopiering av en virtuell dator med en disk storlek som är större än 32 TB. Dessutom stöds inte säkerhets kopiering av krypterade diskar som är större än 4 TB för närvarande. Kontrol lera att disk storleken/diskarna är mindre än eller lika med den gräns som stöds genom att dela diskarna.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – det går inte att initiera säkerhets kopieringen eftersom en annan säkerhets kopiering pågår just nu

**Felkod**: UserErrorBackupOperationInProgress <br>
**Fel meddelande**: det går inte att initiera säkerhets kopieringen eftersom en annan säkerhets kopiering pågår just nu<br>

Det senaste säkerhets kopierings jobbet misslyckades på grund av att en befintlig säkerhets kopiering pågår. Du kan inte starta ett nytt säkerhets kopierings jobb förrän det aktuella jobbet har slutförts. Se till att den pågående säkerhets kopieringen har slutförts innan du utlöser eller schemalägger en annan säkerhets kopierings åtgärd. Gör så här för att kontrol lera status för säkerhets kopierings jobb:

1. Logga in på Azure Portal, klicka på **alla tjänster**. Skriv Recovery Services och klicka på **Recovery Services-valv**. Listan över Recovery Services-valv visas.
2. I listan över Recovery Services-valv väljer du ett valv där säkerhets kopian har kon figurer ATS.
3. På instrument panelen för valv klickar du på **säkerhets kopierings jobb** . då visas alla säkerhets kopierings jobb.
   - Om ett säkerhets kopierings jobb pågår väntar du tills det är klart eller Avbryt säkerhets kopierings jobbet.
     - Du avbryter säkerhets kopierings jobbet genom att högerklicka på säkerhets kopierings jobbet och klicka på **Avbryt** eller använda [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob).
   - Om du har konfigurerat om säkerhets kopian i ett annat valv kontrollerar du att inga säkerhets kopierings jobb körs i det gamla valvet. Avbryt säkerhets kopierings jobbet om det finns.
     - Om du vill avbryta säkerhetskopieringsjobbet högerklickar du på säkerhetskopieringsjobbet och klickar på **Avbryt** eller använder [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob)
4. Försök säkerhetskopiera igen.

Om den schemalagda säkerhets kopierings åtgärden tar längre tid, i konflikt med nästa säkerhets kopierings konfiguration, granskar du de [bästa metoderna](backup-azure-vms-introduction.md#best-practices), [säkerhets kopierings prestanda](backup-azure-vms-introduction.md#backup-performance)och [återställnings överväganden](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError – det gick inte att säkerhetskopiera på grund av ett fel. Mer information finns i informationen om jobb fel meddelanden

**Felkod**: UserErrorCrpReportedUserError <br>
**Fel meddelande**: säkerhets kopieringen misslyckades på grund av ett fel. Mer information finns i informationen om jobb fel meddelanden.

Det här felet rapporteras från den virtuella IaaS-datorn. Om du vill identifiera orsaken till problemet går du till inställningarna för Recovery Services valvet. Under avsnittet **övervakning** väljer du **säkerhets kopierings jobb** för att filtrera och visa statusen. Klicka på **fel** om du vill granska informationen om underliggande fel meddelanden. Vidta ytterligare åtgärder enligt rekommendationerna på sidan med fel information.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent-säkerhets kopieringen misslyckades: den här virtuella datorn skyddas inte (aktivt) av Azure Backup

**Felkod**: UserErrorBcmDatasourceNotPresent <br>
**Fel meddelande**: säkerhets kopieringen misslyckades: den här virtuella datorn skyddas inte (aktivt) av Azure Backup.

Kontrol lera att den aktuella virtuella datorn är aktivt (inte i paus läge) som skyddas av Azure Backup. För att lösa det här problemet kontrollerar du att den virtuella datorn är aktiv och försöker sedan igen.

## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)

#### <a name="solution"></a>Lösning

Den virtuella dator agenten kan ha skadats eller också har tjänsten stoppats. Genom att installera om VM-agenten får du den senaste versionen. Den hjälper också till att starta om kommunikationen med tjänsten.

1. Ta reda på om tjänsten Windows Azure gästa Gent körs i VM-tjänsterna (Services. msc). Försök att starta om tjänsten Windows Azure gästa Gent och starta säkerhets kopieringen.
2. Om tjänsten Windows Azure gästa Gent inte visas i tjänster går du till **program och funktioner** i kontroll panelen för att avgöra om tjänsten Windows Azure gästa Gent är installerad.
3. Om Windows Azures gästa Gent visas i **program och funktioner**avinstallerar du Windows Azures gästa Gent.
4. Hämta och installera den [senaste versionen av agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörs behörighet för att slutföra installationen.
5. Kontrol lera att Windows Azures gästa Gent tjänster visas i tjänster.
6. Köra en säkerhets kopiering på begäran:
   - I portalen väljer du **Säkerhetskopiera nu**.

Kontrol lera också att [Microsoft .NET 4,5 har installerats](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) på den virtuella datorn. .NET 4,5 krävs för att den virtuella dator agenten ska kunna kommunicera med tjänsten.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning

De flesta Agent-relaterade eller felrelaterade fel för virtuella Linux-datorer orsakas av problem som påverkar en gammal VM-agent. Du kan felsöka det här problemet genom att följa dessa allmänna rikt linjer:

1. Följ anvisningarna för [att uppdatera Linux VM-agenten](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > Vi *rekommenderar starkt* att du bara uppdaterar agenten via en distributions lagrings plats. Vi rekommenderar inte att du laddar ned agent koden direkt från GitHub och uppdaterar den. Om den senaste agenten för distributionen inte är tillgänglig kan du kontakta distributions supporten för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten går du till sidan [Windows Azure Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) i GitHub-lagringsplatsen.

2. Se till att Azure-agenten körs på den virtuella datorn genom att köra följande kommando:`ps -e`

   Om processen inte körs startar du om den med hjälp av följande kommandon:

   - För Ubuntu:`service walinuxagent start`
   - För andra distributioner:`service waagent start`

3. [Konfigurera agenten för automatisk omstart](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Kör en ny test säkerhets kopiering. Om felen kvarstår samlar du in följande loggar från den virtuella datorn:

   - /var/lib/waagent/*. XML
   - /var/log/waagent.log
   - /var/log/azure/*

Följ dessa steg om du behöver utförlig loggning för waagent:

1. Leta upp följande rad i/etc/waagent.conf-filen: **Aktivera utförlig loggning (y | n)**
2. Ändra **loggarna. Verbose** -värdet från *n* till *y*.
3. Spara ändringen och starta sedan om waagent genom att följa stegen som beskrivs tidigare i det här avsnittet.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>Konfigurations alternativen för VM-agenten har inte angetts (för virtuella Linux-datorer)

En konfigurations fil (/etc/waagent.conf) styr åtgärder för waagent. Alternativ för konfigurations fil **tillägg. Aktivera** måste anges till **y** och **etableringen. agenten** måste vara inställd på **Automatisk** för att säkerhets kopieringen ska fungera.
Fullständig lista över alternativ för konfigurations filen för virtuella datorer finns i<https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>Program kontroll lösningen blockerar IaaSBcdrExtension.exe

Om du kör [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (eller någon annan lösning för program kontroll) och reglerna är utgivare eller sökväg baserade, kan de blockera **IaaSBcdrExtension.exe** körbara filen från att köras.

#### <a name="solution"></a>Lösning

Undanta `/var/lib` sökvägen eller den körbara filen **IaaSBcdrExtension.exe** från AppLocker (eller annan program vara för program kontroll.)

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Det går inte att hämta ögonblicks bild status, eller så kan en ögonblicks bild inte tas

Säkerhets kopieringen av den virtuella datorn är beroende av att utfärda ett ögonblicks bild kommando till det underliggande lagrings kontot. Säkerhets kopieringen kan inte utföras eftersom den inte har åtkomst till lagrings kontot, eller eftersom körningen av ögonblicks bild aktiviteten är fördröjd.

#### <a name="solution"></a>Lösning

Följande villkor kan leda till att ögonblicks bild aktiviteten Miss kopierar:

| Orsak | Lösning |
| --- | --- |
| VM-statusen rapporteras felaktigt eftersom den virtuella datorn stängs av i Remote Desktop Protocol (RDP). | Om du stänger av den virtuella datorn i RDP, kontrollerar du portalen för att avgöra om status för den virtuella datorn är korrekt. Om den inte är korrekt stänger du den virtuella datorn i portalen med alternativet **avstängnings** alternativ på instrument panelen för den virtuella datorn. |
| Den virtuella datorn kan inte hämta värdens eller infrastrukturens adress från DHCP. | DHCP måste vara aktiverat i gästen för att IaaS VM-säkerhetskopiering ska fungera. Om den virtuella datorn inte kan hämta värd-eller Fabric-adressen från DHCP-svar 245 kan den inte ladda ned eller köra några tillägg. Om du behöver en statisk privat IP-adress bör du konfigurera den via **Azure Portal** eller **PowerShell** och kontrol lera att DHCP-alternativet i den virtuella datorn är aktiverat. [Lär dig mer](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) om att konfigurera en statisk IP-adress med PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Ta bort låset från återställnings punkt resurs gruppen

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till **alternativet alla resurser**, Välj resurs gruppen för återställnings punkt samling i följande format AzureBackupRG_ `<Geo>` _ _ `<number>` .
3. I avsnittet **Inställningar** väljer du **Lås** för att Visa låsen.
4. Om du vill ta bort låset väljer du ellipsen och klickar på **ta bort**.

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>Rensa återställnings punkt samling

När du har tagit bort låset måste återställnings punkterna rensas.

Om du tar bort resurs gruppen för den virtuella datorn eller själva datorn, förblir ögonblicks bilder av hanterade diskar aktiva och upphör att gälla enligt den kvarhållna uppsättningen. Om du vill ta bort ögonblicks bilderna för omedelbar återställning (om du inte behöver dem längre) som lagras i återställnings punkts samlingen rensar du återställnings punkts samlingen enligt stegen nedan.

Om du vill rensa återställnings punkterna följer du någon av metoderna:<br>

- [Rensa återställnings punkts samlingen genom att köra säkerhets kopiering på begäran](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Rensa återställnings punkt samling från Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Rensa återställnings punkts samlingen genom att köra säkerhets kopiering på begäran

När du har tagit bort låset utlöser du en säkerhets kopiering på begäran. Den här åtgärden ser till att återställnings punkterna rensas automatiskt. Vi förväntar dig att den här åtgärden på begäran upphör att fungera första gången; den kommer dock att säkerställa automatisk rensning i stället för manuell borttagning av återställnings punkter. Efter rensningen ska din nästa schemalagda säkerhets kopiering lyckas.

> [!NOTE]
> Automatisk rensning sker efter några timmar efter att säkerhets kopieringen har påbörjats. Om den schemalagda säkerhets kopieringen fortfarande Miss lyckas, försök att ta bort återställnings punkts samlingen manuellt med hjälp av stegen i listan [här](#clean-up-restore-point-collection-from-azure-portal)

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Rensa återställnings punkt samling från Azure Portal <br>

Om du vill rensa samlingen återställnings punkter manuellt, som inte avmarkeras på grund av resurs gruppens lås, kan du prova följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **alla resurser**på menyn **hubb** , Välj resurs gruppen med följande format AZUREBACKUPRG_ `<Geo>` _ `<number>` där den virtuella datorn finns.

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klicka på resurs grupp. **översikts** fönstret visas.
4. Välj alternativet **Visa dolda typer** om du vill visa alla dolda resurser. Välj återställnings punkt samlingarna med följande format AzureBackupRG_ `<VMName>` _ _ `<number>` .

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Rensa återställnings punkts samlingen genom att klicka på **ta bort** .
6. Försök att säkerhetskopiera igen.

> [!NOTE]
 >Om resursen (RP-samlingen) har ett stort antal återställnings punkter kan det hända att de tar bort dem från portalen och Miss lyckas. Detta är ett känt CRP-problem, där alla återställnings punkter inte tas bort under den fastställda tiden och drift tiden går ut. men borttagnings åtgärden slutförs vanligt vis efter 2 eller 3 försök.
