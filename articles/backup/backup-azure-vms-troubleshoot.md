---
title: Felsöka säkerhetskopieringsfel med virtuella Azure-datorer
description: I den här artikeln kan du läsa om hur du felsöker fel som påträffas med säkerhetskopiering och återställning av virtuella Azure-datorer.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 15e4b4c8850798fd2386cd2874b6ab58a18d5406
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297398"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Felsöka säkerhetskopieringsfel på virtuella Azure-datorer

Du kan felsöka fel som påträffades när du använder Azure Backup med informationen nedan:

## <a name="backup"></a>Säkerhetskopiering

Det här avsnittet täcker säkerhetskopieringsåtgärdsfel för Azure Virtual Machine.

### <a name="basic-troubleshooting"></a>Grundläggande felsökning

* Kontrollera att VM-agenten (WA Agent) är den [senaste versionen](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* Kontrollera att Windows- eller Linux VM OS-versionen stöds, se [IaaS VM Backup Support Matrix](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Kontrollera att en annan säkerhetskopieringstjänst inte körs.
  * Om du vill vara säkra på att det inte finns några problem med tillägg [för ögonblicksbilder avinstallerar du tillägg för att tvinga fram omladdning och försöker sedan igen med säkerhetskopian](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout).
* Kontrollera att den virtuella datorn har internetanslutning.
  * Kontrollera att en annan säkerhetskopieringstjänst inte körs.
* Från `Services.msc`, se till att **Windows Azure Guest Agent-tjänsten** **körs**. Om **Tjänsten Windows Azure Guest Agent** saknas installerar du den från [Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* **Händelseloggen** kan visa säkerhetskopieringsfel som kommer från andra säkerhetskopierade produkter, till exempel Windows Server-säkerhetskopiering, och beror inte på Azure-säkerhetskopiering. Använd följande steg för att avgöra om problemet är med Azure Backup:
  * Om det finns ett fel med en post **Säkerhetskopiering** i händelsekällan eller meddelandet kontrollerar du om Azure IaaS VM Backup-säkerhetskopior lyckades och om en återställningspunkt skapades med önskad ögonblicksbildtyp.
  * Om Azure Backup fungerar är problemet troligen med en annan säkerhetskopieringslösning.
  * Här är ett exempel på ett loggningsfel 517 där Azure-säkerhetskopian fungerade bra men "Windows Server Backup" misslyckades:<br>
    ![Windows Server Backup misslyckas](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Om Azure Backup misslyckas letar du efter motsvarande felkod i avsnittet Vanliga vm-säkerhetskopieringsfel i den här artikeln.

## <a name="common-issues"></a>Vanliga problem

Följande är vanliga problem med säkerhetskopieringsfel på virtuella Azure-datorer.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - Kopiera säkerhetskopierade data från valv tidsbeslag

Felkod: KopieraVHDsFromBackUpVaultTakingLongTime <br/>
Felmeddelande: Kopiera säkerhetskopierade data från valv tidsåtgång

Detta kan inträffa på grund av tillfälliga lagringsfel eller otillräcklig lagringskonto IOPS för säkerhetskopieringstjänst för att överföra data till valvet inom timeout-perioden. Konfigurera säkerhetskopiering av virtuella datorer med hjälp av dessa [metodtips](backup-azure-vms-introduction.md#best-practices) och försök igen säkerhetskopieringen.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - VM är inte i ett tillstånd som tillåter säkerhetskopior

Felkod: UserErrorVmNotInDesirableState <br/>
Felmeddelande: DEN VIRTUELLA DATORN är inte i ett tillstånd som tillåter säkerhetskopior.<br/>

Säkerhetskopieringen misslyckades eftersom den virtuella datorn är i feltillstånd. För att säkerhetskopieringen ska lyckas måste tillståndet för den virtuella datorn vara Körs, Stoppad eller Stoppad (frigjord).

* Om den virtuella datorn är i ett tillfälligt tillstånd mellan **Löpning** och **Avstängning**väntar du på att tillståndet ska ändras. Utlösa sedan säkerhetskopieringsjobbet.
* Om den virtuella datorn är en Virtuell Linux-dator och använder kärnmodulen Security-Enhanced Linux, utesluter Azure Linux Agent-sökvägen **/var/lib/waagent** från säkerhetsprincipen och kontrollerar att tillägget säkerhetskopiering är installerat.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - Det gick inte att låsa en eller flera monteringspunkter på den virtuella datorn för att ta en konsekvent ögonblicksbild av filsystemet

Felkod: UserErrorFsFreezeFailed <br/>
Felmeddelande: Det gick inte att låsa en eller flera monteringspunkter för den virtuella datorn för att ta en konsekvent ögonblicksbild av filsystemet.

* Avmontera de enheter för vilka filsystemets tillstånd inte har rensats med kommandot **umount.**
* Kör en konsekvenskontroll av filsystemet på dessa enheter med kommandot **fsck.**
* Montera enheterna igen och försök igen.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - Förlängningsinstallation/-drift misslyckades på grund av ett COM+-fel

Felkod: ExtensionSnapshotFailedCOM <br/>
Felmeddelande: Åtgärden Ögonblicksbild misslyckades på grund av COM+-fel

Felkod: ExtensionInstallationFailedCOM  <br/>
Felmeddelande: Installationen/åtgärden för tillägg misslyckades på grund av ett COM+-fel

Felkod: ExtensionInstallationFailedMDTC <br/>
Felmeddelande: Förlängningsinstallationen misslyckades med felet "COM+ kunde inte prata med Microsoft Distributed Transaction Coordinator <br/>

Säkerhetskopieringen misslyckades på grund av ett problem med Windows-tjänst **COM+** System-programmet.  Följ dessa anvisningar för att lösa problemet:

* Prova att starta/starta om Windows-tjänsten **COM+ System Application** (från en förhöjd kommandotolk **- net start COMSysApp**).
* Se till att tjänsten **Distributed Transaction Coordinator** körs som network **service-konto.** Om inte, ändra den för att köras som **Network Service-konto** och starta om **COM+ System Application**.
* Om det inte går att starta om tjänsten installerar du om tjänsten **Distributed Transaction Coordinator** genom att följa stegen nedan:
  * Stoppa MSDTC-tjänsten
  * Öppna en kommandotolk (cmd)
  * Kör kommandot "msdtc -uninstall"
  * Kör kommandot "msdtc -install"
  * Starta MSDTC-tjänsten
* Starta Windows-tjänsten **COM+ System Application**. När **COM+ System application** startar utlöser du ett säkerhetskopieringsjobb från Azure-portalen.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - Snapshot-åtgärden misslyckades eftersom VSS-brännare var i dåligt skick

Felkod: ExtensionFailedVssWriterInBadState <br/>
Felmeddelande: Det gick inte att göra en ögonblicksbild eftersom VSS-skribenter var i dåligt läge.

Starta om VSS-skribenter som är i dåligt skick. Kör från en upphöjd ```vssadmin list writers```kommandotolk . Utdata innehåller alla VSS-författare och deras tillstånd. För varje VSS-skrivare med ett tillstånd som inte är **[1] Stabil**, för att starta om VSS-skrivare, kör du följande kommandon från en upphöjd kommandotolk:

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - Fel i tolkningen av konfigurationen för säkerhetskopieringstillägget

Felkod: ExtensionConfigParsingFailure<br/>
Felmeddelande: Det gick inte att tolka konfigurationen för tillägget för säkerhetskopiering.

Det här felet uppstår på grund av ändrade behörigheter för **MachineKeys-katalogen:** **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Kör följande kommando och kontrollera att behörigheter i **MachineKeys-katalogen** är standardtecken:**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Standardbehörigheter är följande:

* Alla: (R,W)
* BUILTIN\Administratörer: (F)

Om du ser behörigheter i **MachineKeys-katalogen** som skiljer sig från standardinställningarna följer du dessa steg för att korrigera behörigheter, ta bort certifikatet och utlösa säkerhetskopian:

1. Åtgärda behörigheter för **MachineKeys-katalogen.** Genom att använda Explorer-säkerhetsegenskaper och avancerade säkerhetsinställningar i katalogen återställer du behörigheterna till standardvärdena. Ta bort alla användarobjekt utom standardvärdena från katalogen och se till att behörigheten **Alla** har särskild åtkomst enligt följande:

   * Lista mapp/läsa data
   * Läsa attribut
   * Läsa utökade attribut
   * Skapa filer/skriva data
   * Skapa mappar/lägga till data
   * Skriv attribut
   * Skriv utökade attribut
   * Läs behörigheter
2. Ta bort alla certifikat där **utgiven till** är den klassiska distributionsmodellen eller **Windows Azure CRP-certifikatgenerator:**

   * [Öppna certifikat på en lokal datorkonsol](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * Under **Personliga** > **certifikat**tar du bort alla certifikat där **utgiven till** är den klassiska distributionsmodellen eller Windows Azure **CRP-certifikatgeneratorn**.
3. Utlösa ett vm-säkerhetskopieringsjobb.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - Förlängningstillstånd stöder inte säkerhetskopiering

Felkod: ExtensionStuckInDeletionState <br/>
Felmeddelande: Tilläggstillstånd stöder inte säkerhetskopiering

Säkerhetskopieringen misslyckades på grund av inkonsekvent tillstånd för säkerhetskopieringstillägg. Följ dessa anvisningar för att lösa problemet:

* Kontrollera att gästagenten är installerad och svarar.
* Gå till alla**inställningar för** > **Extensions** virtuella datorer från **Azure-portalen** > 
* Välj säkerhetskopieringstillägget VmSnapshot eller VmSnapshotLinux och klicka på **Avinstallera**.
* När du har tagit bort säkerhetskopieringstillägget försöker du igen säkerhetskopieringen
* Vid nästföljande säkerhetskopiering installeras det nya tillägget med önskat tillstånd.

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - Snapshot-åtgärden misslyckades eftersom ögonblicksbildgränsen överskrids för vissa av de anslutna diskarna

Felkod: ExtensionFailedSnapshotLimitReachedError <br/>
Felmeddelande: Det gick inte att göra en ögonblicksbild eftersom ögonblicksbildgränsen har överskridits för vissa av de anslutna diskarna

Ögonblicksbildåtgärden misslyckades eftersom ögonblicksbildgränsen har överskridits för några av de anslutna diskarna. Slutför felsökningsstegen nedan och försök sedan igen.

* Ta bort de diskblobbögonblicksbilder som inte krävs. Var försiktig så att du inte tar bort diskblobar, bör endast ögonblicksbildblobar tas bort.
* Om Soft-delete är aktiverat på VM-disklagringskonton konfigurerar du mjukborttagningskvarhållning så att befintliga ögonblicksbilder är mindre än det högsta tillåtna vid någon tidpunkt.
* Om Azure Site Recovery är aktiverat i den säkerhetskopierade virtuella datorn gör du stegen nedan:

  * Se till att värdet för **isanysnapshotfailed** anges som falskt i /etc/azure/vmbackup.conf
  * Schemalägg Azure Site Recovery vid en annan tidpunkt, så att den inte står i konflikt med säkerhetskopieringen.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - Snapshot-åtgärden misslyckades på grund av otillräckliga vm-resurser

Felkod: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Felmeddelande: Åtgärden Ögonblicksbild misslyckades på grund av otillräckliga vm-resurser.

Säkerhetskopieringsåtgärden på den virtuella datorn misslyckades på grund av fördröjning i nätverksanrop när ögonblicksbilden utfördes. Du löser problemet genom att utföra steg 1. Om problemet kvarstår kan du prova steg 2 och 3.

**Steg 1:** Skapa ögonblicksbild via värd

Från en upphöjd kommandotolk (administratör) kör du kommandot nedan:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Detta säkerställer att ögonblicksbilderna tas via värden i stället för gästen. Försök att utföra säkerhetskopieringsåtgärden igen.

**Steg 2:** Prova att ändra säkerhetskopieringsschemat till en tid då den virtuella datorn är under mindre belastning (mindre CPU / IOps etc.)

**Steg 3:** Försök [att öka storleken på den virtuella datorn](https://azure.microsoft.com/blog/resize-virtual-machines/) och försök igen

## <a name="common-vm-backup-errors"></a>Vanliga fel vid säkerhetskopiering av virtuella datorer

| Felinformation | Lösning |
| ------ | --- |
| **Felkod**: 320001, ResourceNotFound <br/> **Felmeddelande:** Det gick inte att utföra åtgärden eftersom den virtuella datorn inte längre finns. <br/> <br/> **Felkod:** 400094, BCMV2VMNotFound <br/> **Felmeddelande:** Den virtuella datorn finns inte <br/> <br/>  Det gick inte att hitta en virtuell Azure-dator.  |Det här felet inträffar när den primära virtuella datorn tas bort, men säkerhetskopieringsprincipen söker fortfarande efter en virtuell dator som ska säkerhetskopieras. Åtgärda det här felet genom att vidta följande åtgärder: <ol><li> Återskapa den virtuella datorn med samma namn och samma resursgruppsnamn, **molntjänstnamn**,<br>**Eller**</li><li> Sluta skydda den virtuella datorn med eller utan att ta bort säkerhetskopierade data. Mer information finns i [Sluta skydda virtuella datorer](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
|**Felkod:** UserErrorBCMPremiumStorageQuotaError<br/> **Felmeddelande:** Det gick inte att kopiera ögonblicksbilden av den virtuella datorn på grund av otillräckligt ledigt utrymme i lagringskontot | För premium virtuella datorer på VM backup stack V1 kopierar vi ögonblicksbilden till lagringskontot. Det här steget säkerställer att säkerhetskopieringshanteringstrafik, som fungerar på ögonblicksbilden, inte begränsar antalet IOPS som är tillgängliga för programmet med premiumdiskar. <br><br>Vi rekommenderar att du bara allokerar 50 procent, 17,5 TB, av det totala lagringsutrymmet för lagringsutrymme. Sedan kan Azure Backup-tjänsten kopiera ögonblicksbilden till lagringskontot och överföra data från den här kopierade platsen i lagringskontot till valvet. |
| **Felkod**: 380008, AzureVmOffline <br/> **Felmeddelande:** Det gick inte att installera Microsoft Recovery Services-tillägget eftersom den virtuella datorn inte körs | VM-agenten är en förutsättning för azure recovery services-tillägget. Installera Azure Virtual Machine Agent och starta om registreringsåtgärden. <br> <ol> <li>Kontrollera om VM-agenten är korrekt installerad. <li>Kontrollera att flaggan på vm-konfigurationen är korrekt inställd.</ol> Läs mer om hur du installerar VM-agenten och hur du validerar VM-agentinstallationen. |
| **Felkod:** ExtensionSnapshotBitlockerError <br/> **Felmeddelande:** Ögonblicksbilden misslyckades med åtgärdsfelet för VSS (Volume Shadow Copy Service) **Den här enheten är låst av BitLocker-diskkryptering. Du måste låsa upp enheten från Kontrollpanelen.** |Inaktivera BitLocker för alla enheter på den virtuella datorn och kontrollera om VSS-problemet är löst. |
| **Felkod**: VmNotInDesirableState <br/> **Felmeddelande:** Den virtuella datorn är inte i ett tillstånd som tillåter säkerhetskopior. |<ul><li>Om den virtuella datorn är i ett tillfälligt tillstånd mellan **Löpning** och **Avstängning**väntar du på att tillståndet ska ändras. Utlösa sedan säkerhetskopieringsjobbet. <li> Om den virtuella datorn är en Virtuell Linux-dator och använder kärnmodulen Security-Enhanced Linux, utesluter Azure Linux Agent-sökvägen **/var/lib/waagent** från säkerhetsprincipen och kontrollerar att tillägget säkerhetskopiering är installerat.  |
| VM-agenten finns inte på den virtuella datorn: <br>Installera alla nödvändiga förutsättningar och VM-agenten. Starta sedan om åtgärden. |Läs mer om [VM-agentinstallation och hur du validerar VM-agentinstallation](#vm-agent). |
| **Felkod:** ExtensionSnapshotFailedNoSecureNetwork <br/> **Felmeddelande:** Ögonblicksbilden misslyckades på grund av att det inte gick att skapa en säker nätverkskommunikationskanal. | <ol><li> Öppna Registereditorn genom att köra **regedit.exe** i ett upphöjt läge. <li> Identifiera alla versioner av .NET Framework som finns i systemet. De finns under hierarkin av registernyckel **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> För varje .NET Framework som finns i registernyckeln lägger du till följande nyckel: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| **Felkod**: ExtensionVCRedistInstallationFailure <br/> **Felmeddelande:** Ögonblicksbilden misslyckades på grund av att Visual C++ Redistributable för Visual Studio 2012 inte kunde installeras. | Navigera till C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion och installera vcredist2013_x64.<br/>Kontrollera att registernyckelvärdet som tillåter tjänstinstallationen är inställt på rätt värde. Det vill än ange **startvärdet** i **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** till **3** och inte **4**. <br><br>Om du fortfarande har problem med installationen startar du om installationstjänsten genom att köra **MSIEXEC /UNREGISTER** följt av **MSIEXEC /REGISTER** från en upphöjd kommandotolk.  |
| **Felkod**: UserErrorRequestDisallowedByPolicy <BR> **Felmeddelande:** En ogiltig princip har konfigurerats på den virtuella datorn som förhindrar att ögonblicksbilder utförs. | Om du har en Azure-princip som [styr taggar i din miljö](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags)kan du antingen överväga att ändra principen från en [neka-effekt](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deny) till en [ändra-effekt](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)eller skapa resursgruppen manuellt enligt [namngivningsschemat som krävs av Azure Backup](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines).
## <a name="jobs"></a>Jobb

| Felinformation | Lösning |
| --- | --- |
| Annullering stöds inte för den här jobbtypen: <br>Vänta tills jobbet är klart. |Inget |
| Jobbet är inte i ett avbokningsbart tillstånd: <br>Vänta tills jobbet är klart. <br>**Eller**<br> Det valda jobbet är inte i ett avbokningsbart tillstånd: <br>Vänta tills jobbet är klart. |Det är troligt att jobbet är nästan klart. Vänta tills jobbet är klart.|
| Säkerhetskopiering kan inte avbryta jobbet eftersom det inte pågår: <br>Annullering stöds endast för pågående jobb. Försök att avbryta ett pågående jobb. |Det här felet inträffar på grund av ett övergående tillstånd. Vänta en minut och försök igen. |
| Det gick inte att avbryta jobbet för säkerhetskopiering: <br>Vänta tills jobbet är klart. |Inget |

## <a name="restore"></a>Återställ

| Felinformation | Lösning |
| --- | --- |
| Återställningen misslyckades med ett internt fel i molnet. |<ol><li>Molntjänsten som du försöker återställa är konfigurerad med DNS-inställningar. Du kan kontrollera: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Produktion" Get-AzureDns -DnsSettings $deployment. DnsSettings**.<br>Om **Adressen** är konfigurerad konfigureras DNS-inställningarna.<br> <li>Molntjänsten som du försöker återställa till är konfigurerad med **ReservedIP**och befintliga virtuella datorer i molntjänsten är i stoppat tillstånd. Du kan kontrollera att en molntjänst har reserverat en IP med hjälp av följande PowerShell-cmdlets: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep. ReservedIPName**. <br><li>Du försöker återställa en virtuell dator med följande speciella nätverkskonfigurationer till samma molntjänst: <ul><li>Virtuella datorer under belastningsutjämnad konfiguration, interna och externa.<li>Virtuella datorer med flera reserverade IP-adresser. <li>Virtuella datorer med flera nätverkskort. </ul><li>Välj en ny molntjänst i användargränssnittet eller se [återställa överväganden](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) för virtuella datorer med särskilda nätverkskonfigurationer.</ol> |
| Det valda DNS-namnet har redan tagits: <br>Ange ett annat DNS-namn och försök igen. |Det här DNS-namnet refererar till molntjänstnamnet och slutar vanligtvis med **.cloudapp.net**. Det här namnet måste vara unikt. Om du får det här felet måste du välja ett annat VM-namn under återställningen. <br><br> Det här felet visas endast för användare av Azure-portalen. Återställningen via PowerShell lyckas eftersom den bara återställer diskarna och inte skapar den virtuella datorn. Felet uppstår när den virtuella datorn uttryckligen skapas av dig efter diskåterställningsåtgärden. |
| Den angivna virtuella nätverkskonfigurationen är inte korrekt: <br>Ange en annan konfiguration för virtuella nätverk och försök igen. |Inget |
| Den angivna molntjänsten använder en reserverad IP som inte matchar konfigurationen av den virtuella datorn som återställs: <br>Ange en annan molntjänst som inte använder en reserverad IP. Eller välj en annan återställningspunkt att återställa från. |Inget |
| Molntjänsten har nått sin gräns för antalet indataslutpunkter: <br>Försök igen genom att ange en annan molntjänst eller med hjälp av en befintlig slutpunkt. |Inget |
| Valvet för återställningstjänster och mållagring finns i två olika regioner: <br>Kontrollera att lagringskontot som anges i återställningsåtgärden finns i samma Azure-region som ditt Recovery Services-valv. |Inget |
| Det lagringskonto som angetts för återställningen stöds inte: <br>Endast grundläggande eller standardlagringskonton med lokalt redundanta eller geoundundanta replikeringsinställningar stöds. Välj ett lagringskonto som stöds. |Inget |
| Den typ av lagringskonto som angetts för återställningen är inte online: <br>Kontrollera att lagringskontot som anges i återställningsåtgärden är online. |Det här felet kan inträffa på grund av ett tillfälligt fel i Azure Storage eller på grund av ett avbrott. Välj ett annat lagringskonto. |
| Resursgruppskvoten har uppnåtts: <br>Ta bort vissa resursgrupper från Azure-portalen eller kontakta Azure Support för att öka gränserna. |Inget |
| Det valda undernätet finns inte: <br>Välj ett undernät som finns. |Inget |
| Säkerhetskopieringstjänsten har inte behörighet att komma åt resurser i prenumerationen. |LÃ¶s problemet genom att först återställa diskar med hjälp av stegen i [Restore säkerhetskopiera diskar](backup-azure-arm-restore-vms.md#restore-disks). Använd sedan PowerShell-stegen i [Skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Säkerhetskopiering eller återställning tar tid

Om säkerhetskopieringen tar mer än 12 timmar eller om återställningen tar mer än 6 timmar granskar du [metodtips](backup-azure-vms-introduction.md#best-practices)och [prestandaöverväganden](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM-agent

### <a name="set-up-the-vm-agent"></a>Konfigurera VM-agenten

Vanligtvis finns vm-agenten redan i virtuella datorer som har skapats från Azure-galleriet. Men virtuella datorer som migreras från lokala datacenter har inte VM-agenten installerad. För dessa virtuella datorer måste VM-agenten installeras explicit.

#### <a name="windows-vms"></a>Virtuella Windows-datorer

* Ladda ned och installera [agentens MSI-fil](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du behöver administratörsbehörighet för att slutföra installationen.
* För virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen [uppdaterar du egenskapen VIRTUELL](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att ange att agenten är installerad. Det här steget krävs inte för virtuella Azure Resource Manager-datorer.

#### <a name="linux-vms"></a>Virtuella Linux-datorer

* Installera den senaste versionen av agenten från distributionsdatabasen. Mer information om paketnamnet finns i [Linux Agent-databasen](https://github.com/Azure/WALinuxAgent).
* För virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen använder du den [här bloggen](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) för att uppdatera egenskapen VM och kontrollera att agenten är installerad. Det här steget krävs inte för virtuella resurser för Resource Manager-datorer.

### <a name="update-the-vm-agent"></a>Uppdatera VM-agenten

#### <a name="windows-vms"></a>Virtuella Windows-datorer

* Om du vill uppdatera VM-agenten installerar du om [vm-agentens binärfiler](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Innan du uppdaterar agenten kontrollerar du att inga säkerhetskopieringsåtgärder inträffar under uppdateringen av VM-agenten.

#### <a name="linux-vms"></a>Virtuella Linux-datorer

* Om du vill uppdatera Linux VM-agenten följer du instruktionerna i artikeln [Uppdatera Linux VM-agenten](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Använd alltid distributionsdatabasen för att uppdatera agenten.

    Hämta inte agentkoden från GitHub. Om den senaste agenten inte är tillgänglig för distributionen kontaktar du distributionssupporten för instruktioner om hur du hämtar den senaste agenten. Du kan också kontrollera den senaste Informationen om [Windows Azure Linux-agenten](https://github.com/Azure/WALinuxAgent/releases) i GitHub-databasen.

### <a name="validate-vm-agent-installation"></a>Verifiera vm-agentinstallation

Verifiera VM-agentversionen på virtuella datorer i Windows:

1. Logga in på den virtuella Azure-datorn och navigera till mappen **C:\WindowsAzure\Packages**. Du bör hitta **filen WaAppAgent.exe.**
2. Högerklicka på filen och gå till **Egenskaper**. Välj sedan fliken **Detaljer.** Fältet **Produktversion** ska vara 2.6.1198.718 eller högre.

## <a name="troubleshoot-vm-snapshot-issues"></a>Felsöka problem med ögonblicksbilder av virtuella datorer

Säkerhetskopiering av virtuella datorer är beroende av att utfärda ögonblicksbildkommandon till underliggande lagring. Om du inte har åtkomst till lagring eller fördröjningar i en aktivitetskörning för ögonblicksbilder kan säkerhetskopieringsjobbet misslyckas. Följande villkor kan orsaka fel på ögonblicksbilden:

* **Virtuella datorer med konfigurerad SQL Server-säkerhetskopia kan orsaka fördröjning av ögonblicksbilder**. Som standard skapar vm-säkerhetskopiering en fullständig VSS-säkerhetskopia på virtuella datorer i Windows. Virtuella datorer som kör SQL Server, med SQL Server-säkerhetskopiering konfigurerad, kan uppstå ögonblicksbildfördröjningar. Om ögonblicksbildfördröjningar orsakar säkerhetskopieringsfel anger du följande registernyckel:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **VM-status rapporteras felaktigt eftersom den virtuella datorn stängs av i RDP**. Om du använde fjärrskrivbordet för att stänga av den virtuella datorn kontrollerar du att vm-statusen i portalen är korrekt. Om statusen inte är korrekt använder du alternativet **Avstängning** på instrumentpanelen för virtuell portal för att stänga av den virtuella datorn.
* **Om fler än fyra virtuella datorer delar samma molntjänst sprider du de virtuella datorerna över flera principer för säkerhetskopiering**. Fördela säkerhetskopieringstiderna, så inte fler än fyra VM-säkerhetskopior startar samtidigt. Försök att skilja starttiderna i principerna med minst en timme.
* **Den virtuella datorn körs med hög CPU eller minne**. Om den virtuella datorn körs med hög minnes- eller CPU-användning, mer än 90 procent, köas och fördröjs ögonblicksbildaktiviteten. Så småningom det time out. Om det här problemet inträffar provar du en säkerhetskopiering på begäran.

## <a name="networking"></a>Nätverk

DHCP måste vara aktiverat inuti gästen för att IaaS VM-säkerhetskopiering ska fungera. Om du behöver en statisk privat IP konfigurerar du den via Azure-portalen eller PowerShell. Kontrollera att DHCP-alternativet i den virtuella datorn är aktiverat.
Få mer information om hur du konfigurerar en statisk IP via PowerShell:

* [Så här lägger du till en statisk intern IP-adress i en befintlig virtuell dator](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
* [Ändra allokeringsmetoden för en privat IP-adress som tilldelats ett nätverksgränssnitt](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)

