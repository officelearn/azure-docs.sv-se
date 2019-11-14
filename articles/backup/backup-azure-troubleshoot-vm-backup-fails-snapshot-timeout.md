---
title: 'Felsöka Azure Backup fel: problem med agent och tillägg'
description: Symptom, orsaker och lösningar på Azure Backup fel som rör agent, tillägg och diskar.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
keywords: Azure Backup; VM-agent; Nätverks anslutning;
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 50db82206bbc0b98dcc80bd504022799011697d4
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074124"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Felsöka Azure Backup fel: problem med agenten eller tillägget

Den här artikeln innehåller fel söknings steg som kan hjälpa dig att lösa Azure Backup fel som rör kommunikation med VM-agenten och tillägget.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable-VM-agenten kan inte kommunicera med Azure Backup

**Felkod**: UserErrorGuestAgentStatusUnavailable <br>
**Fel meddelande**: VM-agenten kan inte kommunicera med Azure Backup<br>

Azure VM-agenten kan vara stoppad, inaktuell, i ett inkonsekvent tillstånd eller inte installerad och förhindra att Azure Backup-tjänsten utlöser ögonblicks bilder.  

- Om den virtuella dator agenten har stoppats eller är i ett inkonsekvent tillstånd **startar du om agenten** och försöker säkerhetskopiera igen (försök med en säkerhets kopiering på begäran). Anvisningar för att starta om agenten finns i [Virtuella Windows-datorer](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) eller [Virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).
- Om VM-agenten inte är installerad eller är inaktuell, installerar/uppdaterar du VM-agenten och försöker säkerhetskopiera igen. Anvisningar för hur du installerar/uppdaterar agenten finns i virtuella [Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) eller [virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).  

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError-det gick inte att kommunicera med VM-agenten för ögonblicks bild status

**Felkod**: GuestAgentSnapshotTaskStatusError<br>
**Fel meddelande**: det gick inte att kommunicera med VM-agenten för ögonblicks bild status <br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten initierar säkerhets kopieringen jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:  

