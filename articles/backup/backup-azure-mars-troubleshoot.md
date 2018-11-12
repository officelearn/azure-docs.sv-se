---
title: Felsöka Azure Backup Agent
description: Felsöka installation och registrering av Azure Backup-agenten
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: 10886f7797571273d50516b944bc3f6e61080b74
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345071"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Felsöka Microsoft Azure Recovery Services (MARS)-agenten

Här är hur du löser problem som kan uppstå under konfiguration, registrering, säkerhetskopiering och återställning.

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga valvautentiseringsuppgifter har angetts
| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| **Fel** </br> *Ogiltiga valvautentiseringsuppgifter har angetts. Filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten. (ID: 34513)* | <ul><li> Autentiseringsuppgifterna för valvet är ogiltiga (det vill säga de laddades ned mer än 48 timmar innan registrering).<li>MARS-agenten kan inte ladda ned filer till Windows Temp-katalog. <li>Valvautentiseringsuppgifterna finns på en nätverksplats. <li>TLS 1.0 är inaktiverat<li> En konfigurerad proxyserver blockerar anslutningen. <br> |  <ul><li>Ladda ned nya valvautentiseringsuppgifter. (**Obs**: Om flera valv credential filerna har hämtats tidigare endast den senaste hämta filen är giltig inom 48 timmar.) <li>Gå till **Internetalternativ** > **Security** > **Internet**. Välj sedan **Anpassad nivå**, och Bläddra tills du ser att hämta filen. Välj sedan **aktivera**.<li>Du kan också behöva lägga till dessa webbplatser i Internet Explorer [betrodda platser](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Ändra inställningarna för att använda en proxyserver. Ange sedan proxyn serverinformation. <li> Matcha datum och tid med din dator.<li>Om du får ett felmeddelande om att filhämtningar inte tillåts, är det troligt att det finns ett stort antal filer i C:/Windows/Temp-katalogen.<li>Gå till C:/Windows/Temp och kontrollera om det finns fler än 60 000 eller 65 000 filer med tillägget .tmp. Om det finns tar du bort dessa filer.<li>Kontrollera att du har .NET framework 4.6.2 eller senare installerat. <li>Om du har inaktiverat TLS 1.0 på grund av PCI-efterlevnad, referera till denna [felsökningssida](https://support.microsoft.com/help/4022913). <li>Om du har ett antivirusprogram installerat på servern, Uteslut följande filer från virusgenomsökning: <ul><li>CBengine.exe<li>CSC.exe, som är relaterade till .NET Framework. Det finns en CSC.exe för varje .NET-version som är installerad på servern. Undanta CSC.exe-filer som är knutna till alla versioner av .NET framework på den berörda servern. <li>Tillfällig plats för mappen eller cache. <br>*Standardplatsen för den temporära mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<br><li>Bin-mappen C:\Program Files\Microsoft Azure Recovery Services Agent\Bin


## <a name="the-mars-agent-was-unable-to-connect-to-azure-backup"></a>MARS-agenten kunde inte ansluta till Azure Backup

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| **Fel** </br><ol><li>*Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup. (ID: 100050) Kontrollera nätverksinställningarna och se till att du kan ansluta till internet*<li>*(407) proxyautentisering krävs* |Proxy blockerar anslutningen. |  <ul><li>Gå till **Internetalternativ** > **Security** > **Internet**. Välj sedan **Anpassad nivå** och Bläddra tills du ser att hämta filen. Välj **Aktivera**.<li>Du kan också behöva lägga till dessa webbplatser i Internet Explorer [betrodda platser](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Ändra inställningarna för att använda en proxyserver. Ange sedan proxyn serverinformation. <li>Om du har ett antivirusprogram installerat på servern, Uteslut följande filer från virusgenomsökning. <ul><li>CBEngine.exe (i stället för dpmra.exe).<li>CSC.exe (relaterade till .NET Framework). Det finns en CSC.exe för varje .NET-version som är installerad på servern. Undanta CSC.exe-filer som är knutna till alla versioner av .NET framework på den berörda servern. <li>Tillfällig plats för mappen eller cache. <br>*Standardplatsen för den temporära mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>Bin-mappen C:\Program Files\Microsoft Azure Recovery Services Agent\Bin


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Det gick inte att ange krypteringsnyckeln för säker säkerhetskopiering

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |      
| **Fel** </br>*Det gick inte att ange krypteringsnyckeln för säker säkerhetskopiering. Aktiveringen lyckades inte helt men den krypterade lösenfrasen som har sparats till följande fil*. |<li>Servern är redan registrerad med ett annat valv.<li>Under konfigurationen var lösenfrasen skadad.| Avregistrera servern från valvet och registrera igen med en ny lösenfras.

## <a name="the-activation-did-not-complete-successfully"></a>Aktiveringen slutfördes inte

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
|**Fel** </br><ol>*Aktiveringen slutfördes inte. Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FC07]. Försök igen efter en stund. Kontakta Microsoft-supporten om problemet kvarstår*     | <li> Den temporära mappen finns på en volym som har inte tillräckligt med utrymme. <li> Den temporära mappen har felaktigt flyttats till en annan plats. <li> Filen OnlineBackup.KEK saknas.         | <li>Uppgradera till den [senaste versionen](https://aka.ms/azurebackup_agent) av MARS-agenten.<li>Flytta den tillfälliga mapp eller cache-platsen till en volym med ledigt utrymme som motsvarar 5 – 10% av den totala mängden säkerhetskopierade data. Om du vill flytta cacheplatsen, läser du anvisningarna i [frågor om Azure Backup-agenten](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Kontrollera att filen OnlineBackup.KEK finns. <br>*Standardplatsen för den temporära mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Krypteringslösenfrasen inte korrekt konfigurerad

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
|**Fel** </br><ol>*Fel vid 34506. Krypteringslösenfrasen lagras på den här datorn är inte korrekt konfigurerad*.    | <li> Den temporära mappen finns på en volym som har inte tillräckligt med utrymme. <li> Den temporära mappen har felaktigt flyttats till en annan plats. <li> Filen OnlineBackup.KEK saknas.        | <li>Uppgradera till den [senaste versionen](https://aka.ms/azurebackup_agent) av MARS-agenten.<li>Flytta den temporära mappen eller cacheplatsen till en volym med ledigt utrymme som motsvarar 5 – 10% av den totala mängden säkerhetskopierade data. Om du vill flytta cacheplatsen, läser du anvisningarna i [frågor om Azure Backup-agenten](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Kontrollera att filen OnlineBackup.KEK finns. <br>*Standardplatsen för den temporära mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Säkerhetskopieringar kan inte köras enligt schemat
Om schemalagda säkerhetskopieringar inte hämta utlöses automatiskt, även om manuella säkerhetskopieringar fungera utan problem, kan du prova följande åtgärder:

- Se om PowerShell 3.0 eller senare är installerat på servern. Kör följande kommando för att kontrollera PowerShell-version och kontrollera att den *större* versionsnumret är lika med eller större än 3.

  `$PSVersionTable.PSVersion`

- Se om följande sökväg är en del av den *PSMODULEPATH* miljövariabeln.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Om PowerShell-körningsprincipen för *LocalMachine* är har angetts till begränsad PowerShell-cmdlet som utlöser åtgärden misslyckas. Kör följande kommandon i upphöjt läge, att kontrollera och ställa in körningsprincipen till antingen *Unrestricted* eller *RemoteSigned*.

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Gå till **Kontrollpanelen** > **Administrationsverktyg** > **Schemaläggaren**. Expandera **Microsoft**, och välj **onlinesäkerhetskopieringen**. Dubbelklicka på **Microsoft OnlineBackup**, och gå till den **utlösare** fliken. Kontrollera att statusen är inställd på **aktiverad**. Om det inte finns väljer **redigera**, och välj den **aktiverad** markerar du kryssrutan. På den **Allmänt** går du till fliken **säkerhetsalternativ**. Se till att det användarkonto som valts för att köra uppgiften är antingen **SYSTEM** eller **gruppen lokala administratörer** på servern.


> [!TIP]
> För att säkerställa att ändringarna tillämpas konsekvent, startar du om servern när du har utfört stegen ovan.


## <a name="troubleshoot-restore-issues"></a>Felsökning av problem med återställning

Azure Backup kan inte har montera återställningsvolymen, även om några minuter. Du kan också få felmeddelanden under processen. Om du vill börja återställa normalt, Följ dessa steg:

1.  Avbryt den pågående mount-processen och om den har körts i flera minuter.

2.  Se om du använder den senaste versionen av Backup-agenten. Att ta reda på versionen på den **åtgärder** fönstret MARS-konsolen väljer **om Microsoft Azure Recovery Services Agent**. Bekräfta att den **Version** antalet är lika med eller högre än den version som nämns i [i den här artikeln](https://go.microsoft.com/fwlink/?linkid=229525). Du kan hämta den senaste versionen från [här](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Gå till **Enhetshanteraren** > **lagringsstyrenheter**, och leta upp **Microsoft iSCSI Initiator**. Om du kan hitta den, går du direkt till steg 7.

4.  Om du inte kan hitta Microsoft iSCSI Initiator service, försöker att hitta någon post under **Enhetshanteraren** > **lagringsstyrenheter** kallas **okänd enhet**, med maskinvaru-ID **ROOT\ISCSIPRT**.

5.  Högerklicka på **okänd enhet**, och välj **Uppdatera drivrutin**.

6.  Uppdatera drivrutinen genom att välja alternativet att **Sök automatiskt efter uppdaterade drivrutiner**. Slutförande av uppdateringen bör ändra **okänd enhet** till **Microsoft iSCSI Initiator**, enligt nedan.

    ![Skärmbild av Azure Backup Enhetshanteraren, med lagringsstyrenheter markerat](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Gå till **Aktivitetshanteraren** > **tjänster (lokala)** > **Microsoft iSCSI Initiator Service**.

    ![Skärmbild av Azure Backup Aktivitetshanteraren med tjänster (lokala) markerat](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Starta om Microsoft iSCSI Initiator service. Gör detta genom att högerklicka på tjänsten, väljer **stoppa**, högerklicka igen och välj **starta**.

9.  Försök återställa igen med hjälp av **omedelbar återställning**.

Om återställningen fortfarande misslyckas, startar du om din server eller klient. Om du inte vill starta om eller återställningen fortfarande misslyckas efter att servern startas om, försök att återställa från en annan dator. Följ stegen i [i den här artikeln](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten
Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hur du säkerhetskopierar Windows Server med Azure Backup-agenten](tutorial-backup-windows-server-to-azure.md).
* Om du behöver återställa en säkerhetskopia använder du den här artikeln för att [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).
