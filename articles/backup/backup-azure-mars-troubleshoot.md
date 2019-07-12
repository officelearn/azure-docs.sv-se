---
title: Felsöka Azure Backup-agenten
description: Felsöka installation och registrering av Azure Backup-agenten
services: backup
author: saurabhsensharma
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 437b175efad081b8382d80be8427aa074920fd3e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705045"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Felsöka Microsoft Azure Recovery Services MARS-agenten

Den här artikeln beskriver hur du löser fel som kan uppstå under konfiguration, registrering, säkerhetskopiering och återställning.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du kontrollerar följande innan du börja felsöka Microsoft Azure Recovery Services MARS-agenten:

- [Se till att MARS-agenten är uppdaterad](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Kontrollera att du har nätverksanslutning mellan MARS-agenten och Azure](https://aka.ms/AB-A4dp50).
- Kontrollera MARS körs (i tjänstkonsolen). Om du vill starta om och försök igen.
- [Se till att 5 till 10% lediga volymen utrymme är tillgängligt i den tillfälliga mappen](https://aka.ms/AB-AA4dwtt).
- [Kontrollera om en annan process eller ett antivirusprogram hindrar Azure Backup](https://aka.ms/AB-AA4dwtk).
- Om schemalagd säkerhetskopiering misslyckas men manuell säkerhetskopiering fungerar, se [säkerhetskopieringar inte kör enligt schema](https://aka.ms/ScheduledBackupFailManualWorks).
- Kontrollera ditt operativsystem har de senaste uppdateringarna.
- [Se till att enheter som inte stöds och filer med attribut som inte stöds är undantagna från säkerhetskopia](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Kontrollera att klockan på skyddade systemet är konfigurerad för att korrekt tidszon.
- [Se till att .NET Framework 4.5.2 eller senare är installerat på servern](https://www.microsoft.com/download/details.aspx?id=30653).
- Om du försöker registrera om servern till ett valv:
  - Se till att agenten har avinstallerats från servern utan att tas bort från portalen.
  - Använda samma lösenfras som ursprungligen användes för att registrera servern.
- För offlinesäkerhetskopior, kontrollera du Azure PowerShell 3.7.0 är installerad på både käll- och kopiera datorn innan du påbörjar säkerhetskopieringen.
- Om Backup-agenten körs på virtuella Azure-datorer, se [i den här artikeln](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga valvautentiseringsuppgifter har angetts

**Felmeddelande**: Ogiltiga valvautentiseringsuppgifter har angetts. Filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten. (ID: 34513)

| Orsak | Rekommenderade åtgärder |
| ---     | ---    |
| **Valvautentiseringsuppgifter är inte giltig** <br/> <br/> Valvautentisering filer kan vara skadad eller har gått. (Till exempel de kanske har laddats ned mer än 48 timmar innan tiden för registrering.)| Hämta nya autentiseringsuppgifter från Recovery Services-valv på Azure portal. (Se steg 6 i den [hämta MARS-agenten](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) avsnittet.) Gör sedan följande, efter behov: <ul><li> Om du har redan installerats och registrerats MARS, öppna Microsoft Azure Backup Agent MMC-konsolen och välj sedan **registrera Server** i den **åtgärder** fönstret för att slutföra registreringen med den nya autentiseringsuppgifter. <br/> <li> Om den nya installationen misslyckas försök att installera med de nya autentiseringsuppgifterna.</ul> **Obs!** Om flera valv credential filer har laddats ned, är endast den senaste filen giltig i nästa 48 timmar. Vi rekommenderar att du hämtar en ny valvautentiseringsfil.
| **En proxyserver eller brandvägg blockerar registrering** <br/>eller <br/>**Utan internet-anslutning** <br/><br/> Om din dator eller proxy server har begränsad Internetanslutning och du inte att garantera åtkomst för de nödvändiga URL: er, misslyckas registreringen.| Göra följande:<br/> <ul><li> Arbeta med din IT-teamet att se till att systemet är ansluten till internet.<li> Om du inte har en proxyserver, kontrollera proxyalternativet inte är markerat när du registrerar agenten. [Kontrollera dina proxyinställningar](#verifying-proxy-settings-for-windows).<li> Om du har en brandvägg/proxyserver kan arbeta med ditt nätverksteam och se till att dessa URL: er och IP-adresser som har åtkomst:<br/> <br> **URL: er**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP-adresser**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Prova att registrera igen när du har slutfört föregående felsökningsstegen.
| **Antivirusprogramvaran blockerar registrering** | Om du har installerat på servern, Lägg till nödvändiga undantagsregler virusgenomsökning för dessa filer och mappar: <br/><ui> <li> CBengine.exe <li> CSC.exe<li> Den temporära mappen. Standardplatsen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Bin-mappen i C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Ytterligare rekommendationer
- Gå till C:/Windows/Temp och kontrollera om det finns fler än 60 000 eller 65 000 filer med tillägget .tmp. Om det finns tar du bort dessa filer.
- Se till att datorns datum och tid matchar den lokala tidszonen.
- Se till att [dessa webbplatser](backup-configure-vault.md#verify-internet-access) läggs till i dina betrodda webbplatser i Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Kontrollera proxy-inställningar för Windows

1. Hämta PsExec från den [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) sidan.
1. Kör `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` från en upphöjd kommandotolk.

   Det här kommandot ska öppna Internet Explorer.
1. Gå till **verktyg** > **Internetalternativ** > **anslutningar** > **LAN-inställningar**.
1. Kontrollera proxyinställningarna för system-kontot.
1. Ta bort information om ingen proxy har konfigurerats och proxyinformationen tillhandahålls.
1. Om en proxy har konfigurerats och proxyinformationen är felaktiga, kontrollerar den **Proxy IP** och **Port** informationen är korrekt.
1. Stäng Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Det går inte att hämta valvautentiseringsfilen

| Fel   | Rekommenderade åtgärder |
| ---     | ---    |
|Det gick inte att hämta valvautentiseringsfilen. (ID: 403) | <ul><li> Försök att hämta autentiseringsuppgifterna för valvet genom att använda en annan webbläsare eller göra följande: <ul><li> Starta Internet Explorer. Välj F12. </li><li> Gå till den **nätverk** fliken och rensa cacheminnet och cookies. </li> <li> Uppdatera sidan.<br></li></ul> <li> Kontrollera att prenumerationen har inaktiverats/har upphört att gälla.<br></li> <li> Kontrollera om någon brandväggsregel blockerar nedladdningen. <br></li> <li> Se till att du inte har förbrukat gränsen för valvet (50 datorer per valv).<br></li>  <li> Se till att användaren har Azure Backup-behörigheter som krävs för att ladda ned valvautentiseringsuppgifter och registrera en server med valvet. Se [Use Role-Based Access Control för att hantera Azure Backup återställningspunkter](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup

| Fel  | Möjlig orsak | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup. (ID: 100050) kontrollera nätverksinställningarna och se till att du kan ansluta till internet.<li>(407) proxyautentisering krävs. |En proxy blockerar anslutningen. |  <ul><li>I Internet Explorer går du till **verktyg** > **Internetalternativ** > **Security** > **Internet**. Välj **Anpassad nivå** och bläddra ned till den **Filhämtning** avsnittet. Välj **aktivera**.<p>Du kan också behöva lägga till [URL: er och IP-adresser](backup-configure-vault.md#verify-internet-access) till dina betrodda webbplatser i Internet Explorer.<li>Ändra inställningarna för att använda en proxyserver. Ange sedan proxyn serverinformation.<li> Om din dator har begränsad tillgång till internet måste du se till att brandväggsinställningarna på den datorn eller proxyservern tillåter dessa [URL: er och IP-adresser](backup-configure-vault.md#verify-internet-access). <li>Om du har installerat på servern, kan du undanta de här filerna från virusgenomsökning: <ul><li>CBEngine.exe (i stället för dpmra.exe).<li>CSC.exe (relaterade till .NET Framework). Det finns en CSC.exe för alla .NET Framework-version som är installerade på servern. Undanta filer som CSC.exe för alla versioner av .NET Framework på den berörda servern. <li>Den tillfälliga mapp eller cache platsen. <br>Standardplatsen för den temporära mappen eller cachesökvägen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Bin-mappen i C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Det gick inte att ange krypteringsnyckeln för säker säkerhetskopiering

| Fel | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| <br />Det gick inte att ange krypteringsnyckeln för säker säkerhetskopiering. Aktiveringen lyckades inte helt, men den krypterade lösenfrasen som har sparats till följande fil. |<li>Servern är redan registrerad med ett annat valv.<li>Under konfigurationen var lösenfrasen skadad.| Avregistrera servern från valvet och registrera den igen med en ny lösenfras.

## <a name="the-activation-did-not-complete-successfully"></a>Aktiveringen slutfördes inte

| Fel  | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
|<br />Aktiveringen slutfördes inte. Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FC07]. Försök igen senare. Kontakta Microsoft-supporten om problemet kvarstår.     | <li> Den temporära mappen finns på en volym som inte har tillräckligt med utrymme. <li> Den temporära mappen har flyttats felaktigt. <li> Filen OnlineBackup.KEK saknas.         | <li>Uppgradera till den [senaste versionen](https://aka.ms/azurebackup_agent) av MARS-agenten.<li>Flytta den tillfälliga mapp eller cache-platsen till en volym med ledigt utrymme som är mellan 5 och 10% av den totala mängden säkerhetskopierade data. Om du vill flytta cacheplatsen, läser du anvisningarna i [vanliga frågor om hur du säkerhetskopierar filer och mappar](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Kontrollera att filen OnlineBackup.KEK finns. <br>*Standardplatsen för den temporära mappen eller cachesökvägen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Krypteringslösenfrasen inte korrekt konfigurerad

| Fel  | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
| <br />Fel vid 34506. Krypteringslösenfrasen lagras på den här datorn har inte konfigurerats korrekt.    | <li> Den temporära mappen finns på en volym som inte har tillräckligt med utrymme. <li> Den temporära mappen har flyttats felaktigt. <li> Filen OnlineBackup.KEK saknas.        | <li>Uppgradera till den [senaste versionen](https://aka.ms/azurebackup_agent) av MARS-agenten.<li>Flytta den tillfälliga mapp eller cache-platsen till en volym med ledigt utrymme som är mellan 5 och 10% av den totala mängden säkerhetskopierade data. Om du vill flytta cacheplatsen, läser du anvisningarna i [vanliga frågor om hur du säkerhetskopierar filer och mappar](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Kontrollera att filen OnlineBackup.KEK finns. <br>*Standardplatsen för den temporära mappen eller cachesökvägen är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-schedule"></a>Säkerhetskopieringar kan inte köras enligt schema
Om schemalagda säkerhetskopieringar inte hämta utlöses automatiskt men manuella säkerhetskopieringar att fungera korrekt, kan du prova följande åtgärder:

- Se till att Windows Server-schemat för säkerhetskopiering inte står i konflikt med Azure filer och mappar schemat för säkerhetskopiering.

- Kontrollera status för säkerhetskopiering online är inställd på **aktivera**. Du kan kontrollera statusen genom att göra följande:

  1. I Schemaläggaren Expandera **Microsoft** och välj **onlinesäkerhetskopieringen**.
  1. Dubbelklicka på **Microsoft OnlineBackup** och gå till den **utlösare** fliken.
  1. Kontrollera om status är inställd **aktiverad**. Om det inte finns väljer **redigera**väljer **aktiverad**, och välj sedan **OK**.

- Se till att det användarkonto som valts för att köra uppgiften är antingen **SYSTEM** eller **gruppen lokala administratörer** på servern. Kontrollera användarkontot, gå till den **Allmänt** fliken och markera den **Security** alternativ.

- Se till att PowerShell 3.0 eller senare är installerat på servern. Om du vill kontrollera PowerShell-version du kör det här kommandot och kontrollera att den `Major` versionsnumret är 3 eller senare:

  `$PSVersionTable.PSVersion`

- Se till att den här sökvägen är en del av den `PSMODULEPATH` miljövariabeln:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Om PowerShell-körningsprincipen för `LocalMachine` är har angetts till begränsad PowerShell-cmdlet som utlöser åtgärden misslyckas. Kör följande kommandon i upphöjt läge för att kontrollera och ställa in körningsprincipen till antingen `Unrestricted` eller `RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Se till att det finns inga saknas eller är skadad PowerShell-modulen MSOnlineBackup filer. Om det finns filer saknas eller är skadad kan du göra följande:

  1. Kopiera mappen MSOnlineBackup från C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules från valfri dator som har en MARS-agenten fungerar korrekt.
  1. Klistra in de kopierade filerna på samma mapp plats (C:\Program Files\Microsoft Azure Recovery Services-Agent\bin\Modules) på den problematiska datorn.

     Om det finns redan en MSOnlineBackup mapp på datorn, klistra in filerna till den eller ersätta befintliga filer.


> [!TIP]
> För att säkerställa att ändringarna tillämpas konsekvent, starta om servern när du har utfört de föregående stegen.


## <a name="troubleshoot-restore-problems"></a>Felsöka problem med återställning

Azure Backup kan inte har montera återställningsvolymen, även om några minuter. Och du kan få felmeddelanden under processen. Du börjar återställa normalt genom att göra följande:

1.  Avbryta processen montera om den har körts i flera minuter.

2.  Kontrollera om du har den senaste versionen av Backup-agenten. Kontrollera versionen, på den **åtgärder** fönstret MARS-konsolen väljer **om Microsoft Azure Recovery Services Agent**. Bekräfta att den **Version** antalet är lika med eller högre än den version som nämns i [i den här artikeln](https://go.microsoft.com/fwlink/?linkid=229525). Välj den här länken till [hämta den senaste versionen](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Gå till **Enhetshanteraren** > **lagringsstyrenheter** och leta upp **Microsoft iSCSI Initiator**. Om du har hittat det kan gå direkt till steg 7.

4.  Om du inte kan hitta Microsoft iSCSI Initiator service, försöker att hitta någon post under **Enhetshanteraren** > **lagringsstyrenheter** med namnet **okänd enhet** med maskinvaru-ID **ROOT\ISCSIPRT**.

5.  Högerklicka på **okänd enhet** och välj **Uppdatera drivrutin**.

6.  Uppdatera drivrutinen genom att välja alternativet att **Sök automatiskt efter uppdaterade drivrutiner**. Den här uppdateringen bör ändra **okänd enhet** till **Microsoft iSCSI Initiator**:

    ![Skärmbild av Azure Backup Enhetshanteraren, med lagringsstyrenheter markerat](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Gå till **Aktivitetshanteraren** > **tjänster (lokala)**  > **Microsoft iSCSI Initiator Service**:

    ![Skärmbild av Azure Backup Aktivitetshanteraren med tjänster (lokala) markerat](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Starta om Microsoft iSCSI Initiator service. Gör detta genom att högerklicka på tjänsten och välj **stoppa**. Högerklicka igen och välj **starta**.

9.  Försök återställa igen med hjälp av [omedelbar återställning](backup-instant-restore-capability.md).

Om återställningen fortfarande misslyckas, startar du om din server eller klient. Om du inte vill att starta om eller om återställningen fortfarande misslyckas även när du startar om servern, försök [återställa från en annan dator](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten
Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att snabbt lösa ditt problem.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hur du säkerhetskopierar Windows Server med Azure Backup-agenten](tutorial-backup-windows-server-to-azure.md).
* Om du vill återställa en säkerhetskopia kan se [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).
