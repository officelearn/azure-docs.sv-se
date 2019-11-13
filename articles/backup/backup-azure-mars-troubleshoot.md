---
title: Felsöka Azure Backup Agent
description: I den här artikeln får du lära dig hur du felsöker installationen och registreringen av den Azure Backup agenten.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: dacurwin
ms.openlocfilehash: abd4e91b8fd3332191b58acf38daed06d03801be
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012842"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Felsöka Microsoft Azure Recovery Services (MARS)-agenten

I den här artikeln beskrivs hur du löser fel som kan uppstå under konfiguration, registrering, säkerhets kopiering och återställning.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du kontrollerar följande innan du börjar felsöka Microsoft Azure Recovery Services-agenten (MARS):

- [Se till att mars-agenten är](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)aktuell.
- [Se till att du har nätverks anslutning mellan mars-agenten och Azure](https://aka.ms/AB-A4dp50).
- Se till att MARS körs (i tjänst konsolen). Om du behöver startar du om och försöker igen.
- [Se till att det finns 5% till 10% ledigt volym utrymme på platsen för den tillfälliga mappen](https://aka.ms/AB-AA4dwtt).
- [Kontrol lera om en annan process eller ett antivirus program stör Azure Backup](https://aka.ms/AB-AA4dwtk).
- Om schemalagd säkerhets kopiering Miss lyckas men manuell säkerhets kopiering fungerar, se [säkerhets kopieringar körs inte enligt schema](https://aka.ms/ScheduledBackupFailManualWorks).
- Se till att operativ systemet har de senaste uppdateringarna.
- [Se till att enheter och filer som inte stöds med attribut som inte stöds undantas från säkerhets kopieringen](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Se till att klockan på det skyddade systemet är konfigurerad till rätt tidszon.
- [Se till att .NET Framework 4.5.2 eller senare är installerat på servern](https://www.microsoft.com/download/details.aspx?id=30653).
- Om du försöker omregistrera servern till ett valv:
  - Se till att agenten har avinstallerats på servern och att den tas bort från portalen.
  - Använd samma lösen fras som ursprungligen användes för att registrera servern.
- För säkerhets kopiering offline kontrollerar du att Azure PowerShell 3.7.0 har installerats både på källan och på kopierings datorn innan du startar säkerhets kopieringen.
- Om säkerhets kopierings agenten körs på en virtuell Azure-dator kan du läsa [den här artikeln](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga autentiseringsuppgifter för valvet har angetts

**Fel meddelande**: ogiltiga valv uppgifter angavs. Filen är antingen skadad eller har inte de senaste autentiseringsuppgifterna som är associerade med återställnings tjänsten. (ID: 34513)

| Orsak | Rekommenderade åtgärder |
| ---     | ---    |
| **Autentiseringsuppgifterna för valvet är ogiltiga** <br/> <br/> Valvets autentiseringsuppgifter kan vara skadade eller kan ha upphört att gälla. (Till exempel kan de ha hämtats mer än 48 timmar före registrerings tiden.)| Hämta nya autentiseringsuppgifter från Recovery Services Vault på Azure Portal. (Se steg 6 i avsnittet [Ladda ned mars-agenten](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) .) Utför sedan dessa steg efter behov: <ul><li> Om du redan har installerat och registrerat MARS öppnar du MMC-konsolen för Microsoft Azure Backup Agent och väljer sedan **Registrera Server** i **Åtgärds** fönstret för att slutföra registreringen med de nya autentiseringsuppgifterna. <br/> <li> Om den nya installationen Miss lyckas kan du försöka med att installera om med de nya autentiseringsuppgifterna.</ul> **Obs!** om filer med flera valv har hämtats är det bara den senaste filen som är giltig för de kommande 48 timmarna. Vi rekommenderar att du hämtar en ny fil med autentiseringsuppgifter för valvet.
| **Proxyservern eller brand väggen blockerar registreringen** <br/>eller <br/>**Ingen Internet anslutning** <br/><br/> Om din dator eller proxyserver har begränsad Internet anslutning och du inte ser till att du har åtkomst till de nödvändiga URL: erna, Miss söker registreringen.| Gör så här:<br/> <ul><li> Arbeta med IT-teamet för att säkerställa att systemet är ansluten till Internet.<li> Om du inte har en proxyserver, se till att alternativet proxy inte är markerat när du registrerar agenten. [Kontrol lera proxyinställningarna](#verifying-proxy-settings-for-windows).<li> Om du har en brand vägg/proxyserver arbetar du med nätverks teamet för att se till att dessa URL: er och IP-adresser har åtkomst:<br/> <br> **Er**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP-adresser**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Försök att registrera igen när du har slutfört föregående fel söknings steg.
| **Antivirus programmet blockerar registreringen** | Om du har installerat antivirus program på servern lägger du till nödvändiga undantags regler i Antivirus genomsökningen för dessa filer och mappar: <br/><ul> <li> CBengine. exe <li> CSC.exe<li> Mappen scratch. Standard platsen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Bin-mappen i C:\Program\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Ytterligare rekommendationer

- Gå till C:/Windows/Temp och kontrol lera om det finns fler än 60 000 eller 65 000 filer med tillägget. tmp. Ta bort de här filerna om det finns.
- Se till att datorns datum och tid stämmer överens med den lokala tids zonen.
- Se till att [platserna](backup-configure-vault.md#verify-internet-access) läggs till i dina betrodda platser i Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verifiera proxyinställningar för Windows

1. Hämta PsExec från [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) -sidan.
1. Kör `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` från en upphöjd kommando tolk.

   Det här kommandot öppnar Internet Explorer.
1. Gå till **verktyg** > **Internet alternativ** > **anslutningar** > **LAN-inställningar**.
1. Kontrol lera proxyinställningarna för system-kontot.
1. Ta bort informationen om ingen proxy har kon figurer ATS och det finns information om proxyn.
1. Om en proxyserver har kon figurer ATS och informationen om proxyn är felaktig kontrollerar du att **proxy-IP-adressen** och **port** informationen är korrekt.
1. Stäng Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Det gick inte att hämta filen med valv behörighet

| Fel   | Rekommenderade åtgärder |
| ---     | ---    |
|Det gick inte att ladda ned filen med valv behörighet. (ID: 403) | <ul><li> Försök att ladda ned autentiseringsuppgifter för valvet med hjälp av en annan webbläsare eller utför följande steg: <ul><li> Starta Internet Explorer. Välj F12. </li><li> Gå till fliken **nätverk** och rensa cacheminnet och cookies. </li> <li> Uppdatera sidan.<br></li></ul> <li> Kontrol lera om prenumerationen är inaktive rad/upphör att gälla.<br></li> <li> Kontrol lera om någon brand Väggs regel blockerar nedladdningen. <br></li> <li> Se till att du inte har förbrukat gränsen för valvet (50 datorer per valv).<br></li>  <li> Se till att användaren har de Azure Backup behörigheter som krävs för att hämta autentiseringsuppgifter för valvet och registrera en server med valvet. Se [använda rollbaserad Access Control för att hantera Azure Backup återställnings punkter](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup

| Fel  | Möjlig orsak | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure återställnings tjänst agenten kunde inte ansluta till Microsoft Azure Backup. (ID: 100050) Kontrol lera nätverks inställningarna och se till att du kan ansluta till Internet.<li>(407) Proxyautentisering krävs. |En proxyserver blockerar anslutningen. |  <ul><li>I Internet Explorer går du till **verktyg** > **Internet alternativ** > **säkerhet** > **Internet**. Välj **Anpassad nivå** och rulla ned till **fil hämtnings** avsnittet. Välj **Aktivera**.<p>Du kan också behöva lägga till [URL: er och IP-adresser](backup-configure-vault.md#verify-internet-access) till dina betrodda platser i Internet Explorer.<li>Ändra inställningarna för att använda en proxyserver. Ange sedan information om proxyservern.<li> Om datorn har begränsad Internet åtkomst kontrollerar du att brand Väggs inställningarna på datorn eller proxyservern tillåter dessa [URL: er och IP-adresser](backup-configure-vault.md#verify-internet-access). <li>Om du har installerat antivirus program på servern kan du undanta dessa filer från AntiVirus genomsökningen: <ul><li>CBEngine. exe (i stället för DPMRA. exe).<li>CSC. exe (relaterad till .NET Framework). Det finns en CSC. exe för alla .NET Framework-versioner som är installerade på servern. Undanta CSC. exe-filer för alla versioner av .NET Framework på den berörda servern. <li>Den tillfälliga mappen eller cacheplatsen. <br>Standard platsen för mappen scratch eller sökvägen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Bin-mappen i C:\Program\Microsoft Azure Recovery Services Agent\Bin.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Det gick inte att ange krypterings nyckel för säkra säkerhets kopior

| Fel | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| <br />Det gick inte att ange krypterings nyckel för säkra säkerhets kopieringar. Aktiveringen slutfördes inte helt, men krypterings lösen frasen sparades i följande fil. |<li>Servern är redan registrerad på ett annat valv.<li>Lösen frasen har skadats under konfigurationen.| Avregistrera servern från valvet och registrera den igen med en ny lösen fras.

## <a name="the-activation-did-not-complete-successfully"></a>Aktiveringen slutfördes inte korrekt

| Fel  | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
|<br />Aktiveringen slutfördes inte korrekt. Den aktuella åtgärden kunde inte utföras på grund av ett internt tjänst fel [0x1FC07]. Försök igen efter en stund. Kontakta Microsoft-supporten om problemet kvarstår.     | <li> Scratch-mappen finns på en volym som inte har tillräckligt med utrymme. <li> Den tillfälliga mappen har flyttats felaktigt. <li> Filen OnlineBackup. KEK saknas.         | <li>Uppgradera till den [senaste versionen](https://aka.ms/azurebackup_agent) av mars-agenten.<li>Flytta mappen scratch eller cacheplatsen till en volym med ett ledigt utrymme som är mellan 5% och 10% av den totala storleken på säkerhets kopierings data. Se stegen i [vanliga frågor om säkerhets kopiering av filer och mappar](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)för att flytta cacheplatsen på rätt sätt.<li> Se till att filen OnlineBackup. KEK finns. <br>*Standard platsen för mappen scratch eller sökvägen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Krypterings lösen frasen är inte korrekt konfigurerad

| Fel  | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
| <br />Fel 34506. Krypterings lösen frasen som är lagrad på den här datorn är inte korrekt konfigurerad.    | <li> Scratch-mappen finns på en volym som inte har tillräckligt med utrymme. <li> Den tillfälliga mappen har flyttats felaktigt. <li> Filen OnlineBackup. KEK saknas.        | <li>Uppgradera till den [senaste versionen](https://aka.ms/azurebackup_agent) av mars-agenten.<li>Flytta mappen scratch eller cacheplatsen till en volym med ett ledigt utrymme som är mellan 5% och 10% av den totala storleken på säkerhets kopierings data. Se stegen i [vanliga frågor om säkerhets kopiering av filer och mappar](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)för att flytta cacheplatsen på rätt sätt.<li> Se till att filen OnlineBackup. KEK finns. <br>*Standard platsen för mappen scratch eller sökvägen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Säkerhets kopieringar körs inte enligt schema

Om schemalagda säkerhets kopieringar inte aktive ras automatiskt, men manuella säkerhets kopieringar fungerar korrekt, kan du försöka med följande åtgärder:

- Kontrol lera att Windows Server Backup-schemat inte hamnar i konflikt med schemat för säkerhets kopiering av Azure-filer och-mappar.

- Se till att statusen för onlinesäkerhetskopiering är **aktive**rad. Gör så här för att kontrol lera statusen:

  1. I Schemaläggaren expanderar du **Microsoft** och väljer **onlinesäkerhetskopiering**.
  1. Dubbelklicka på **Microsoft-OnlineBackup** och gå till fliken **utlösare** .
  1. Kontrol lera om status är **aktive rad**. Om den inte är det väljer du **Redigera**, väljer **aktive rad**och väljer sedan **OK**.

- Se till att det användar konto som har valts för att köra uppgiften är antingen **system** -eller **lokal administratörs grupp** på servern. Verifiera användar kontot genom att gå till fliken **Allmänt** och kontrol lera **säkerhets** alternativen.

- Se till att PowerShell 3,0 eller senare är installerat på servern. Kontrol lera PowerShell-versionen genom att köra det här kommandot och kontrol lera att `Major` versions numret är 3 eller senare:

  `$PSVersionTable.PSVersion`

- Se till att den här sökvägen är en del av variabeln `PSMODULEPATH`-miljö:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Om körnings principen för PowerShell för `LocalMachine` har angetts till `restricted`, kan PowerShell-cmdleten som utlöser säkerhets kopierings åtgärden Miss förväntas. Kör dessa kommandon i förhöjd läge för att kontrol lera och ställa in körnings principen på antingen `Unrestricted` eller `RemoteSigned`:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Se till att det inte finns några saknade eller skadade MSOnlineBackup-filer för PowerShell-modulen. Gör så här om det finns saknade eller skadade filer:

  1. Kopiera mappen MSOnlineBackup från en dator som har en MARS-agent som fungerar korrekt och kopiera mappen från C:\Program\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. På den problematiska datorn klistrar du in de kopierade filerna på samma plats (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Om det redan finns en MSOnlineBackup-mapp på datorn klistrar du in filerna i den eller ersätter befintliga filer.

> [!TIP]
> Om du vill säkerställa att ändringarna tillämpas konsekvent startar du om servern när du har genomfört föregående steg.

## <a name="troubleshoot-restore-problems"></a>Felsöka återställnings problem

Azure Backup kan inte montera återställnings volymen, även efter flera minuter. Och du kan få fel meddelanden under processen. Gör så här för att börja återskapa normalt:

1. Avbryt monterings processen om den körs i flera minuter.

2. Kontrol lera att du har den senaste versionen av säkerhets kopierings agenten. Om du vill kontrol lera versionen går du till fönstret **åtgärder** i mars-konsolen och väljer **om Microsoft Azure Recovery Services agent**. Kontrol lera att **versions** numret är lika med eller högre än den version som nämns i [den här artikeln](https://go.microsoft.com/fwlink/?linkid=229525). Välj den här länken för att [Ladda ned den senaste versionen](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Gå till **Enhetshanteraren** > **lagrings styrenheter** och leta upp **Microsoft iSCSI Initiator**. Om du hittar den går du direkt till steg 7.

4. Om du inte kan hitta Microsoft iSCSI Initiator-tjänsten försöker du hitta en post under **Enhetshanteraren** > **lagrings styrenheter** med namnet **Okänd enhet** med maskinvaru-ID **ROOT\ISCSIPRT**.

5. Högerklicka på **Okänd enhet** och välj **Uppdatera driv rutins program vara**.

6. Uppdatera driv rutinen genom att välja alternativet att **söka automatiskt efter uppdaterad driv rutins program vara**. Den här uppdateringen bör ändra den **okända enheten** till **Microsoft iSCSI-initieraren**:

    ![Skärm bild av Azure Backup Enhetshanteraren, med lagrings styrenheter markerade](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Gå till **aktivitets hanteraren** > **tjänster (lokala)**  > **Microsoft iSCSI Initiator service**:

    ![Skärm bild av Azure Backup aktivitets hanteraren med tjänster (lokala) markerade](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Starta om Microsoft iSCSI Initiator service. Det gör du genom att högerklicka på tjänsten och välja **stoppa**. Högerklicka sedan på den igen och välj **Starta**.

9. Försök återställa igen med hjälp av [omedelbar återställning](backup-instant-restore-capability.md).

Om återställningen fortfarande Miss lyckas startar du om servern eller klienten. Om du inte vill starta om eller om återställningen fortfarande Miss lyckas även när du har startat om servern kan du försöka [återställa från en annan dator](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Felsöka cache-problem

Säkerhets kopierings åtgärden kan Miss lyckas om cache-mappen (även kallat Scratch Folder) är felaktigt konfigurerad, saknas nödvändiga komponenter eller har begränsad åtkomst.

### <a name="prerequisites"></a>Krav

För att MARS agent-åtgärder ska lyckas måste cache-mappen uppfylla nedanstående krav:

- [Se till att det finns 5% till 10% ledigt volym utrymme på platsen för den tillfälliga mappen](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Se till att mappen för arbets platsen är giltig och tillgänglig](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Se till att filattributen i cache-mappen stöds](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Se till att det allokerade lagrings utrymmet för skugg kopior räcker för säkerhets kopierings processen](#increase-shadow-copy-storage)
- [Se till att det inte finns några andra processer (t. ex. antivirus program) begränsa åtkomsten till cache-mappen](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Öka lagring av skugg kopior

Säkerhets kopierings åtgärder kan inte utföras om det inte finns tillräckligt med lagrings utrymme för skugg kopior som krävs för att skydda data källan. Lös problemet genom att öka lagrings utrymmet för skugg kopian på den skyddade volymen med vssadmin enligt nedan:

- Kontrol lera det aktuella skugg lagrings utrymmet från den upphöjda kommando tolken:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Öka skugg lagrings utrymmet med kommandot nedan:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>En annan process eller ett antivirus program blockerar åtkomst till cache-mappen

Om du har installerat antivirus program på servern lägger du till nödvändiga undantags regler i Antivirus genomsökningen för dessa filer och mappar:  

- Mappen scratch. Standard platsen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- Bin-mappen i C:\Program\Microsoft Azure Recovery Services Agent\Bin
- CBengine. exe
- CSC.exe

## <a name="common-issues"></a>Vanliga problem

I det här avsnittet beskrivs vanliga fel som uppstår när du använder MARS-agenten.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Felmeddelande | Rekommenderad åtgärd |
-- | --
Microsoft Azure Recovery Services-agenten kunde inte komma åt kontrollsumman för säkerhetskopian på den tillfälliga platsen | Lös problemet genom att utföra följande och starta om servern <br/> - [kontrol lera om det finns ett antivirus program eller andra processer som låser den virtuella platsens filer](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [kontrol lera om arbets platsen är giltig och tillgänglig för mars-agenten.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Felmeddelande | Rekommenderad åtgärd |
-- | --
Microsoft Azure Recovery Services-agenten kunde inte komma åt den tillfälliga platsen för att initiera VHD | Lös problemet genom att utföra följande och starta om servern <br/> - [kontrol lera om det finns ett antivirus program eller andra processer som låser den virtuella platsens filer](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [kontrol lera om arbets platsen är giltig och tillgänglig för mars-agenten.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Felmeddelande | Rekommenderad åtgärd |
-- | --
Det gick inte att säkerhetskopiera eftersom det inte finns tillräckligt med lagrings utrymme i volymen där mappen Scratch finns | Lös problemet genom att kontrol lera stegen nedan och försök igen:<br/>- [Se till att mars-agenten är senaste](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Verifiera och lösa lagrings problem som påverkar säkerhets kopieringens arbets yta](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Felmeddelande | Rekommenderad åtgärd |
-- | --
Det går inte att hitta ändringar i en fil. Detta kan bero på olika orsaker. Försök att utföra åtgärden igen. | Lös problemet genom att kontrol lera stegen nedan och försök igen:<br/> - [Se till att mars-agenten är senaste](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Verifiera och lösa lagrings problem som påverkar säkerhets kopieringens arbets yta](#prerequisites)

## <a name="next-steps"></a>Nästa steg

- Få mer information om [hur du säkerhetskopierar Windows Server med Azure Backup-agenten](tutorial-backup-windows-server-to-azure.md).
- Om du behöver återställa en säkerhets kopia, se [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).
