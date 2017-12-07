---
title: "Felsöka Azure Backup-agenten (MARS-agenten) | Microsoft Docs"
description: "Felsökning av installation och registrering av Azure Backup-agenten"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shreeshd
editor: 
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: da297177ca56822f7d50dbe06ec022640bf5d0cb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="troubleshooting-azure-backup-agent-configurationregistration-issues"></a>Felsökning av problem med Azure Backup Agent konfiguration och registrering
## <a name="recommended-steps"></a>Rekommenderade åtgärder
Referera till den rekommenderade åtgärder som anges nedan för att matcha motsvarande fel visas under konfigurationen eller registrering av Azure Backup-agenten.

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Fel: Ogiltiga autentiseringsuppgifter har angetts. Filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten.
| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| **Fel** </br> *Ogiltiga valvautentiseringsuppgifter har angetts. Filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten. (ID: 34513)* | <ol><li> Autentiseringsuppgifter för valv är inte giltiga (dvs. de har hämtats mer än 48 timmar innan registreringen).<li>   Azure Backup-agenten kan inte hämta en temporär fil till Temp-mappen i Windows. <li>De Valvautentiseringsuppgifter som finns på en nätverksplats. <li>TLS 1.0 är inaktiverad<li> En konfigurerad proxyserver blockerar anslutningen <br> |  <ol><li>Hämta nya autentiseringsuppgifter för valv<li>Gå till internet-alternativ > Säkerhet > Internet > Anpassad nivå > Bläddra till avsnittet av filen ladda ned och välj Aktivera.<li>Du kanske också lägga till webbplatsen och [tillförlitliga platser](https://docs.microsoft.com/en-us/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Ändra inställningarna för att använda proxy och ge proxyinformationen <li> Matcha datum och tid (UTC) din dator<li>Gå till C:/Windows/Temp och kontrollera om det finns mer än 60 000 eller 65 000 filer (med filtillägget tmp) och ta bort dessa filer.<li>Du kan testa detta genom att köra SDP-paketet på servern. Om du får felet anger filhämtningar tillåts inte, kan du vara relativt säkra om många filer i C:/Windows/Temp-katalog.<li>Kontrollera att du har .NET framework 4.6.2 installerat <li>Om du har inaktiverat TLS 1.0 på grund av PCI-överensstämmelse, [felsöka länk](https://support.microsoft.com/help/4022913). <li>Om du har ett antivirusprogram installerat på servern, Uteslut följande filer från virusgenomsökning. <ol><li>CBengine.exe<li>CSC.exe (relaterade till .NET Framework. Det finns en CSC.exe per .NET-version som är installerade på servern. Undanta alla CSC.exe-filer som är knutna till alla versioner av .NET framework på servern.) <li>Scratch plats för mappen eller cache. <br>*Standardplatsen för den tillfälliga mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="error-the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Fel: Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |
| **Fel** </br><ol><li>*Microsoft Azure Recovery Service-agenten kunde inte ansluta till Microsoft Azure Backup. (ID: 100050) Kontrollera nätverksinställningarna och se till att du är ansluten till internet*<li>*(407) proxyautentisering krävs* |Proxyservern blockerar anslutningen * |  <ol><li>Gå till internet-alternativ > Säkerhet > Internet > Anpassad nivå > Bläddra till avsnittet av filen ladda ned och välj Aktivera.<li>Du kanske också lägga till webbplatsen och [tillförlitliga platser](https://docs.microsoft.com/en-us/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Ändra inställningarna för att använda proxy och ge proxyinformationen <li>Om du har ett antivirusprogram installerat på servern, Uteslut följande filer från virusgenomsökning. <ol><li>CBengine.exe<li>(i stället för dpmra.exe)<li>CSC.exe (relaterade till .NET Framework. Det finns en CSC.exe per .NET-version som är installerade på servern. Undanta alla CSC.exe-filer som är knutna till alla versioner av .NET framework på servern.) <li>Scratch plats för mappen eller cache <br>*Standardplatsen för den tillfälliga mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="error-failed-to-set-the-encryption-key-for-secure-backups"></a>Fel: Kunde inte ange krypteringsnyckeln för säker säkerhetskopieringar

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |      
| **Fel** </br>*Kunde inte ange krypteringsnyckeln för säker säkerhetskopieringar. Den aktuella åtgärden misslyckades på grund av ett internt fel: Ogiltig inkommande tjänstfel'. Försök igen efter en stund. Kontakta Microsoft-supporten om problemet kvarstår* |Servern är redan registrerad med en annan valvet| Avregistrera servern från valvet och registrera igen.

## <a name="error-the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>Fel: Aktiveringen slutfördes inte. Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FC07]

| Felinformation | Möjliga orsaker | Rekommenderade åtgärder |
| ---     | ---     | ---    |          
| **Fel** </br><ol><li>*Aktiveringen slutfördes inte. Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FC07]. Försök igen efter en stund. Kontakta Microsoft-supporten om problemet kvarstår* <li>*Fel 34506. Den krypteringslösenfras som sparats på den här datorn är inte korrekt konfigurerad* | <li> Tillfälliga mappen finns på en volym som har tillräckligt med utrymme <li> Flytta tillfälliga mapp felaktigt till en annan plats <li> OnlineBackup.KEK filen saknas | <li>Flytta den tillfälliga mappen eller cacheplats till en volym med ledigt utrymme som motsvarar 5-10% av den totala storleken för säkerhetskopierade data. Följer du stegen som anges i [i den här artikeln](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup) att flytta placeringen i cachen.<li> Kontrollera att OnlineBackup.KEK filen finns <br>*Standardplatsen för den tillfälliga mappen eller sökvägen till cache är C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten
Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.

## <a name="next-steps"></a>Nästa steg
* Visa mer detaljerad information om [säkerhetskopiera din Windows-Server med Azure Backup-agenten](tutorial-backup-windows-server-to-azure.md).
* Om du behöver återställa en säkerhetskopia använder du den här artikeln för att [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).
