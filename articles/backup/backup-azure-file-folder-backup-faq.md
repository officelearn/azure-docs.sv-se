---
title: Vanliga frågor när du säkerhetskopierar filer och mappar med Azure Backup
description: Vanliga frågor om hur du säkerhetskopierar filer och mappar med Azure Backup-adresser.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: dacurwin
ms.openlocfilehash: 5dbd4fefd5c5e1acd7e12ace547ddb8866b7f081
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148596"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Vanliga frågor om hur du säkerhetskopierar filer och mappar 

Den här artikeln innehåller svar på vanliga frågor fler säkerhetskopiera filer och mappar med Microsoft Azure Recovery Services MARS-agenten i den [Azure Backup](backup-overview.md) service.

## <a name="general"></a>Allmänt

### <a name="why-does-the-mars-agent-need-net-framework-452-or-higher"></a>Varför MARS-agenten måste .NET framework 4.5.2 eller senare?

Nya funktioner som är tillgängliga i [omedelbar återställning](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) måste .NET Framework 4.5.2 eller senare.

## <a name="configure-backups"></a>Konfigurera säkerhetskopieringar

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Var kan jag hämta den senaste versionen av MARS-agenten? 
Senaste MARS-agenten används när du säkerhetskopierar Windows Server-datorer, System Center DPM och Microsoft Azure Backup server är tillgänglig för [hämta](https://aka.ms/azurebackup_agent). 

### <a name="how-long-are-vault-credentials-valid"></a>Hur lång tid är giltiga autentiseringsuppgifter för valv?
Valvautentiseringsuppgifterna upphör att gälla efter 48 timmar. Om filen med autentiseringsuppgifter upphör att gälla, kan du hämta filen igen från Azure-portalen.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Från vilka enheter kan jag säkerhetskopiera filer och mappar? 

Du kan inte säkerhetskopiera följande typer av enheter och volymer:

* Flyttbart medium: Alla källor för säkerhetskopieringsobjekt måste rapporteras som fasta.
* Skrivskyddade volymer: Volymen måste vara skrivbar för tjänsten volume shadow copy (VSS) för att fungera.
* Offlinevolymer: Volymen måste vara online för att VSS ska fungera.
* Nätverksresurser: Volymen måste vara lokal på servern som ska säkerhetskopieras med onlinesäkerhetskopiering.
* BitLocker-skyddade volymer: Volymen måste vara upplåst innan säkerhetskopieringen kan utföras.
* Identifiering av filsystem: NTFS är det enda filsystem som stöds.

### <a name="what-file-and-folder-types-are-supported"></a>Vilka typer av filer och mappar stöds?

Följande typer stöds:

* Krypterade
* Komprimerade
* Utspridda
* Komprimerade + utspridda
* Hårda länkar: Stöds inte, hoppas över
* Referenspunkt: Stöds inte, hoppas över
* Krypterade + utspridda: Stöds inte, hoppas över
* Komprimerade Stream: Stöds inte, hoppas över
* Referenspunkt, inklusive DFS-länkar och knutpunkter


### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Kan jag använda MARS-agenten för att säkerhetskopiera filer och mappar på en Azure-dator?  
Ja. Azure Backup innehåller VM-nivå säkerhetskopiering för virtuella Azure-datorer med hjälp av VM-tillägget för Azure VM-agenten. Du kan installera MARS-agenten för att göra det om du vill säkerhetskopiera filer och mappar på gästoperativsystemet för Windows på den virtuella datorn. 

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Kan jag använda MARS-agenten för säkerhetskopiering av filer och mappar med tillfällig lagring för virtuella Azure-datorer? 
Ja. Installera MARS-agenten och säkerhetskopiera filer och mappar på Windows-gästoperativsystemet till ett tillfälligt lagringsutrymme. -Säkerhetskopieringsjobb misslyckas om tillfällig lagring data om du rensar.
- Om data i tillfällig lagring tas bort kan återställa du bara till beständig lagring.

### <a name="how-do-i-register-a-server-to-another-region"></a>Hur registrerar jag mig en server till en annan region?

Säkerhetskopierade data skickas till datacentret för det valv som servern är registrerad. Det enklaste sättet att ändra datacentret är att avinstallera och installera om agenten och sedan registrerar datorn till ett nytt valv i den region som du behöver

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Deduplicerar MARS-agenten support Windows Server 2012?
Ja. MARS-agenten konverterar deduplicerade data till vanliga data när den förbereder säkerhetskopieringen. Den och sedan optimerar data för säkerhetskopiering, krypterar data och skickar sedan krypterade data till valvet.

## <a name="manage-backups"></a>Hantera säkerhetskopior

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Vad händer om jag byter namn på en Windows-dator som konfigureras för säkerhetskopiering?

När du byter namn på en Windows-dator, stoppas alla konfigurerade säkerhetskopieringar.

- Du behöver registrera namnet på nya datorn med Backup-valvet.
- När du registrerar ett nytt namn med valvet, den första åtgärden är en *fullständig* säkerhetskopiering.
- Om du behöver återställa data som säkerhetskopierats till valvet med det gamla servernamnet kan du använda alternativet för att återställa till en annan plats i guiden Återställ Data. [Läs mer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine). 

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Vad är den maximala längden på sökvägen för säkerhetskopiering?
MARS-agenten använder NTFS och använder den specifikationen för filsökvägarnas längd begränsas av de [Windows API](/windows/desktop/FileIO/naming-a-file#fully_qualified_vs._relative_paths). Om du vill skydda filerna är längre än det tillåtna värdet kan du säkerhetskopiera den överordnade mappen eller diskenheten.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Vilka tecken tillåts i filsökvägar?

MARS-agenten använder NTFS och tillåter [stöds tecken](/windows/desktop/FileIO/naming-a-file#naming_conventions) i namn/sökvägar.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Varningen ”Azure-säkerhetskopieringar inte har konfigurerats för den här servern” visas.
Den här varningen kan visas även om du har konfigurerat en säkerhetskopieringsprincip när inställningarna för Säkerhetskopieringsschemat på den lokala servern inte är samma som inställningarna som lagras i säkerhetskopieringsvalvet.
- När servern eller inställningarna har återställts till ett fungerande tillstånd, kan scheman för säkerhetskopiering bli osynkroniserad.
- Om du får den här varningen [konfigurera](backup-azure-manage-windows-server.md) säkerhetskopieringsprincipen igen och kör en på begäran-säkerhetskopiering att omsynkronisera den lokala servern med Azure.


## <a name="manage-the-backup-cache-folder"></a>Hantera säkerhetskopiering cachemappen

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Vilken är den minsta nödvändiga storleken på cachelagringsmappen?
Storleken på cachelagringsmappen avgör mängden data som säkerhetskopieras.
- Volymer för cache-mappen bör ha ledigt utrymme som är lika med minst 5 – 10% av den totala mängden säkerhetskopierade data.
- Om volymen har mindre än 5% ledigt utrymme, öka volymstorleken eller flytta cachelagringsmappen till en volym med tillräckligt med utrymme.
- 
### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Hur ändrar jag cachelagringsplatsen för MARS-agenten?


1. Kör följande kommando i en upphöjd kommandotolk för att stoppa Backup-motorn:

    ```PS C:\> Net stop obengine```

2. Flytta inte filerna. Kopiera i stället cachelagringsmappen till en annan enhet som har tillräckligt med utrymme.
3. Uppdatera följande registerposter med sökvägen till den nya cachelagringsmappen.<br/>

    | Sökväg i registret | Registernyckel | Värde |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Ny plats för cachemappen* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Ny plats för cachemappen* |

4. Starta om Backup-motorn i en upphöjd kommandotolk:

    ```PS C:\> Net start obengine```

5. När säkerhetskopieringen är klar med den nya platsen, kan du ta bort den ursprungliga cachelagringsmappen.


### <a name="where-should-the-cache-folder-be-located"></a>Där cachemappen placeras?

Följande platser rekommenderas inte för cachelagringsmappen:

* Nätverket resurs/flyttbart medium: Cachelagringsmappen måste vara lokal på servern som ska säkerhetskopieras med onlinesäkerhetskopiering. Nätverksplatser eller flyttbara medier som USB-enheter stöds inte
* Offlinevolymer: Cachelagringsmappen måste vara online för väntad säkerhetskopiering med Azure Backup-agenten

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Finns det några attribut för cachelagringsmappen som inte stöds?
Följande attribut eller deras kombinationer stöds inte för cachelagringsmappen:

* Krypterade
* Deduplicerade
* Komprimerade
* Utspridda
* Referenspunkt

Cachelagringsmappen och den virtuella hårddisken för metadata har inte de attribut som krävs för Azure Backup-agenten.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Finns det ett sätt att justera mängden bandbredd som används för säkerhetskopiering?
 
Ja, du kan använda den **ändra egenskaper för** alternativ i MARS-agenten för att justera bandbredden och val av tidpunkt. [Läs mer](backup-configure-vault.md#enable-network-throttling)**.

## <a name="restore"></a>Återställ

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Vad händer om jag avbryter en pågående återställningsjobbet?

Om en pågående återställningsjobbet avbryts så stoppas återställningsprocessen. Alla filer som har återställts innan avbrottet Behåll konfigurerade mål (ursprungliga eller en annan plats), utan några återställningar.


## <a name="next-steps"></a>Nästa steg

[Lär dig](tutorial-backup-windows-server-to-azure.md) säkerhetskopiering av en Windows-dator.
