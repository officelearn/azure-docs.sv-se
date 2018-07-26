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
ms.openlocfilehash: e7a63167285c06fdfe632e7d45d9fddd3cca7842
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248530"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent-issues"></a>Felsöka problem med agentens för Microsoft Azure Recovery Services (MARS)
## <a name="recommended-steps"></a>Rekommenderade åtgärder
I den här artikeln för att lösa fel under konfigurationen, registrering, säkerhetskopiering och återställning med hjälp av MARS-agenten.

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Ogiltiga valvautentiseringsuppgifter har angetts. Filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten.
| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| **Fel** </br> *Ogiltiga valvautentiseringsuppgifter har angetts. Filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten. (ID: 34513)* | <ul><li> Autentiseringsuppgifterna för valvet är ogiltiga (det vill säga de laddades ned mer än 48 timmar innan registrering).<li>MARS-agenten kan inte ladda ned filer till Windows Temp-katalog. <li>Valvautentiseringsuppgifterna finns på en nätverksplats. <li>TLS 1.0 är inaktiverat<li> En konfigurerad proxyserver blockerar anslutningen. <br> |  <ul><li>Ladda ned nya valvautentiseringsuppgifter.<li>Gå till **Internetalternativ** > **Security** > **Internet**. Välj sedan **Anpassad nivå**, och Bläddra tills du ser att hämta filen. Välj sedan **aktivera**.<li>Du kan också behöva lägga till webbplatsen till [betrodda platser](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Ändra inställningarna för att använda en proxyserver. Ange sedan proxyn serverinformation. <li> Matcha datum och tid med din dator.<li>Gå till C:/Windows/Temp och kontrollera om det finns fler än 60 000 eller 65 000 filer med tillägget .tmp. Om det finns tar du bort dessa filer.<li>Du kan kontrollera det här körs SDP-paketet på servern. Om du får ett felmeddelande om att filhämtningar inte tillåts, är det troligt att det finns ett stort antal filer i C:/Windows/Temp-katalogen.<li>Kontrollera att du har .NET framework 4.6.2 eller senare installerat. <li>Om du har inaktiverat TLS 1.0 på grund av PCI-efterlevnad, referera till denna [felsökningssida](https://support.microsoft.com/help/4022913). <li>Om du har ett antivirusprogram installerat på servern, Uteslut följande filer från virusgenomsökning: <ul><li>CBengine.exe<li>CSC.exe, som är relaterade till .NET Framework. Det finns en CSC.exe för varje .NET-version som är installerad på servern. Undanta CSC.exe-filer som är knutna till alla versioner av .NET framework på den berörda servern. <li>Tillfällig plats för mappen eller cache. <br>*Standardplatsen för den temporära mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| **Fel** </br><ol><li>*Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup. (ID: 100050) Kontrollera nätverksinställningarna och se till att du kan ansluta till internet*<li>*(407) proxyautentisering krävs* |Proxy blockerar anslutningen. |  <ul><li>Gå till **Internetalternativ** > **Security** > **Internet**. Välj sedan **Anpassad nivå** och Bläddra tills du ser att hämta filen. Välj **Aktivera**.<li>Du kan också behöva lägga till webbplatsen till [betrodda platser](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Ändra inställningarna för att använda en proxyserver. Ange sedan proxyn serverinformation. <li>Om du har ett antivirusprogram installerat på servern, Uteslut följande filer från virusgenomsökning. <ul><li>CBEngine.exe (i stället för dpmra.exe).<li>CSC.exe (relaterade till .NET Framework). Det finns en CSC.exe för varje .NET-version som är installerad på servern. Undanta CSC.exe-filer som är knutna till alla versioner av .NET framework på den berörda servern. <li>Tillfällig plats för mappen eller cache. <br>*Standardplatsen för den temporära mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Det gick inte att ange krypteringsnyckeln för säker säkerhetskopiering

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |      
| **Fel** </br>*Det gick inte att ange krypteringsnyckeln för säker säkerhetskopiering aktiveringen lyckades inte helt men den krypterade lösenfrasen som har sparats till följande fil*. |<li>Servern är redan registrerad med ett annat valv.<li>Under konfigurationen var lösenfrasen skadad| Avregistrera servern från valvet och registrera igen med en ny lösenfras.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>Aktiveringen slutfördes inte. Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FC07]

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
|**Fel** </br><ol>*Aktiveringen slutfördes inte. Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FC07]. Försök igen efter en stund. Kontakta Microsoft-supporten om problemet kvarstår*     | <li> Den temporära mappen finns på en volym som har inte tillräckligt med utrymme. <li> Den temporära mappen har felaktigt flyttats till en annan plats. <li> Filen OnlineBackup.KEK saknas.         | <li>Uppgradera till den [senaste versionen](http://aka.ms/azurebackup_agent) av MARS-agenten.<li>Flytta den tillfälliga mapp eller cache-platsen till en volym med ledigt utrymme som motsvarar 5 – 10% av den totala mängden säkerhetskopierade data. Om du vill flytta cacheplatsen, läser du anvisningarna i [frågor om Azure Backup-agenten](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Kontrollera att filen OnlineBackup.KEK finns. <br>*Standardplatsen för den temporära mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="error-34506-the-encryption-passphrase-stored-on-this-computer-is-not-correctly-configured"></a>Fel vid 34506. Krypteringslösenfrasen lagras på den här datorn är inte korrekt konfigurerad

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
|---------|---------|---------|
|**Fel** </br><ol>*Fel vid 34506. Krypteringslösenfrasen lagras på den här datorn är inte korrekt konfigurerad*.    | <li> Den temporära mappen finns på en volym som har inte tillräckligt med utrymme. <li> Den temporära mappen har felaktigt flyttats till en annan plats. <li> Filen OnlineBackup.KEK saknas.        | <li>Uppgradera till den [senaste versionen](http://aka.ms/azurebackup_agent) av MARS-agenten.<li>Flytta den temporära mappen eller cacheplatsen till en volym med ledigt utrymme som motsvarar 5 – 10% av den totala mängden säkerhetskopierade data. Om du vill flytta cacheplatsen, läser du anvisningarna i [frågor om Azure Backup-agenten](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Kontrollera att filen OnlineBackup.KEK finns. <br>*Standardplatsen för den temporära mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-do-not-run-as-per-schedule"></a>Säkerhetskopieringar kör inte enligt schema
Utför följande steg när schemalagda säkerhetskopieringar hämta inte aktiveras automatiskt, även om manuella säkerhetskopieringar fungera utan problem. 
 
<li>Se till att PowerShell 3.0 eller senare har installerats på servern. Du kan kontrollera PowerShell-version genom att köra följande kommando och kontrollera att den *större* versionsnumret är lika med eller större än 3.

`$PSVersionTable.PSVersion`
<li>Se till att följande sökväg är en del av den *PSMODULEPATH* miljövariabeln.

`<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`
<li>Om powershell-körningsprincipen för den *LocalMachine* är har angetts till begränsad powershell-cmdlet som utlöser åtgärden misslyckas. Kör följande kommandon i upphöjt läge för att kontrollera och ställa in körningsprincipen till antingen *Unrestricted* eller *RemoteSigned*

`PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

`PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`
<li>Gå till Kontrollpanelen -> Administrationsverktyg -> Schemaläggaren -> expanderar Microsoft -> Välj onlinesäkerhetskopiering
<li>Dubbelklicka på aktiviteten ”Microsoft-OnlineBackup” och gå till fliken ”utlösare”.
<li>Kontrollera att statusen för aktiviteten är inställd på 'Enabled'. Om inte, klicka på ”Redigera” och markera kryssrutan 'Enabled'
<li>Navigera till den *säkerhetsalternativ* delen av den *Allmänt* fliken
<li>Se till att det användarkonto som valts för att köra uppgiften är antingen *SYSTEM* eller gruppen lokala administratörer på servern > [!TIP] rekommenderar vi att du startar om servern när du har utfört stegen ovan för att säkerställa att ändringar gjort tillämpas konsekvent


## <a name="troubleshooting-restore-issues"></a>Felsökning av problem med återställning

### <a name="failure-to-mount-the-recovery-volume-during-recovery-of-files-and-folders"></a>Det gick inte att montera återställningsvolymen under återställning av filer och mappar
Om Azure Backup har inte monterar återställningsvolymen även efter ett par minuter om du klickar på **montera** eller går inte att montera återställningsvolymen med ett eller flera fel, Följ stegen nedan för att börja återställa normalt.

1.  Avbryta pågående mount-processen om det har körts i flera minuter.

2.  Se till att du är på den senaste versionen av Azure Backup-agenten. Om du vill veta versionsinformationen för Azure Backup-agenten klickar du på **om Microsoft Azure Recovery Services Agent** på den **åtgärder** rutan i Microsoft Azure Backup konsolen och se till att **Version** antalet är lika med eller högre än den version som nämns i [i den här artikeln](https://go.microsoft.com/fwlink/?linkid=229525). Du kan hämta den senaste versionen från [här](https://go.microsoft.com/fwLink/?LinkID=288905)

3.  Gå till **Enhetshanteraren** -> **lagringsstyrenheter** och se till att du kan hitta **Microsoft iSCSI Initiator**. Om du kan hitta den, gå direkt till steg 7 nedan. 

4.  Om du inte kan hitta Microsoft iSCSI Initiator service som anges i steg 3, kontrollerar du om du hittar en post under **Enhetshanteraren** -> **lagringsstyrenheter** kallas  **Okänd enhet** med maskinvaru-ID **ROOT\ISCSIPRT**.

5.  Högerklicka på **okänd enhet** och välj **Uppdatera drivrutin**.

6.  Uppdatera drivrutinen genom att välja alternativet att **Sök automatiskt efter uppdaterade drivrutiner**. Slutförande av uppdateringen bör ändra **okänd enhet** till **Microsoft iSCSI Initiator** enligt nedan. 

    ![Kryptering](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Gå till **Aktivitetshanteraren** -> **tjänster (lokala)** -> **Microsoft iSCSI Initiator Service**. 

    ![Kryptering](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Starta om Microsoft iSCSI Initiator service genom att högerklicka på tjänsten, klicka på Stoppa, och ytterligare att högerklicka igen och klicka på **starta**.

9.  Försök att återställa med hjälp av omedelbar återställning. 

Starta om servern/klienten om återställningen fortfarande misslyckas. Om en omstart inte är önskvärt eller återställningen fortfarande misslyckas efter att servern startas om, försök att återställa från en annan dator genom att följa stegen som anges i [i den här artikeln](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten
Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hur du säkerhetskopierar Windows Server med Azure Backup-agenten](tutorial-backup-windows-server-to-azure.md).
* Om du behöver återställa en säkerhetskopia använder du den här artikeln för att [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).
