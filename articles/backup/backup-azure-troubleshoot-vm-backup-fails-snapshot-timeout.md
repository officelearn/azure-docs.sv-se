---
title: 'Felsöka Azure Backup fel: Gäst agentens status är inte tillgänglig'
description: Symptom, orsaker och lösningar på Azure Backup fel som rör agent, tillägg och diskar.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
keywords: Azure Backup; VM-agent; Nätverks anslutning;
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 391ad5c6535d457c2df988cd29d21e481310b17f
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061757"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Felsöka Azure Backup fel: Problem med agenten eller tillägget

Den här artikeln innehåller fel söknings steg som kan hjälpa dig att lösa Azure Backup fel som rör kommunikation med VM-agenten och tillägget.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable-VM-agenten kan inte kommunicera med Azure Backup

**Felkod**: UserErrorGuestAgentStatusUnavailable <br>
**Fel meddelande**: VM-agenten kan inte kommunicera med Azure Backup<br>

Azure VM-agenten kan vara stoppad, inaktuell, i ett inkonsekvent tillstånd eller inte installerad och förhindra att Azure Backup-tjänsten utlöser ögonblicks bilder.  

- Om den virtuella dator agenten har stoppats eller är i ett inkonsekvent tillstånd **startar du om agenten** och försöker säkerhetskopiera igen (försök med en ad hoc-säkerhetskopiering). Steg för att starta om agenten finns i virtuella [Windows-datorer](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) eller [virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).
- Om VM-agenten inte är installerad eller är inaktuell, installerar/uppdaterar du VM-agenten och försöker säkerhetskopiera igen. Anvisningar för hur du installerar/uppdaterar agenten finns i virtuella [Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) eller [virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).  

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError-det gick inte att kommunicera med VM-agenten för ögonblicks bild status

**Felkod**: GuestAgentSnapshotTaskStatusError<br>
**Fel meddelande**: Det gick inte att kommunicera med VM-agenten för ögonblicksbild av status <br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten initierar säkerhets kopieringen jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:  

