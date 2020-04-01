---
title: Felsöka Azure Backup-agenten
description: I den här artikeln får du lära dig hur du felsöker installation och registrering av Azure Backup-agenten.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: a15f8a4531bc31dab5b99e125454b0d9c4fd4521
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421276"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Felsöka MARS-agenten (Microsoft Azure Recovery Services)

I den här artikeln beskrivs hur du löser fel som kan visas under konfiguration, registrering, säkerhetskopiering och återställning.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du kontrollerar följande innan du börjar felsöka Microsoft Azure Recovery Services (MARS)-agenten:

- [Se till att MARS-agenten är uppdaterad](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Se till att du har nätverksanslutning mellan MARS-agenten och Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Se till att MARS körs (i servicekonsolen). Om du behöver starta om och försöka igen åtgärden.
- [Se till att 5 till 10 % ledigt volymutrymme finns på plats i scratch-mappen](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Kontrollera om en annan process eller ett annat antivirusprogram stör Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Om schemalagd säkerhetskopiering misslyckas men manuell säkerhetskopiering fungerar läser du [Säkerhetskopieringar körs inte enligt schemat](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule).
- Se till att ditt operativsystem har de senaste uppdateringarna.
- [Kontrollera att enheter och filer som inte stöds med attribut som inte stöds är undantagna från säkerhetskopian](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Kontrollera att klockan på det skyddade systemet är konfigurerad till rätt tidszon.
- [Kontrollera att .NET Framework 4.5.2 eller senare är installerat på servern](https://www.microsoft.com/download/details.aspx?id=30653).
- Om du försöker registrera om servern till ett valv:
  - Kontrollera att agenten avinstalleras på servern och att den tas bort från portalen.
  - Använd samma lösenfras som ursprungligen användes för att registrera servern.
- För offlinesäkerhetskopior kontrollerar du att Azure PowerShell 3.7.0 är installerat på både käll- och kopieringsdatorn innan du startar säkerhetskopian.
- Om säkerhetskopieringsagenten körs på en virtuell Azure-dator läser du [den här artikeln](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga valvautentiseringsuppgifter har angetts

**Felmeddelande:** Ogiltiga autentiseringsuppgifter för valvet. Filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten. (ID: 34513)

| Orsak | Rekommenderade åtgärder |
| ---     | ---    |
| **Autentiseringsuppgifter för arkiv är ogiltiga** <br/> <br/> Arkivautentiseringsfiler kan vara skadade eller ha upphört att gälla. (De kan till exempel ha hämtats mer än 48 timmar före registreringstiden.)| Hämta nya autentiseringsuppgifter från Recovery Services-valvet på Azure-portalen. (Se steg 6 i avsnittet [Hämta MARS-agenten.)](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) Vidta sedan dessa åtgärder, beroende på vad som är lämpligt: <ul><li> Om du redan har installerat och registrerat MARS öppnar du MMC-konsolen Microsoft Azure Backup Agent och väljer sedan **Registrera server** i **åtgärdsfönstret** för att slutföra registreringen med de nya autentiseringsuppgifterna. <br/> <li> Om den nya installationen misslyckas kan du prova att installera om med de nya autentiseringsuppgifterna.</ul> **Om**flera valvautentiseringsfiler har hämtats är endast den senaste filen giltig under de närmaste 48 timmarna. Vi rekommenderar att du hämtar en ny arkivautentiseringsfil.
| **Proxyserver/brandvägg blockerar registreringen** <br/>eller <br/>**Ingen internetanslutning** <br/><br/> Om din dator eller proxyserver har begränsad internetanslutning och du inte ser till att åtkomsten för de nödvändiga webbadresserna är nödvändig misslyckas registreringen.| Gör så här:<br/> <ul><li> Samarbeta med IT-teamet för att säkerställa att systemet har internetanslutning.<li> Om du inte har en proxyserver kontrollerar du att proxyalternativet inte är markerat när du registrerar agenten. [Kontrollera proxyinställningarna](#verifying-proxy-settings-for-windows).<li> Om du har en brandväggs-/proxyserver kan du arbeta med nätverksteamet för att se till att dessa webbadresser och IP-adresser har åtkomst:<br/> <br> **Webbadresser**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP-adresser**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Försök att registrera dig igen när du har slutfört de föregående felsökningsstegen.<br></br> Om anslutningen är via Azure ExpressRoute kontrollerar du att inställningarna är konfigurerade enligt beskrivningen i [Azure ExpressRoute-supporten](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Antivirusprogram blockerar registrering** | Om du har installerat antivirusprogram på servern lägger du till nödvändiga undantagsregler i antivirussökningen efter dessa filer och mappar: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> Repmappen. Standardplatsen är C:\Program\Microsoft Azure Recovery Services Agent\Scratch. <li> Lagerplatsmappen på C:\Program\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Ytterligare rekommendationer

- Gå till C:/Windows/Temp och kontrollera om det finns fler än 60 000 eller 65 000 filer med TMP-tillägget. Om det finns, ta bort dessa filer.
- Se till att maskinens datum och tid matchar den lokala tidszonen.
- Kontrollera att [dessa webbplatser](install-mars-agent.md#verify-internet-access) läggs till på dina betrodda platser i Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verifiera proxyinställningar för Windows

1. Ladda ner PsExec från [Sysinternals-sidan.](https://docs.microsoft.com/sysinternals/downloads/psexec)
1. Kör `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` från en upphöjd kommandotolk.

   Det här kommandot öppnas i Internet Explorer.
1. Gå till >  **Internet-alternativ för** > **Internet-alternativ** > **Lan-inställningar**.**Connections**
1. Kontrollera proxyinställningarna för systemkontot.
1. Om ingen proxy har konfigurerats och proxyinformation finns tar du bort informationen.
1. Om en proxy har konfigurerats och proxyinformationen är felaktig kontrollerar du att **proxy-IP-** och **portinformationen** är korrekta.
1. Stäng Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Det går inte att hämta autentiseringsfilen för valvet

| Fel   | Rekommenderade åtgärder |
| ---     | ---    |
|Det gick inte att hämta valvautentiseringsfilen. (ID: 403) | <ul><li> Prova att hämta autentiseringsuppgifterna för valvet med hjälp av en annan webbläsare eller gör så här: <ul><li> Starta Internet Explorer. Välj F12. </li><li> Gå till fliken **Nätverk** och rensa cacheminnet och cookies. </li> <li> Uppdatera sidan.<br></li></ul> <li> Kontrollera om prenumerationen är inaktiverad/har upphört att gälla.<br></li> <li> Kontrollera om någon brandväggsregel blockerar hämtningen. <br></li> <li> Se till att du inte har uttömt gränsen för valvet (50 maskiner per valv).<br></li>  <li> Kontrollera att användaren har Azure Backup-behörigheter som krävs för att hämta autentiseringsuppgifter för valvet och registrera en server med valvet. Se [Använda rollbaserad åtkomstkontroll för att hantera återställningspunkter för Azure Backup](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup

| Fel  | Möjlig orsak | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure Recovery Service Agent kunde inte ansluta till Microsoft Azure Backup. (ID: 100050) Kontrollera nätverksinställningarna och se till att du kan ansluta till internet.<li>(407) Proxy autentisering krävs. |En proxy blockerar anslutningen. |  <ul><li>I Internet Explorer går du till **Verktyg** > **Internet options** > **Security** > **Internet**. Välj **Anpassad nivå** och bläddra ned till avsnittet Hämta **fil.** Välj **Aktivera**.<p>Du kan också behöva lägga till [webbadresser och IP-adresser](install-mars-agent.md#verify-internet-access) till dina betrodda platser i Internet Explorer.<li>Ändra inställningarna för att använda en proxyserver. Ange sedan information om proxyservern.<li> Om din maskin har begränsad internetåtkomst, se till att brandväggsinställningarna på datorn eller proxyn tillåter dessa [webbadresser och IP-adresser](install-mars-agent.md#verify-internet-access). <li>Om du har installerat antivirusprogram på servern utesluter du dessa filer från antivirussökningen: <ul><li>CBEngine.exe (i stället för dpmra.exe).<li>CSC.exe (relaterad till .NET Framework). Det finns en CSC.exe för varje .NET Framework-version installerad på servern. Uteslut CSC.exe-filer för alla versioner av .NET Framework på den berörda servern. <li>Scratch-mappen eller cacheplatsen. <br>Standardplatsen för scratch-mappen eller cachesökvägen är C:\Program\Microsoft Azure Recovery Services Agent\Scratch.<li>Lagerplatsmappen på C:\Program\Microsoft Azure Recovery Services Agent\Bin.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Det gick inte att ange krypteringsnyckeln för säker säkerhetskopiering

| Fel | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| <br />Det gick inte att ställa in krypteringsnyckeln för säkra säkerhetskopior. Aktiveringen lyckades inte helt men krypteringslösenfrasen sparades i följande fil. |<li>Servern är redan registrerad med ett annat valv.<li>Under konfigurationen var lösenfrasen skadad.| Avregistrera servern från valvet och registrera den igen med en ny lösenfras.

## <a name="the-activation-did-not-complete-successfully"></a>Aktiveringen slutfördes inte

| Fel  | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
|<br />Aktiveringen slutfördes inte. Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FC07]. Försök igen efter en stund. Kontakta Microsoft-supporten om problemet kvarstår.     | <li> Scratch-mappen finns på en volym som inte har tillräckligt med utrymme. <li> Scratch-mappen har flyttats felaktigt. <li> Filen OnlineBackup.KEK saknas.         | <li>Uppgradera till den [senaste versionen](https://aka.ms/azurebackup_agent) av MARS-agenten.<li>Flytta scratch-mappen eller cacheplatsen till en volym med ledigt utrymme som är mellan 5 % och 10 % av den totala storleken på säkerhetskopieringsdata. Information om hur du flyttar cacheplatsen korrekt finns i stegen i [Vanliga frågor om säkerhetskopiering av filer och mappar](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Kontrollera att filen OnlineBackup.KEK finns. <br>*Standardplatsen för scratch-mappen eller cachesökvägen är C:\Program\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Krypteringslösenfrasen är inte korrekt konfigurerad

| Fel  | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
| <br />Fel 34506. Krypteringslösenfrasen som lagras på den här datorn är inte korrekt konfigurerad.    | <li> Scratch-mappen finns på en volym som inte har tillräckligt med utrymme. <li> Scratch-mappen har flyttats felaktigt. <li> Filen OnlineBackup.KEK saknas.        | <li>Uppgradera till den [senaste versionen](https://aka.ms/azurebackup_agent) av MARS-agenten.<li>Flytta scratch-mappen eller cacheplatsen till en volym med ledigt utrymme som är mellan 5 % och 10 % av den totala storleken på säkerhetskopieringsdata. Information om hur du flyttar cacheplatsen korrekt finns i stegen i [Vanliga frågor om säkerhetskopiering av filer och mappar](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Kontrollera att filen OnlineBackup.KEK finns. <br>*Standardplatsen för scratch-mappen eller cachesökvägen är C:\Program\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Säkerhetskopior körs inte enligt schema

Om schemalagda säkerhetskopior inte utlöses automatiskt men manuella säkerhetskopior fungerar korrekt kan du prova följande åtgärder:

- Se till att windows server-säkerhetskopieringsschemat inte står i konflikt med schemat för säkerhetskopiering av Azure-filer och mappar.

- Kontrollera att säkerhetskopieringsstatus online är inställd **på Aktivera**. Så här verifierar du status:

  1. Expandera **Microsoft** i Schemaläggaren och välj **Säkerhetskopiering online**.
  1. Dubbelklicka på **Microsoft-OnlineBackup** och gå till fliken **Utlösare.**
  1. Kontrollera om statusen är **aktiverad**. Om den inte är den väljer du **Redigera**, väljer **Aktiverad**och väljer sedan **OK**.

- Kontrollera att användarkontot som valts för att köra uppgiften är **antingen SYSTEM-** eller **Lokala administratörers grupp** på servern. Om du vill verifiera användarkontot går du till fliken **Allmänt** och kontrollerar **säkerhetsalternativen.**

- Kontrollera att PowerShell 3.0 eller senare är installerat på servern. Om du vill kontrollera PowerShell-versionen kör `Major` du det här kommandot och kontrollerar att versionsnumret är 3 eller senare:

  `$PSVersionTable.PSVersion`

- Kontrollera att den här `PSMODULEPATH` sökvägen är en del av miljövariabeln:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Om PowerShell-körningsprincipen `LocalMachine` för `restricted`är inställd på kan PowerShell-cmdlet som utlöser säkerhetskopieringsaktiviteten misslyckas. Kör dessa kommandon i upphöjdt läge för att `Unrestricted` `RemoteSigned`kontrollera och ställa in körningsprincipen till antingen eller :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Kontrollera att det inte saknas eller är skadade PowerShell-modul MSOnlineBackup-filer. Om det finns några filer som saknas eller är skadade gör du så här:

  1. Kopiera mappen MSOnlineBackup från alla datorer som har en MARS-agent som fungerar som den ska från C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. På den problematiska datorn klistrar du in de kopierade filerna på samma mappplats (C:\Program\Microsoft Azure Recovery Services Agent\bin\Modules).

     Om det redan finns en MSOnlineBackup-mapp på datorn klistrar du in filerna i den eller ersätter befintliga filer.

> [!TIP]
> Om du vill vara säkra på att ändringarna tillämpas konsekvent startar du om servern efter att ha utfört föregående steg.

## <a name="resource-not-provisioned-in-service-stamp"></a>Resursen har inte etablerats i servicestämpel

Fel | Möjliga orsaker | Rekommenderade åtgärder
--- | --- | ---
Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel "Resursen har inte etablerats i servicestämpeln". Försök igen efter en tid. (ID: 230006) | Den skyddade servern har bytt namn. | <li> Byt namn på servern till det ursprungliga namnet som registrerats med valvet. <br> <li> Registrera om servern till valvet med det nya namnet.


## <a name="troubleshoot-restore-problems"></a>Felsöka återställningsproblem

Azure Backup kanske inte kan montera återställningsvolymen, även efter flera minuter. Och du kan få felmeddelanden under processen. För att börja återställa normalt, vidta följande åtgärder:

1. Avbryt monteringsprocessen om den har pågått i flera minuter.

2. Kontrollera om du har den senaste versionen av säkerhetskopieringsagenten. Om du vill kontrollera versionen väljer du **Om Microsoft Azure Recovery Services Agent**i fönstret **Åtgärder** på MARS-konsolen . Bekräfta att **versionsnumret** är lika med eller högre än den version som nämns i [den här artikeln](https://go.microsoft.com/fwlink/?linkid=229525). Välj den här länken om du vill [hämta den senaste versionen](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Gå till > **Enhetshanterarens lagringsstyrenheter** och leta reda på **Microsoft iSCSI Initiator**. **Device Manager** Om du hittar den går du direkt till steg 7.

4. Om du inte kan hitta Tjänsten Microsoft iSCSI Initiator kan du försöka **Unknown Device** hitta en post under **Enhetshanterarens** > **lagringsstyrenheter** med namnet Okänd enhet med maskinvaru-ID **ROOT\ISCSIPRT**.

5. Högerklicka på **Okänd enhet** och välj Uppdatera **drivrutinsprogram**.

6. Uppdatera drivrutinen genom att välja alternativet att **söka automatiskt efter uppdaterad drivrutin**. Den här uppdateringen bör ändra **okänd enhet** till **Microsoft iSCSI Initiator:**

    ![Skärmbild av Azure Backup Device Manager, med lagringsstyrenheter markerade](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Gå till **Task Manager** > **Services (Local)** > **Microsoft iSCSI Initiator Service:**

    ![Skärmbild av Aktivitetshanteraren för Azure Backup, med tjänster (lokala) markerade](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Starta om tjänsten Microsoft iSCSI Initiator. Det gör du genom att högerklicka på tjänsten och välja **Stoppa**. Högerklicka sedan på den igen och välj **Start**.

9. Försök återställa igen med hjälp av [Omedelbar återställning](backup-instant-restore-capability.md).

Om återställningen fortfarande misslyckas startar du om servern eller klienten. Om du inte vill starta om, eller om återställningen fortfarande misslyckas även efter att du har startat om servern, kan du försöka [återställa från en annan dator](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Felsöka cacheproblem

Säkerhetskopiering kan misslyckas om cachemappen (även kallad scratch-mapp) är felaktigt konfigurerad, saknar förutsättningar eller har begränsad åtkomst.

### <a name="prerequisites"></a>Krav

För att MARS-agentåtgärder ska lyckas måste cachemappen uppfylla nedanstående krav:

- [Se till att 5% till 10% ledigt volymutrymme finns i scratch mappen plats](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Se till att plats för scratch-mappen är giltig och tillgänglig](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Kontrollera att filattribut i cachemappen stöds](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Se till att det allokerade lagringsutrymmet för skuggkopiering är tillräckligt för säkerhetskopieringsprocessen](#increase-shadow-copy-storage)
- [Se till att det inte finns några andra processer (t.ex. antivirusprogram) som begränsar åtkomsten till cachemappen](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Öka lagring av skuggkopiering

Säkerhetskopieringsåtgärder kan misslyckas om det inte finns tillräckligt med lagringsutrymme för skuggkopiering som krävs för att skydda datakällan. LÃ¶s problemet genom att öka lagringsutrymmet för skuggkopiering på den skyddade volymen med vssadmin enligt nedan:

- Kontrollera det aktuella skugglagringsutrymmet från den upphöjda kommandotolken:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Öka skugglagringsutrymmet med kommandot nedan:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>En annan process eller antivirusprogram blockerar åtkomst till cachemapp

Om du har installerat antivirusprogram på servern lägger du till nödvändiga undantagsregler i antivirussökningen efter dessa filer och mappar:  

- Repmappen. Standardplatsen är C:\Program\Microsoft Azure Recovery Services Agent\Scratch
- Lagerplatsmappen på C:\Program\Microsoft Azure Recovery Services Agent\Bin
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Vanliga problem

Det här avsnittet beskriver de vanliga fel som du stöter på när du använder MARS-agenten.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Felmeddelande | Rekommenderad åtgärd |
-- | --
Microsoft Azure Recovery Services-agenten kunde inte komma åt kontrollsumman för säkerhetskopian på den tillfälliga platsen | LÃ¶s problemet genom att lÃ¶sa nedan och starta om servern <br/> - [Kontrollera om det finns ett antivirusprogram eller andra processer som låser repplatsfilerna](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Kontrollera om scratch-platsen är giltig och tillgänglig för Mars Agent.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Felmeddelande | Rekommenderad åtgärd |
-- | --
Microsoft Azure Recovery Services-agenten kunde inte komma åt den tillfälliga platsen för att initiera VHD | LÃ¶s problemet genom att lÃ¶sa nedan och starta om servern <br/> - [Kontrollera om det finns ett antivirusprogram eller andra processer som låser repplatsfilerna](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Kontrollera om scratch-platsen är giltig och tillgänglig för Mars Agent.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Felmeddelande | Rekommenderad åtgärd |
-- | --
Säkerhetskopieringen misslyckades på grund av otillräcklig lagring i volym där repmappen finns | Lös problemet genom att kontrollera stegen nedan och försöka igen:<br/>- [Se till att MARS-agenten är senaste](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Verifiera och lösa lagringsproblem som påverkar reputrymmet för säkerhetskopiering](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Felmeddelande | Rekommenderad åtgärd |
-- | --
Det går inte att hitta ändringar i en fil. Detta kan bero på olika orsaker. Försök att utföra åtgärden igen. | Lös problemet genom att kontrollera stegen nedan och försöka igen:<br/> - [Se till att MARS-agenten är senaste](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Verifiera och lösa lagringsproblem som påverkar reputrymmet för säkerhetskopiering](#prerequisites)

## <a name="next-steps"></a>Nästa steg

- Få mer information om [hur du säkerhetskopierar Windows Server med Azure Backup-agenten](tutorial-backup-windows-server-to-azure.md).
- Om du behöver återställa en säkerhetskopia läser du [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).