**Orsak 1: [agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Orsak 3: [ögonblicks bilds status kan inte hämtas eller så går det inte att hämta ögonblicks bilder](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Orsak 4: [säkerhets kopierings tillägget kan inte uppdateras eller läsas in](#the-backup-extension-fails-to-update-or-load)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached-Max gränsen för återställnings punkt samlingen har uppnåtts

**Felkod**: UserErrorRpCollectionLimitReached <br>
**Fel meddelande**: Max gränsen för återställnings punkt samlingen har uppnåtts. <br>

- Det här problemet kan inträffa om en lås på återställnings punkt resurs gruppen förhindrar automatisk rensning av återställnings punkter.
- Det här problemet kan också inträffa om flera säkerhets kopieringar utlöses per dag. För närvarande rekommenderar vi bara en säkerhets kopiering per dag, eftersom återställnings punkterna för återställnings punkten behålls i 1-5 dagar per konfigurerad kvarhållning av ögonblicks bilder och endast 18 snabb RPs kan associeras med en virtuell dator vid en viss tidpunkt. <br>

Rekommenderad åtgärd:<br>
Lös problemet genom att ta bort låset på den virtuella datorns resurs grupp och försök igen för att utlösa rensning.
> [!NOTE]
> Backup-tjänsten skapar en separat resurs grupp än resurs gruppen för den virtuella datorn som ska lagra återställnings punkts samlingen. Kunderna uppmanas inte att låsa resurs gruppen som skapas för användning av säkerhets kopierings tjänsten. Namngivnings formatet för resurs gruppen som skapats av säkerhets kopierings tjänsten är: AzureBackupRG_`<Geo>`_`<number>` tex: AzureBackupRG_northeurope_1

**Steg 1: [ta bort låset från resurs gruppen för återställnings punkter](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Steg 2: [Rensa återställnings punkts samling](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured-säkerhets kopiering har inte tillräcklig behörighet till nyckel valvet för säkerhets kopiering av krypterade virtuella datorer

**Felkod**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Fel meddelande**: säkerhets kopieringen har inte tillräcklig behörighet för nyckel valvet för säkerhets kopiering av krypterade virtuella datorer. <br>

För att en säkerhets kopiering ska lyckas på krypterade virtuella datorer måste den ha behörighet att komma åt nyckel valvet. Detta kan göras med hjälp av [Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) eller via [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork-åtgärden misslyckades på grund av ingen nätverks anslutning på den virtuella datorn

**Felkod**: ExtensionSnapshotFailedNoNetwork<br>
**Fel meddelande**: ögonblicks bild åtgärden misslyckades på grund av ingen nätverks anslutning på den virtuella datorn<br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten initierar säkerhets kopieringen jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:

**Orsak 1: [ögonblicks bilds statusen kan inte hämtas eller så går det inte att hämta en ögonblicks bild](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 2: [säkerhets kopierings tillägget kan inte uppdateras eller läsas in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 3: [den virtuella datorn har inte Internet åtkomst](#the-vm-has-no-internet-access)**

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks-VMSnapshot-tilläggs åtgärd misslyckades

**Felkod**: ExtensionOperationFailedForManagedDisks <br>
**Fel meddelande**: VMSnapshot-tilläggs åtgärd misslyckades<br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten initierar säkerhets kopieringen jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:  
**Orsak 1: [ögonblicks bilds statusen kan inte hämtas eller så går det inte att hämta en ögonblicks bild](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 2: [säkerhets kopierings tillägget kan inte uppdateras eller läsas in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 3: [agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 4: [agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2-säkerhets kopiering Miss lyckas, med ett internt fel

**Felkod**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Fel meddelande**: säkerhets kopieringen misslyckades med ett internt fel. försök igen om några minuter <br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten initierar säkerhets kopieringen jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:  
**Orsak 1: [agenten som är installerad på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [ögonblicks bilds status kan inte hämtas eller så går det inte att hämta ögonblicks bilder](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 4: [säkerhets kopierings tillägget kan inte uppdateras eller läsas in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 5: säkerhets kopierings tjänsten har inte behörighet att ta bort de gamla återställnings punkterna på grund av ett resurs grupp lås** <br>
**Orsak 6: [den virtuella datorn har inte Internet åtkomst](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize – de konfigurerade disk storlekarna stöds för närvarande inte av Azure Backup.

**Felkod**: UserErrorUnsupportedDiskSize <br>
**Fel meddelande**: den konfigurerade disk storleken/diskarna stöds för närvarande inte av Azure Backup. <br>

Säkerhets kopierings åtgärden kunde inte utföras vid säkerhets kopiering av en virtuell dator med en disk storlek som är större än 32 TB. Dessutom stöds inte säkerhets kopiering av krypterade diskar som är större än 4 TB idag. Kontrol lera att disk storleken/diskarna är mindre än eller lika med den gräns som stöds genom att dela diskarna.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – det går inte att initiera säkerhets kopieringen eftersom en annan säkerhets kopiering pågår just nu

**Felkod**: UserErrorBackupOperationInProgress <br>
**Fel meddelande**: det går inte att initiera säkerhets kopieringen eftersom en annan säkerhets kopiering pågår just nu<br>

Det senaste säkerhets kopierings jobbet misslyckades på grund av att en befintlig säkerhets kopiering pågår. Du kan inte starta ett nytt säkerhets kopierings jobb förrän det aktuella jobbet har slutförts. Se till att den pågående säkerhets kopieringen har slutförts innan du utlöser eller schemalägger en annan säkerhets kopierings åtgärd. Kontrol lera status för säkerhets kopierings jobb genom att utföra stegen nedan:

1. Logga in på Azure Portal, klicka på **alla tjänster**. Skriv Recovery Services och klicka på **Recovery Services-valv**. Listan över Recovery Services-valv visas.
2. I listan över Recovery Services-valv väljer du ett valv där säkerhets kopian har kon figurer ATS.
3. På instrument panelen för valv klickar du på **säkerhets kopierings jobb** . då visas alla säkerhets kopierings jobb.
   - Om ett säkerhets kopierings jobb pågår väntar du tills det är klart eller Avbryt säkerhets kopierings jobbet.
     - Du avbryter säkerhets kopierings jobbet genom att högerklicka på säkerhets kopierings jobbet och klicka på **Avbryt** eller använda [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
   - Om du har konfigurerat om säkerhets kopian i ett annat valv kontrollerar du att inga säkerhets kopierings jobb körs i det gamla valvet. Avbryt säkerhets kopierings jobbet om det finns.
     - Om du vill avbryta säkerhetskopieringsjobbet högerklickar du på säkerhetskopieringsjobbet och klickar på **Avbryt** eller använder [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Försök säkerhetskopiera igen.

Om den schemalagda säkerhets kopierings åtgärden tar längre tid, i konflikt med nästa säkerhets kopierings konfiguration, granskar du de [bästa metoderna](backup-azure-vms-introduction.md#best-practices), [säkerhets kopierings prestanda](backup-azure-vms-introduction.md#backup-performance)och [återställnings överväganden](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-vm-has-no-internet-access"></a>Den virtuella datorn har inte Internet åtkomst

Den virtuella datorn har inte Internet åtkomst per distributions kravet. Eller så kan det ha begränsningar som förhindrar åtkomst till Azure-infrastrukturen.

För att det ska fungera korrekt kräver säkerhets kopierings tillägget anslutning till offentliga Azure-IP-adresser. Tillägget skickar kommandon till en Azure Storage-slutpunkt (HTTPs-URL) för att hantera ögonblicks bilder av den virtuella datorn. Om tillägget inte har åtkomst till det offentliga Internet Miss lyckas säkerhets kopieringen.

#### <a name="solution"></a>Lösning

Information om hur du löser nätverks problemet finns i [upprätta nätverks anslutning](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)

#### <a name="solution"></a>Lösning

Den virtuella dator agenten kan ha skadats eller också har tjänsten stoppats. Genom att installera om VM-agenten får du den senaste versionen. Den hjälper också till att starta om kommunikationen med tjänsten.

1. Ta reda på om tjänsten Windows Azure gästa Gent körs i VM-tjänsterna (Services. msc). Försök att starta om tjänsten Windows Azure gästa Gent och starta säkerhets kopieringen.
2. Om tjänsten Windows Azure gästa Gent inte visas i tjänster går du till **program och funktioner** i kontroll panelen för att avgöra om tjänsten Windows Azure gästa Gent är installerad.
3. Om Windows Azures gästa Gent visas i **program och funktioner**avinstallerar du Windows Azures gästa Gent.
4. Hämta och installera den [senaste versionen av agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörs behörighet för att slutföra installationen.
5. Kontrol lera att Windows Azures gästa Gent tjänster visas i tjänster.
6. Köra en säkerhets kopiering på begäran:
   - I portalen väljer du **Säkerhetskopiera nu**.

Kontrol lera också att [Microsoft .NET 4,5 har installerats](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) på den virtuella datorn. .NET 4,5 krävs för att den virtuella dator agenten ska kunna kommunicera med tjänsten.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning

De flesta Agent-relaterade eller felrelaterade fel för virtuella Linux-datorer orsakas av problem som påverkar en gammal VM-agent. Du kan felsöka det här problemet genom att följa dessa allmänna rikt linjer:

1. Följ anvisningarna för [att uppdatera Linux VM-agenten](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Vi *rekommenderar starkt* att du bara uppdaterar agenten via en distributions lagrings plats. Vi rekommenderar inte att du laddar ned agent koden direkt från GitHub och uppdaterar den. Om den senaste agenten för distributionen inte är tillgänglig kan du kontakta distributions supporten för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten går du till sidan [Windows Azure Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) i GitHub-lagringsplatsen.

2. Se till att Azure-agenten körs på den virtuella datorn genom att köra följande kommando: `ps -e`

   Om processen inte körs startar du om den med hjälp av följande kommandon:

   - För Ubuntu: `service walinuxagent start`
   - För andra distributioner: `service waagent start`

3. [Konfigurera agenten för automatisk omstart](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Kör en ny test säkerhets kopiering. Om felen kvarstår samlar du in följande loggar från den virtuella datorn:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Följ dessa steg om du behöver utförlig loggning för waagent:

1. Leta upp följande rad i/etc/waagent.conf-filen: **Aktivera utförlig loggning (y | n)**
2. Ändra **loggarna. Verbose** -värdet från *n* till *y*.
3. Spara ändringen och starta sedan om waagent genom att följa stegen som beskrivs tidigare i det här avsnittet.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Det går inte att hämta ögonblicks bild status, eller så kan en ögonblicks bild inte tas

Säkerhets kopieringen av den virtuella datorn är beroende av att utfärda ett ögonblicks bild kommando till det underliggande lagrings kontot. Säkerhets kopieringen kan inte utföras eftersom den inte har åtkomst till lagrings kontot, eller eftersom körningen av ögonblicks bild aktiviteten är fördröjd.

#### <a name="solution"></a>Lösning

Följande villkor kan leda till att ögonblicks bild aktiviteten Miss kopierar:

| Orsak | Lösning |
| --- | --- |
| VM-statusen rapporteras felaktigt eftersom den virtuella datorn stängs av i Remote Desktop Protocol (RDP). | Om du stänger av den virtuella datorn i RDP, kontrollerar du portalen för att avgöra om status för den virtuella datorn är korrekt. Om den inte är korrekt stänger du den virtuella datorn i portalen med alternativet **avstängnings** alternativ på instrument panelen för den virtuella datorn. |
| Den virtuella datorn kan inte hämta värdens eller infrastrukturens adress från DHCP. | DHCP måste vara aktiverat i gästen för att IaaS VM-säkerhetskopiering ska fungera. Om den virtuella datorn inte kan hämta värd-eller Fabric-adressen från DHCP-svar 245 kan den inte ladda ned eller köra några tillägg. Om du behöver en statisk privat IP-adress bör du konfigurera den via **Azure Portal** eller **PowerShell** och kontrol lera att DHCP-alternativet i den virtuella datorn är aktiverat. [Lär dig mer](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) om att konfigurera en statisk IP-adress med PowerShell.

### <a name="the-backup-extension-fails-to-update-or-load"></a>Säkerhets kopierings tillägget kan inte uppdateras eller läsas in

Om tillägg inte kan läsas in, Miss lyckas säkerhets kopieringen eftersom en ögonblicks bild inte kan tas.

#### <a name="solution"></a>Lösning

Avinstallera tillägget för att tvinga VMSnapshot-tillägget att läsas in på nytt. Nästa säkerhets kopierings försök läser in tillägget igen.

Så här avinstallerar du tillägget:

1. I [Azure Portal](https://portal.azure.com/)går du till den virtuella dator som har problem med säkerhets kopieringen.
2. Välj **inställningar**.
3. Välj **Tillägg**.
4. Välj **VMSnapshot-tillägg**.
5. Välj **Avinstallera**.

För virtuella Linux-datorer, om VMSnapshot-tillägget inte visas i Azure Portal, [uppdaterar du Azure Linux-agenten](../virtual-machines/linux/update-agent.md)och kör sedan säkerhets kopieringen.

När du slutför de här stegen installeras tillägget om under nästa säkerhets kopiering.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Ta bort låset från återställnings punkt resurs gruppen

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till **alternativet alla resurser**, Välj resurs gruppen för återställnings punkt samling i följande format AzureBackupRG_`<Geo>`_`<number>`.
3. I avsnittet **Inställningar** väljer du **Lås** för att Visa låsen.
4. Om du vill ta bort låset väljer du ellipsen och klickar på **ta bort**.

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>Rensa återställnings punkt samling

När du har tagit bort låset måste återställnings punkterna rensas.

Om du tar bort resurs gruppen för den virtuella datorn eller själva datorn, förblir ögonblicks bilder av hanterade diskar aktiva och upphör att gälla enligt den kvarhållna uppsättningen. För att ta bort ögonblicks bilderna för omedelbar återställning (om du inte behöver dem längre) som lagras i återställnings punkts samlingen rensar du återställnings punkts samlingen enligt stegen nedan.

Om du vill rensa återställnings punkterna följer du någon av metoderna:<br>

- [Rensa återställnings punkts samlingen genom att köra säkerhets kopiering på begäran](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Rensa återställnings punkt samling från Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Rensa återställnings punkts samlingen genom att köra säkerhets kopiering på begäran

När du har tagit bort låset utlöser du en säkerhets kopiering på begäran. Detta säkerställer att återställnings punkterna rensas automatiskt. Vi förväntar dig att den här åtgärden på begäran upphör att fungera första gången; den kommer dock att säkerställa automatisk rensning i stället för manuell borttagning av återställnings punkter. Efter rensningen bör nästa schemalagda säkerhets kopiering lyckas.

> [!NOTE]
> Automatisk rensning sker efter några timmar efter att säkerhets kopieringen har påbörjats. Om den schemalagda säkerhets kopieringen fortfarande Miss lyckas, försök att ta bort återställnings punkts samlingen manuellt med hjälp av stegen i listan [här](#clean-up-restore-point-collection-from-azure-portal)

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Rensa återställnings punkt samling från Azure Portal <br>

Gör så här om du vill rensa samlingen återställnings punkter manuellt, som inte rensas på grund av låset på resurs gruppen:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **alla resurser**på menyn **hubb** , Välj resurs gruppen med följande format AzureBackupRG_`<Geo>`_`<number>` där den virtuella datorn finns.

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klicka på resurs grupp, bladet **Översikt** visas.
4. Välj alternativet **Visa dolda typer** om du vill visa alla dolda resurser. Välj återställnings punkt samlingarna med följande format AzureBackupRG_`<VMName>`_`<number>`.

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Rensa återställnings punkts samlingen genom att klicka på **ta bort** .
6. Försök att säkerhetskopiera igen.

> [!NOTE]
 >Om resursen (RP-samlingen) har ett stort antal återställnings punkter kan det hända att de tar bort dem från portalen och Miss lyckas. Detta är ett känt CRP-problem, där alla återställnings punkter inte tas bort under den fastställda tiden och drift tiden går ut. men borttagnings åtgärden slutförs vanligt vis efter 2 eller 3 försök.