**Orsak 1: [Agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Orsak 2: [Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Orsak 3: [Det går inte att hämta ögonblicks bild status, eller så kan en ögonblicks bild inte tas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Orsak 4: [Säkerhets kopierings tillägget kan inte uppdateras eller läsas in](#the-backup-extension-fails-to-update-or-load)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached-Max gränsen för återställnings punkt samlingen har uppnåtts

**Felkod**: UserErrorRpCollectionLimitReached <br>
**Fel meddelande**: Max gränsen för återställnings punkt samlingen har uppnåtts. <br>

- Det här problemet kan inträffa om en lås på återställnings punkt resurs gruppen förhindrar automatisk rensning av återställnings punkter.
- Det här problemet kan också inträffa om flera säkerhets kopieringar utlöses per dag. För närvarande rekommenderar vi bara en säkerhets kopiering per dag, eftersom återställnings punkterna för återställnings punkten behålls i 1-5 dagar per konfigurerad kvarhållning av ögonblicks bilder och endast 18 snabb RPs kan associeras med en virtuell dator vid en viss tidpunkt. <br>

Rekommenderad åtgärd:<br>
Lös problemet genom att ta bort låset på den virtuella datorns resurs grupp och försök igen för att utlösa rensning.
> [!NOTE]
> Backup-tjänsten skapar en separat resurs grupp än resurs gruppen för den virtuella datorn som ska lagra återställnings punkts samlingen. Kunderna uppmanas inte att låsa resurs gruppen som skapas för användning av säkerhets kopierings tjänsten. Namngivnings formatet för resurs gruppen som skapas av säkerhets kopierings tjänsten är: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Steg 1: [Ta bort låset från resurs gruppen för återställnings punkter](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Steg 2: [Rensa återställnings punkt samling](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured-säkerhets kopiering har inte tillräcklig behörighet till nyckel valvet för säkerhets kopiering av krypterade virtuella datorer

**Felkod**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Fel meddelande**: Säkerhets kopieringen har inte tillräcklig behörighet för nyckel valvet för säkerhets kopiering av krypterade virtuella datorer. <br>

För att en säkerhets kopiering ska lyckas på krypterade virtuella datorer måste den ha behörighet att komma åt nyckel valvet. Detta kan göras med hjälp av [Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) eller via [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork-åtgärden misslyckades på grund av ingen nätverks anslutning på den virtuella datorn

**Felkod**: ExtensionSnapshotFailedNoNetwork<br>
**Fel meddelande**: Det gick inte att utföra åtgärden för ögonblicksbilden eftersom den virtuella datorn saknar nätverksanslutning<br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten initierar säkerhets kopieringen jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:

**Orsak 1: [Det går inte att hämta ögonblicks bild status, eller så kan en ögonblicks bild inte tas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 2: [Säkerhets kopierings tillägget kan inte uppdateras eller läsas in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 3: [Den virtuella datorn har inte Internet åtkomst](#the-vm-has-no-internet-access)**

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks-VMSnapshot-tilläggs åtgärd misslyckades

**Felkod**: ExtensionOperationFailedForManagedDisks <br>
**Fel meddelande**: VMSnapshot-tilläggets åtgärd misslyckades<br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten initierar säkerhets kopieringen jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:  
**Orsak 1: [Det går inte att hämta ögonblicks bild status, eller så kan en ögonblicks bild inte tas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 2: [Säkerhets kopierings tillägget kan inte uppdateras eller läsas in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 3: [Agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 4: [Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2-säkerhets kopiering Miss lyckas, med ett internt fel

**Felkod**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Fel meddelande**: Säkerhets kopieringen misslyckades med ett internt fel. försök igen om några minuter <br>

När du har registrerat och schemalagt en virtuell dator för Azure Backup tjänsten initierar säkerhets kopieringen jobbet genom att kommunicera med tillägget för säkerhets kopiering av virtuella datorer för att ta en ögonblicks bild av tidpunkten. Något av följande villkor kan förhindra att ögonblicks bilden utlöses. Om ögonblicks bilden inte utlöses kan ett säkerhets kopierings fel uppstå. Slutför följande fel söknings steg i ordningen i listan och försök sedan igen:  
**Orsak 1: [Agenten som är installerad på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 2: [Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [Det går inte att hämta ögonblicks bild status, eller så kan en ögonblicks bild inte tas](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 4: [Säkerhets kopierings tillägget kan inte uppdateras eller läsas in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 5: Säkerhets kopierings tjänsten har inte behörighet att ta bort de gamla återställnings punkterna på grund av ett resurs grupp lås** <br>
**Orsak 6: [Den virtuella datorn har inte Internet åtkomst](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095-gb"></a>UserErrorUnsupportedDiskSize-Azure Backup stöder för närvarande inte disk storlekar som är större än 4095 GB

**Felkod**: UserErrorUnsupportedDiskSize <br>
**Fel meddelande**: För närvarande stöder Azure Backup inte disk storlekar som är större än 4095 GB <br>

Säkerhets kopierings åtgärden kunde inte utföras vid säkerhets kopiering av en virtuell dator med en disk storlek som är större än 4095 GB. Om du vill registrera dig för en begränsad, allmän för hands version av Azure Backup stöd för stora diskar för diskar som är större än 4 TB och upp till 30 TB i storlek, se den här [artikeln](backup-azure-vms-introduction.md#limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30tb).

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – det går inte att initiera säkerhets kopieringen eftersom en annan säkerhets kopiering pågår just nu

**Felkod**: UserErrorBackupOperationInProgress <br>
**Fel meddelande**: Det gick inte att initiera säkerhets kopieringen eftersom en annan säkerhets kopiering pågår just nu<br>

Det senaste säkerhets kopierings jobbet misslyckades på grund av att en befintlig säkerhets kopiering pågår. Du kan inte starta ett nytt säkerhets kopierings jobb förrän det aktuella jobbet har slutförts. Se till att den pågående säkerhets kopieringen har slutförts innan du utlöser eller schemalägger en annan säkerhets kopierings åtgärd. Kontrol lera status för säkerhets kopierings jobb genom att utföra stegen nedan:

1. Logga in på Azure Portal, klicka på **alla tjänster**. Skriv Recovery Services och klicka på **Recovery Services-valv**. Listan över Recovery Services-valv visas.
2. I listan över Recovery Services-valv väljer du ett valv där säkerhets kopian har kon figurer ATS.
3. På instrument panelen för valv klickar du på **säkerhets kopierings jobb** . då visas alla säkerhets kopierings jobb.

- Om ett säkerhets kopierings jobb pågår väntar du tills det är klart eller Avbryt säkerhets kopierings jobbet.
  - Du avbryter säkerhets kopierings jobbet genom att högerklicka på säkerhets kopierings jobbet och klicka på **Avbryt** eller använda [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
- Om du har konfigurerat om säkerhets kopian i ett annat valv kontrollerar du att inga säkerhets kopierings jobb körs i det gamla valvet. Avbryt säkerhets kopierings jobbet om det finns.
  - Om du vill avbryta säkerhetskopieringsjobbet högerklickar du på säkerhetskopieringsjobbet och klickar på **Avbryt** eller använder [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)

4. Försök säkerhetskopiera igen.

Om den schemalagda säkerhets kopierings åtgärden tar längre tid, i konflikt med nästa säkerhets kopierings konfiguration, granskar du de [bästa metoderna](backup-azure-vms-introduction.md#best-practices), [säkerhets kopierings prestanda](backup-azure-vms-introduction.md#backup-performance)och återställnings [överväganden](backup-azure-vms-introduction.md#backup-and-restore-considerations).

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

1. Följ anvisningarna för [att uppdatera Linux VM](../virtual-machines/linux/update-agent.md)-agenten.

   > [!NOTE]
   > Vi *rekommenderar starkt* att du bara uppdaterar agenten via en distributions lagrings plats. Vi rekommenderar inte att du laddar ned agent koden direkt från GitHub och uppdaterar den. Om den senaste agenten för distributionen inte är tillgänglig kan du kontakta distributions supporten för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten går du till sidan [Windows Azure Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) i GitHub-lagringsplatsen.

2. Se till att Azure-agenten körs på den virtuella datorn genom att köra följande kommando:`ps -e`

   Om processen inte körs startar du om den med hjälp av följande kommandon:

   - För Ubuntu:`service walinuxagent start`
   - För andra distributioner:`service waagent start`

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
| VM-statusen rapporteras felaktigt eftersom den virtuella datorn stängs av i Remote Desktop Protocol (RDP). | Om du stänger av den virtuella datorn i RDP, kontrollerar du portalen för att avgöra om status för den virtuella datorn är korrekt. Om den inte är korrekt stänger du den virtuella datorn i portalen med alternativet avstängnings alternativ på instrument panelen för den virtuella datorn. |
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

För virtuella Linux-datorer, om VMSnapshot-tillägget inte visas i Azure Portal, [uppdaterar du Azure Linux](../virtual-machines/linux/update-agent.md)-agenten och kör sedan säkerhets kopieringen.

När du slutför de här stegen installeras tillägget om under nästa säkerhets kopiering.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Ta bort låset från återställnings punkt resurs gruppen

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till **alternativet alla resurser**, Välj resurs gruppen för återställnings punkt samling i följande format`<Geo>`AzureBackupRG_`<number>`_.
3. I avsnittet **Inställningar** väljer du **Lås** för att Visa låsen.
4. Om du vill ta bort låset väljer du ellipsen och klickar på **ta bort**.

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>Rensa återställnings punkt samling

När du har tagit bort låset måste återställnings punkterna rensas. Om du vill rensa återställnings punkterna följer du någon av metoderna:<br>

- [Rensa återställnings punkts samlingen genom att köra Ad hoc-säkerhetskopiering](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
- [Rensa återställnings punkt samling från Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Rensa återställnings punkts samlingen genom att köra Ad hoc-säkerhetskopiering

När du har tagit bort låset utlöser du en ad hoc/manuell säkerhets kopiering. Detta säkerställer att återställnings punkterna rensas automatiskt. Den här ad hoc/manuell-åtgärden kan inte utföras första gången. den kommer dock att säkerställa automatisk rensning i stället för manuell borttagning av återställnings punkter. Efter rensningen bör nästa schemalagda säkerhets kopiering lyckas.

> [!NOTE]
> Automatisk rensning sker efter några timmar efter att ad hoc/manuell säkerhets kopiering har Aktiver ATS. Om den schemalagda säkerhets kopieringen fortfarande Miss lyckas, försök att ta bort återställnings punkts samlingen manuellt med hjälp av stegen i listan [här](#clean-up-restore-point-collection-from-azure-portal)

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Rensa återställnings punkt samling från Azure Portal <br>

Gör så här om du vill rensa samlingen återställnings punkter manuellt, som inte rensas på grund av låset på resurs gruppen:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **alla resurser**på menyn`<Geo>` **hubb** , Välj resurs gruppen med följande format AzureBackupRG_ _`<number>` där din virtuella dator finns.

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klicka på resurs grupp, bladet **Översikt** visas.
4. Välj alternativet **Visa dolda typer** om du vill visa alla dolda resurser. Välj återställnings punkt samlingarna med följande format`<VMName>`AzureBackupRG_`<number>`_.

    ![Ta bort lås](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Rensa återställnings punkts samlingen genom att klicka på **ta bort** .
6. Försök att säkerhetskopiera igen.

> [!NOTE]
 >Om resursen (RP-samlingen) har ett stort antal återställnings punkter kan det hända att de tar bort dem från portalen och Miss lyckas. Detta är ett känt CRP-problem, där alla återställnings punkter inte tas bort under den fastställda tiden och drift tiden går ut. men borttagnings åtgärden slutförs vanligt vis efter 2 eller 3 försök.
