---
title: Felsök säkerhets kopierings fel med Azure Virtual Machines
description: I den här artikeln får du lära dig hur du felsöker fel som påträffas med säkerhets kopiering och återställning av virtuella Azure-datorer.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: dacurwin
ms.openlocfilehash: 78de85cede228f4b1c6ff01388fd7a08f78aa74f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747190"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Felsöka säkerhets kopierings fel på virtuella Azure-datorer

Du kan felsöka fel som uppstår när du använder Azure Backup med informationen i listan nedan:

## <a name="backup"></a>Backup

Det här avsnittet beskriver felet vid säkerhets kopiering av virtuella Azure-datorer.

### <a name="basic-troubleshooting"></a>Grundläggande felsökning

* Se till att VM-agenten (WA-agenten) är den [senaste versionen](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* Se till att Windows eller Linux VM OS-versionen stöds, se [IaaS VM backup support Matrix](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Kontrol lera att ingen annan säkerhets kopierings tjänst körs.
  * För att se till att det inte finns några ögonblicks bilds tillägg måste [du avinstallera tillägg för att framtvinga inläsning och sedan försöka säkerhetskopiera igen](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-backup-extension-fails-to-update-or-load).
* Kontrol lera att den virtuella datorn är ansluten till Internet.
  * Kontrol lera att ingen annan säkerhets kopierings tjänst körs.
* Kontrol lera att tjänsten **Windows Azure gästa Gent** **körs**från `Services.msc`. Om tjänsten **Windows Azure gästa Gent** saknas installerar du den från [säkerhetskopiera virtuella Azure-datorer i ett Recovery Services valv](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* **Händelse loggen** kan visa säkerhets kopierings problem som kommer från andra säkerhets kopierings produkter, t. ex. Windows Server Backup, och inte på grund av Azure Backup. Använd följande steg för att fastställa om problemet är med Azure Backup:
  * Om det uppstår ett fel med en post **säkerhets kopia** i händelse källan eller meddelandet kontrollerar du om säkerhets kopieringen av Azure IaaS VM-säkerhetskopiering lyckades och om en återställnings punkt skapades med den önskade ögonblicks bild typen.
  * Om Azure Backup fungerar är problemet troligt vis en annan lösning för säkerhets kopiering.
  * Här är ett exempel på ett logg boks fel 517 där Azure Backup fungerade men "Windows Server Backup" misslyckades:<br>
    ![Windows Server Backup inte](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Om Azure Backup inte fungerar söker du efter motsvarande felkod i avsnittet Vanliga fel vid säkerhets kopiering av virtuella datorer i den här artikeln.

## <a name="common-issues"></a>Vanliga problem

Följande är vanliga problem med säkerhets kopierings fel på virtuella Azure-datorer.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>Tids gränsen nåddes under CopyingVHDsFromBackUpVaultTakingLongTime av säkerhetskopierade data från valvet

Felkod: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Fel meddelande: det gick inte att kopiera säkerhetskopierade data från valvet

Detta kan inträffa på grund av tillfälliga lagrings fel eller otillräckligt lagrings konto IOPS för säkerhets kopierings tjänsten för att överföra data till valvet inom tids gränsen. Konfigurera säkerhets kopiering av virtuella datorer med dessa [metod tips](backup-azure-vms-introduction.md#best-practices) och försök att säkerhetskopiera igen.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState-VM är inte i ett tillstånd som tillåter säkerhets kopieringar.

Felkod: UserErrorVmNotInDesirableState <br/>
Fel meddelande: den virtuella datorn är inte i ett tillstånd som tillåter säkerhets kopieringar.<br/>

Säkerhets kopierings åtgärden misslyckades eftersom den virtuella datorn är i ett felaktigt tillstånd. För att säkerhetskopieringen ska lyckas måste tillståndet för den virtuella datorn vara Körs, Stoppad eller Stoppad (frigjord).

* Om den virtuella datorn är i ett tillfälligt tillstånd mellan att **köra** och **stänga**av, väntar du tills status har ändrats. Utlös sedan säkerhets kopierings jobbet.
* Om den virtuella datorn är en virtuell Linux-dator och använder den säkerhetsförbättrade Linux-modulen för Linux, utelämnar du sökvägen **/var/lib/waagent** för Azure Linux-agenten från säkerhets principen och kontrollerar att säkerhets kopierings tillägget är installerat.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed – det gick inte att låsa en eller flera monterings punkter för den virtuella datorn för att ta en konsekvent fil system ögonblicks bild

Felkod: UserErrorFsFreezeFailed <br/>
Fel meddelande: det gick inte att låsa en eller flera monterings punkter för den virtuella datorn för att ta en konsekvent fil system ögonblicks bild.

* Kontrol lera fil systemets tillstånd för alla monterade enheter med kommandot **tune2fs** , till exempel **tune2fs-l/dev/sdb1 \\** .\| grep- **filsystem-tillstånd**.
* Demontera enheterna för vilka fil systemets tillstånd inte har rensats, med kommandot **umount** .
* Kör en konsekvens kontroll av fil systemet på dessa enheter med kommandot **fsck** .
* Montera enheterna igen och försök att säkerhetskopiera igen.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC-Extension-installationen/åtgärden misslyckades på grund av ett COM+-fel

Felkod: ExtensionSnapshotFailedCOM <br/>
Fel meddelande: ögonblicks bild åtgärden misslyckades på grund av ett COM+-fel

Felkod: ExtensionInstallationFailedCOM  <br/>
Fel meddelande: tilläggs installation/åtgärd misslyckades på grund av ett COM+-fel

Felkod: ExtensionInstallationFailedMDTC <br/>
Fel meddelande: tilläggs installationen misslyckades med felet "COM+ kunde inte kommunicera med Microsoft koordinator för distribuerad transaktion <br/>

Säkerhets kopieringen misslyckades på grund av ett problem med Windows-tjänstens **com+-system** program.  Följ dessa anvisningar för att lösa problemet:

* Försök starta/starta om Windows-tjänsten **com+-systemprogram** (från en upphöjd kommando tolk **-net start COMSysApp**).
* Se till att **koordinator för distribuerad transaktion** -tjänsten körs som **nätverks tjänst** konto. Om inte, ändrar du den så att den körs som **nätverks tjänst** konto och startar om **com+-system programmet**.
* Om du inte kan starta om tjänsten installerar du om **koordinator för distribuerad transaktion** tjänsten genom att följa stegen nedan:
  * Stoppa MSDTC-tjänsten
  * Öppna en kommandotolk (cmd)
  * Kör kommandot "MSDTC-Uninstall"
  * Kör kommandot "MSDTC-Install"
  * Starta MSDTC-tjänsten
* Starta Windows-tjänsten **com+-system tillämpning**. När **com+-system programmet** startar utlöser du ett säkerhets kopierings jobb från Azure Portal.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState-åtgärden misslyckades eftersom VSS-skrivare var i ett felaktigt tillstånd

Felkod: ExtensionFailedVssWriterInBadState <br/>
Fel meddelande: ögonblicks bild åtgärden misslyckades eftersom VSS-skrivare befann sig i ett felaktigt tillstånd.

Starta om VSS-skrivare som är i ett felaktigt tillstånd. Kör ```vssadmin list writers```i en upphöjd kommando tolk. Utdata innehåller alla VSS-skrivare och deras tillstånd. För varje VSS-skrivare med ett tillstånd som inte är **[1] stabil**för att starta om VSS Writer kör du följande kommandon från en upphöjd kommando tolk:

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure – det gick inte att parsa konfigurationen för säkerhets kopierings tillägget

Felkod: ExtensionConfigParsingFailure<br/>
Fel meddelande: det gick inte att parsa konfigurationen för säkerhets kopierings tillägget.

Det här felet inträffar på grund av ändrade behörigheter i **MachineKeys** -katalogen: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Kör följande kommando och kontrol lera att behörigheterna för **MachineKeys** -katalogen är standardvärden:**icacls%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Standard behörigheterna är följande:

* Alla: (R, W)
* BUILTIN\Administrators: (F)

Om du ser behörigheter i **MachineKeys** -katalogen som skiljer sig från standardvärdena följer du dessa steg för att åtgärda behörighet, ta bort certifikatet och utlösa säkerhets kopieringen:

1. Åtgärda behörigheter för **MachineKeys** -katalogen. Genom att använda säkerhets egenskaper i Utforskaren och avancerade säkerhets inställningar i katalogen återställer du behörighet tillbaka till standardvärdena. Ta bort alla användar objekt utom standardvärdena från katalogen och se till att behörigheten **alla** har särskild åtkomst enligt följande:

   * Visa mapp/läsa data
   * Läsa attribut
   * Läsa utökade attribut
   * Skapa filer/skriva data
   * Skapa mappar/lägga till data
   * Skriva attribut
   * Skriva utökade attribut
   * Läs behörigheter
2. Ta bort alla certifikat som har **utfärdats till** är den klassiska distributions modellen eller **Windows Azure CRP Certificate Generator**:

   * [Öppna certifikat på en lokal dator konsol](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
   * Under **personliga** > **certifikat**, ta bort alla certifikat som har **utfärdats till** är den klassiska distributions modellen eller **Windows Azure CRP Certificate Generator**.
3. Utlös ett jobb för säkerhets kopiering av virtuella datorer.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState-Extension-tillstånd stöds inte för säkerhets kopiering

Felkod: ExtensionStuckInDeletionState <br/>
Fel meddelande: det går inte att använda tilläggs tillstånd för säkerhets kopiering

Säkerhets kopierings åtgärden misslyckades på grund av ett inkonsekvent tillstånd för säkerhets kopierings tillägget. Följ dessa anvisningar för att lösa problemet:

* Kontrollera att gästagenten är installerad och svarar.
* Från Azure Portal går du till **virtuell dator** > **alla inställningar** > **tillägg**
* Välj säkerhetskopieringstillägget VmSnapshot eller VmSnapshotLinux och klicka på **Avinstallera**.
* Försök att säkerhetskopiera igen efter att du har tagit bort säkerhets kopierings tillägget
* Vid nästföljande säkerhetskopiering installeras det nya tillägget med önskat tillstånd.

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError-åtgärden misslyckades eftersom gränsen för ögonblicks bilder har överskridits för vissa av diskarna

Felkod: ExtensionFailedSnapshotLimitReachedError  <br/>
Fel meddelande: ögonblicks bild åtgärden misslyckades eftersom ögonblicks bilds gränsen har överskridits för vissa av diskarna som är anslutna

Det gick inte att utföra ögonblicks bild åtgärden eftersom gränsen för ögonblicks bilder har överskridits för vissa av de anslutna diskarna. Slutför nedanstående fel söknings steg och försök sedan igen.

* Ta bort disk-BLOB-ögonblicksbilder som inte krävs. Var försiktig med att inte ta bort disk-blob. det är bara ögonblicks bilds blobbar som ska tas bort.
* Om mjuk borttagning är aktiverat på VM disk Storage-konton, konfigurerar du mjuk borttagnings kvarhållning, så att befintliga ögonblicks bilder är mindre än det maximalt tillåtna antalet vid någon tidpunkt.
* Om Azure Site Recovery har Aktiver ATS i den säkerhetskopierade virtuella datorn utför du stegen nedan:

  * Se till att värdet för **isanysnapshotfailed** är inställt på falskt i/etc/Azure/vmbackup.conf
  * Schemalägg Azure Site Recovery vid en annan tidpunkt, så att det inte står i konflikt med säkerhets kopierings åtgärden.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive-åtgärden misslyckades på grund av otillräckliga VM-resurser.

Felkod: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Fel meddelande: ögonblicks bild åtgärden misslyckades på grund av otillräckliga VM-resurser.

Det gick inte att säkerhetskopiera den virtuella datorn på grund av fördröjning i nätverks anrop medan ögonblicks bild åtgärden utfördes. Du löser problemet genom att utföra steg 1. Om problemet kvarstår kan du prova steg 2 och 3.

**Steg 1**: skapa en ögonblicks bild via värden

Från en upphöjd kommandotolk (administratör) kör du kommandot nedan:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Detta säkerställer att ögonblicksbilderna tas via värden i stället för gästen. Försök att utföra säkerhetskopieringsåtgärden igen.

**Steg 2**: försök att ändra schemat för säkerhets kopiering till en tidpunkt då den virtuella datorn är under mindre belastning (mindre processor/IOps osv.)

**Steg 3**: försök att [öka storleken på den virtuella datorn](https://azure.microsoft.com/blog/resize-virtual-machines/) och försök igen

## <a name="common-vm-backup-errors"></a>Vanliga fel vid säkerhets kopiering av virtuella datorer

| Felinformation | Lösning |
| ------ | --- |
| **Felkod**: 320001, ResourceNotFound <br/> **Fel meddelande**: det gick inte att utföra åtgärden eftersom den virtuella datorn inte längre finns. <br/> <br/> **Felkod**: 400094, BCMV2VMNotFound <br/> **Fel meddelande**: den virtuella datorn finns inte <br/> <br/>  Det gick inte att hitta någon virtuell Azure-dator.  |Felet uppstår när den primära virtuella datorn tas bort, men säkerhets kopierings principen söker fortfarande efter en virtuell dator som ska säkerhets kopie ras. Åtgärda felet genom att utföra följande steg: <ol><li> Återskapa den virtuella datorn med samma namn och samma resurs grupp namn, **moln tjänst namn**,<br>**eller**</li><li> Sluta skydda den virtuella datorn med eller utan att ta bort säkerhetskopierade data. Mer information finns i [stoppa skyddet av virtuella datorer](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| **Felkod**: UserErrorVmProvisioningStateFailed<br/> **Fel meddelande**: den virtuella datorn har ett misslyckat etablerings tillstånd: <br>Starta om den virtuella datorn och kontrol lera att den virtuella datorn körs eller stängs av. | Felet uppstår när ett av de misslyckade tilläggen placerar den virtuella datorn i ett misslyckat etablerings tillstånd. Gå till listan tillägg, kontrol lera om det finns ett misslyckat tillägg, ta bort det och försök att starta om den virtuella datorn. Om alla tillägg är i körnings tillstånd kontrollerar du om VM-agenttjänsten körs. Om inte, startar du om VM-agenttjänsten. |
|**Felkod**: UserErrorBCMPremiumStorageQuotaError<br/> **Fel meddelande**: det gick inte att kopiera ögonblicks bilden av den virtuella datorn eftersom det inte finns tillräckligt med ledigt utrymme på lagrings kontot | För Premium-VM: ar på VM-säkerhetskopiering stack v1 kopieras ögonblicks bilden till lagrings kontot. Det här steget ser till att säkerhets kopierings hanterings trafiken, som fungerar på ögonblicks bilden, inte begränsar antalet IOPS som är tillgängligt för programmet med hjälp av Premium diskar. <br><br>Vi rekommenderar att du endast tilldelar 50 procent, 17,5 TB, av det totala lagrings konto utrymmet. Sedan kan Azure Backups tjänsten kopiera ögonblicks bilden till lagrings kontot och överföra data från den här kopierade platsen i lagrings kontot till valvet. |
| **Felkod**: 380008, AzureVmOffline <br/> **Fel meddelande**: det gick inte att installera Microsoft Recovery Services-tillägget eftersom den virtuella datorn inte körs | VM-agenten är en förutsättning för Azure Recovery Services-tillägget. Installera agenten för den virtuella Azure-datorn och starta om registrerings åtgärden. <br> <ol> <li>Kontrol lera att den virtuella dator agenten är korrekt installerad. <li>Kontrol lera att flaggan på VM-konfigurationen är korrekt inställd.</ol> Läs mer om hur du installerar VM-agenten och hur du verifierar installationen av VM-agenten. |
| **Felkod**: ExtensionSnapshotBitlockerError <br/> **Fel meddelande**: det gick inte att utföra ögonblicks bild åtgärden med den tjänsten Volume Shadow Copy (VSS) åtgärds fel **enheten är låst av BitLocker-diskkryptering. Du måste låsa upp den här enheten från kontroll panelen.** |Inaktivera BitLocker för alla enheter på den virtuella datorn och kontrol lera om problemet med VSS är löst. |
| **Felkod**: VmNotInDesirableState <br/> **Fel meddelande**: den virtuella datorn är inte i ett tillstånd som tillåter säkerhets kopieringar. |<ul><li>Om den virtuella datorn är i ett tillfälligt tillstånd mellan att **köra** och **stänga**av, väntar du tills status har ändrats. Utlös sedan säkerhets kopierings jobbet. <li> Om den virtuella datorn är en virtuell Linux-dator och använder den säkerhetsförbättrade Linux-modulen för Linux, utelämnar du sökvägen **/var/lib/waagent** för Azure Linux-agenten från säkerhets principen och kontrollerar att säkerhets kopierings tillägget är installerat.  |
| VM-agenten finns inte på den virtuella datorn: <br>Installera eventuella nödvändiga komponenter och VM-agenten. Starta sedan om åtgärden. |Läs mer om [installation av VM-agenten och hur du verifierar installationen av VM-agenten](#vm-agent). |
| **Felkod**: ExtensionSnapshotFailedNoSecureNetwork <br/> **Fel meddelande**: ögonblicks bild åtgärden misslyckades på grund av att det inte gick att skapa en säker kanal för nätverkskommunikation. | <ol><li> Öppna Registereditorn genom att köra **regedit. exe** i förhöjd läge. <li> Identifiera alla versioner av .NET Framework som finns i systemet. De finns under hierarkin för register nyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Lägg till följande nyckel för varje .NET Framework som finns i register nyckeln: <br> **SchUseStrongCrypto "= DWORD: 00000001**. </ol>|
| **Felkod**: ExtensionVCRedistInstallationFailure <br/> **Fel meddelande**: ögonblicks bild åtgärden misslyckades på grund av att det C++ inte gick att installera Visual redistributable för Visual Studio 2012. | Gå till C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion och installera vcredist2013_x64.<br/>Kontrol lera att värdet för register nyckeln som tillåter tjänst installationen har värdet korrekt. Det vill säga ange **startvärdet** i **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** till **3** och inte **4**. <br><br>Om du fortfarande har problem med installationen startar du om installations tjänsten genom att köra **msiexec/unregister** följt av **msiexec/register** från en upphöjd kommando tolk.  |

## <a name="jobs"></a>Jobb

| Felinformation | Lösning |
| --- | --- |
| Annullering stöds inte för den här jobb typen: <br>Vänta tills jobbet har slutförts. |Ingen |
| Jobbet är inte i ett cancelable-tillstånd: <br>Vänta tills jobbet har slutförts. <br>**eller**<br> Det valda jobbet är inte i ett cancelable-tillstånd: <br>Vänta tills jobbet har slutförts. |Det är troligt att jobbet är nästan klart. Vänta tills jobbet är klart.|
| Säkerhets kopieringen kan inte avbryta jobbet eftersom det inte pågår: <br>Annullering stöds bara för pågående jobb. Försök att avbryta ett pågående jobb. |Felet beror på ett överförings tillstånd. Vänta en minut och försök att avbryta åtgärden igen. |
| Säkerhets kopieringen kunde inte avbryta jobbet: <br>Vänta tills jobbet har slutförts. |Ingen |

## <a name="restore"></a>Återställ

| Felinformation | Lösning |
| --- | --- |
| Återställningen misslyckades med ett internt moln fel. |<ol><li>Den moln tjänst som du försöker återställa till har kon figurer ATS med DNS-inställningar. Du kan kontrol lera följande: <br>**$Deployment = get-AzureDeployment-ServiceName "ServiceName"-fack "produktion" Get-AzureDns-DnsSettings $Deployment. DnsSettings**.<br>Om **adress** har kon figurer ATS konfigureras DNS-inställningarna.<br> <li>Den moln tjänst som du försöker återställa till har kon figurer ATS med **reservedip**och befintliga virtuella datorer i moln tjänsten är i stoppat läge. Du kan kontrol lera att en moln tjänst har reserverat en IP-adress med hjälp av följande PowerShell-cmdletar: **$Deployment = get-AzureDeployment-ServiceName "ServiceName"-plats "produktion" $DEP. ReservedIPName**. <br><li>Du försöker återställa en virtuell dator med följande särskilda nätverkskonfigurationer i samma moln tjänst: <ul><li>Virtuella datorer under belastnings Utjämnings konfiguration, intern och extern.<li>Virtuella datorer med flera reserverade IP-adresser. <li>Virtuella datorer med flera nätverkskort. </ul><li>Välj en ny moln tjänst i användar gränssnittet eller se [återställnings överväganden](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) för virtuella datorer med särskilda nätverkskonfigurationer.</ol> |
| Det valda DNS-namnet har redan tagits: <br>Ange ett annat DNS-namn och försök igen. |Det här DNS-namnet refererar till moln tjänstens namn, vanligt vis slutar med **. cloudapp.net**. Det här namnet måste vara unikt. Om du får det här felet måste du välja ett annat namn för virtuell dator under återställningen. <br><br> Det här felet visas endast för användare av Azure Portal. Återställnings åtgärden via PowerShell slutförs eftersom den återställer endast diskarna och inte skapar den virtuella datorn. Felet kommer att visas när den virtuella datorn skapas explicit av dig efter disk återställnings åtgärden. |
| Den angivna konfigurationen för virtuellt nätverk är felaktig: <br>Ange en annan konfiguration för virtuellt nätverk och försök igen. |Ingen |
| Den angivna moln tjänsten använder en reserverad IP-adress som inte matchar konfigurationen för den virtuella dator som återställs: <br>Ange en annan moln tjänst som inte använder en reserverad IP-adress. Eller Välj en annan återställnings punkt att återställa från. |Ingen |
| Moln tjänsten har nått gränsen för antalet ingångs slut punkter: <br>Försök igen genom att ange en annan moln tjänst eller genom att använda en befintlig slut punkt. |Ingen |
| Recovery Services valvet och mål lagrings kontot finns i två olika regioner: <br>Se till att det lagrings konto som anges i återställnings åtgärden finns i samma Azure-region som Recovery Services-valvet. |Ingen |
| Det lagrings konto som har angetts för återställnings åtgärden stöds inte: <br>Endast Basic-eller standard-lagrings konton med lokalt redundanta eller geo-redundanta replikeringsinställningar stöds. Välj ett lagrings konto som stöds. |Ingen |
| Den angivna lagrings konto typen för återställnings åtgärden är inte online: <br>Kontrol lera att lagrings kontot som angetts i återställnings åtgärden är online. |Det här felet kan inträffa på grund av ett tillfälligt fel i Azure Storage eller på grund av ett avbrott. Välj ett annat lagrings konto. |
| Resurs grupps kvoten har uppnåtts: <br>Ta bort några resurs grupper från Azure Portal eller kontakta Azure-supporten för att öka gränserna. |Ingen |
| Det valda under nätet finns inte: <br>Välj ett undernät som finns. |Ingen |
| Säkerhets kopierings tjänsten har inte behörighet att komma åt resurser i din prenumeration. |Lös problemet genom att först återställa diskarna genom att följa stegen i [återställa säkerhetskopierade diskar](backup-azure-arm-restore-vms.md#restore-disks). Använd sedan PowerShell-stegen i [skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Säkerhets kopiering eller återställning tar tid

Om säkerhets kopieringen tar mer än 12 timmar, eller om det tar längre tid än 6 timmar att återställa, kan du granska [metod tips](backup-azure-vms-introduction.md#best-practices)och [prestanda överväganden](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM-agent

### <a name="set-up-the-vm-agent"></a>Konfigurera VM-agenten

Normalt finns VM-agenten redan i virtuella datorer som skapas från Azure-galleriet. Men virtuella datorer som migreras från lokala data Center kommer inte att ha VM-agenten installerad. VM-agenten måste installeras explicit för de virtuella datorerna.

#### <a name="windows-vms"></a>Virtuella Windows-datorer

* Ladda ned och installera [agentens MSI-fil](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörs behörighet för att slutföra installationen.
* För virtuella datorer som skapats med den klassiska distributions modellen [uppdaterar du egenskapen VM](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad. Det här steget krävs inte för Azure Resource Manager virtuella datorer.

#### <a name="linux-vms"></a>Virtuella Linux-datorer

* Installera den senaste versionen av agenten från distributions platsen. Mer information om paket namnet finns i Linux- [agentens lagrings plats](https://github.com/Azure/WALinuxAgent).
* För virtuella datorer som skapats med den klassiska distributions modellen [använder du den här bloggen](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att uppdatera egenskapen VM och kontrol lera att agenten är installerad. Det här steget krävs inte för virtuella Resource Manager-datorer.

### <a name="update-the-vm-agent"></a>Uppdatera VM-agenten

#### <a name="windows-vms"></a>Virtuella Windows-datorer

* Om du vill uppdatera VM-agenten installerar du om [binärfilerna för VM-agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Innan du uppdaterar agenten ska du kontrol lera att inga säkerhets kopierings åtgärder sker under uppdateringen av VM-agenten.

#### <a name="linux-vms"></a>Virtuella Linux-datorer

* Uppdatera Linux VM-agenten genom att följa anvisningarna i artikeln [Uppdatera Linux VM-agenten](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Använd alltid distributions platsen för att uppdatera agenten.

    Hämta inte agent koden från GitHub. Om den senaste agenten inte är tillgänglig för din distribution kan du kontakta distributions supporten för instruktioner om hur du hämtar den senaste agenten. Du kan också kontrol lera den senaste informationen om [Windows Azure Linux-agenten](https://github.com/Azure/WALinuxAgent/releases) i GitHub-lagringsplatsen.

### <a name="validate-vm-agent-installation"></a>Verifiera installation av VM-agent

Verifiera VM-agentens version på virtuella Windows-datorer:

1. Logga in på den virtuella Azure-datorn och navigera till mappen **C:\WindowsAzure\Packages**. Du bör hitta filen **WaAppAgent. exe** .
2. Högerklicka på filen och gå till **Egenskaper**. Välj fliken **information** . Fältet **produkt version** ska vara 2.6.1198.718 eller högre.

## <a name="troubleshoot-vm-snapshot-issues"></a>Felsök problem med VM-ögonblicksbild

VM-säkerhetskopiering är beroende av utfärdande av ögonblicks bild kommandon för underliggande lagring. Om du inte har åtkomst till lagring eller fördröjningar i en ögonblicks bild aktivitet kan säkerhets kopierings jobbet inte köras. Följande villkor kan orsaka ögonblicks bild aktivitets fel:

* **Nätverks åtkomst till lagring blockeras med hjälp av NSG**. Läs mer om hur du [etablerar nätverks åtkomst](backup-azure-arm-vms-prepare.md#establish-network-connectivity) till lagrings utrymme med hjälp av antingen listan över tillåtna IP-adresser eller via en proxyserver.
* **Virtuella datorer med SQL Server säkerhets kopia konfigurerad kan orsaka aktivitets fördröjning i ögonblicks bilder**. Som standard skapar VM-säkerhetskopiering en fullständig VSS-säkerhetskopiering på virtuella Windows-datorer. Virtuella datorer som kör SQL Server, med SQL Server säkerhets kopiering konfigurerad, kan uppleva fördröjningar i ögonblicks bilder. Om ögonblicks bildernas fördröjning orsakar problem med säkerhets kopieringen anger du följande register nyckel:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **VM-status rapporteras felaktigt eftersom den virtuella datorn stängs av i RDP**. Om du använde fjärr skrivbordet för att stänga av den virtuella datorn kontrollerar du att statusen för den virtuella datorn i portalen är korrekt. Om statusen inte är korrekt använder du alternativet **Stäng** av den virtuella portalens instrument panel för att stänga av den virtuella datorn.
* **Om fler än fyra virtuella datorer delar samma moln tjänst sprider du de virtuella datorerna över flera säkerhets kopierings principer**. Sprid säkerhets kopierings tiderna, så att inte fler än fyra VM-säkerhetskopieringar startar på samma gång. Försök att avgränsa start tiderna i principerna med minst en timme.
* **Den virtuella datorn körs med hög processor eller minne**. Om den virtuella datorn körs med hög minnes-eller CPU-användning, är mer än 90 procent av ögonblicks bilds aktiviteten i kö och fördröjd. Slutligen tids gränsen. Om det här problemet inträffar kan du prova en säkerhets kopiering på begäran.

## <a name="networking"></a>Nätverk

Precis som alla tillägg måste du ha åtkomst till det offentliga Internet för att kunna arbeta med säkerhets kopierings tillägg. Att inte ha åtkomst till det offentliga Internet kan själva manifesta på olika sätt:

* Det går inte att installera tillägget.
* Säkerhets kopierings åtgärder som disk ögonblicks bild kan inte fungera.
* Det går inte att visa status för säkerhets kopierings åtgärden.

Behovet av att lösa offentliga Internet adresser beskrivs i [den här bloggen för Azure-support](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Kontrol lera DNS-konfigurationerna för det virtuella nätverket och se till att Azure-URI: erna kan lösas.

När namn matchningen har utförts måste du också tillhandahålla åtkomst till Azure IP-adresser. Gör något av följande om du vill avblockera åtkomst till Azure-infrastrukturen:

* Lista över tillåtna IP-intervall för Azure-datacenter:
   1. Hämta listan över [Azure datacenter-IP-adresser](https://www.microsoft.com/download/details.aspx?id=41653) som ska finnas i listan över tillåtna.
   1. Avblockera IP-adresserna med hjälp av cmdleten [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) . Kör denna cmdlet i den virtuella Azure-datorn i ett upphöjd PowerShell-fönster. Kör som administratör.
   1. Lägg till regler i NSG, om du har ett på plats, för att tillåta åtkomst till IP-adresser.
* Skapa en sökväg för HTTP-trafik som ska flödas:
   1. Om du har en viss nätverks begränsning på plats kan du distribuera en HTTP-proxyserver för att dirigera trafiken. Ett exempel är en nätverks säkerhets grupp. Se stegen för att distribuera en HTTP-proxyserver i [upprätta nätverks anslutningar](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Lägg till regler i NSG, om du har ett på plats, för att tillåta åtkomst till Internet från HTTP-proxyn.

> [!NOTE]
> DHCP måste vara aktiverat i gästen för att IaaS VM-säkerhetskopiering ska fungera. Om du behöver en statisk privat IP-adress konfigurerar du den via Azure Portal eller PowerShell. Kontrol lera att DHCP-alternativet i den virtuella datorn är aktiverat.
> Få mer information om hur du konfigurerar en statisk IP-adress med hjälp av PowerShell:
>
> * [Så här lägger du till en statisk intern IP-adress till en befintlig virtuell dator](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> * [Ändra tilldelnings metoden för en privat IP-adress som tilldelats till ett nätverks gränssnitt](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
