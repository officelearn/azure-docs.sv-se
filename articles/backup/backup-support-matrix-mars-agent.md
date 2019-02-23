---
title: Stödmatris för säkerhetskopiering av datorer som kör Microsoft Azure Recovery Services MARS-agenten med Azure Backup
description: Den här artikeln sammanfattas Azure Backup stöd när du säkerhetskopierar datorer som kör Microsoft Azure Recovery Services MARS-agenten.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: b579816a4367537a7da4aa0f68c8776465295448
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670761"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Stödmatris för säkerhetskopiering med Microsoft Azure Recovery Services MARS-agenten

Du kan använda den [Azure Backup-tjänsten](backup-overview.md) för säkerhetskopiering av lokala datorer och appar och virtuella Azure-datorer. Den här artikeln sammanfattas support inställningar och begränsningar för säkerhetskopiering av datorer med Microsoft Azure Recovery Services MARS-agenten.

## <a name="about-the-mars-agent"></a>Om MARS-agenten

MARS-agenten används av Azure Backup för att säkerhetskopiera data från lokala datorer och virtuella Azure-datorer till en backup Recovery Services-valv i Azure. MARS-agenten kan användas på följande sätt:
- Kör agenten på den lokala Windows-datorer så att de kan säkerhetskopiera direkt till ett säkerhetskopieringsvalv i Azure Recovery Services.
- Kör agenten på Windows Azure virtuella datorer så att du kan säkerhetskopiera direkt till ett valv.
- Kör agenten på en Microsoft Azure Backup Server (MABS) eller en System Center Data Protection - server Manager (DPM). I det här scenariot, datorer och arbetsbelastningar som säkerhetskopiera till MABS/DPM och sedan MABS/DPM backas säkerhetskopiera till ett valv i Azure med hjälp av MARS-agenten. 

Vad du kan säkerhetskopiera beror på där agenten är installerad.

- [Läs mer](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) om arkitektur för säkerhetskopiering med MARS-agenten.
- [Läs mer]() om MABS/DPM [säkerhetskopiera arkitektur](backup-architecture.md#architecture-back-up-to-dpmmabs)och [krav](backup-support-matrix-mabs-dpm.md).


## <a name="supported-installation"></a>Installation som stöds

**Installera** | **Detaljer**
--- | ---
**Hämta senaste MARS-agenten** | Du kan hämta den senaste versionen av agenten från valvet eller [ladda ned den direkt](https://aka.ms/azurebackup_agent).
**Installera direkt på en dator** | Du kan installera MARS-agenten direkt på en lokal Windows server eller Windows Azure virtuell dator som kör någon av de [operativsystem som stöds]().
**Installera på en sekundär server** | När du ställer in DPM- eller MABS att säkerhetskopiera till Azure, hämta och installera MARS-agenten på servern. Agenten kan installeras i enlighet med den [operativsystem som stöds](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) i matrisen säkerhetskopieringsserver support.

> [!NOTE]
> Har en Azure Backup-tillägg-installation av standard Azure virtuella datorer aktiveras för säkerhetskopiering. Det här tillägget säkerhetskopierar hela VM. Du kan installera och köra MARS-agenten på en virtuell Azure-dator tillsammans med tillägget om du vill säkerhetskopiera specifika mappar och filer, i stället fullständig virtuell dator.
> När du kör MARS-agenten på en Azure VM, säkerhetskopierar filer/mappar finns i tillfällig lagring på den virtuella datorn. Säkerhetskopieringar misslyckas om filer/mappar tas bort från temporär lagring eller om tillfällig lagring har tagits bort.


## <a name="cache-folder-support"></a>Stöd för cache-mapp

När du säkerhetskopierar på med MARS-agenten kan agenten tar en ögonblicksbild av data och lagrar den i en mapp i lokalt cacheminne innan de skickas till Azure. Cachemappen (grunden) har ett antal krav.

**Cache** | **Detaljer**
--- | ---
**Cachestorlek** |  Ledigt utrymme på cache-mappstorleken ska vara minst 5 – 10% av den totala storleken på dina säkerhetskopierade data. 
**Cacheplats** | Cachelagringsmappen måste vara lokalt på datorn som säkerhetskopieras och måste vara online.<br/><br/> Cachemappen får inte finnas på en nätverksresurs, på flyttbara medier eller på en offline-volym. 
**Cache-mappen** | Cachemappen ska krypteras på en deduplicerad volym eller på en mapp som är komprimerade/null-optimerade /-referenspunkt
**Ändra cacheplatsen för** | Du kan ändra placeringen i cachen genom att stoppa säkerhetskopieringen (net stop bengine) och kopiera cachelagringsmappen till en ny enhet (se till att det finns tillräckligt med plats). Sedan du uppdaterar två registerposter under HKLM\SOFTWARE\Microsoft\Windows Azure Backup (Config/ScratchLocation och Config/CloudBackupProvider/ScratchLocation) till den nya platsen och starta om motorn.

## <a name="networking-and-access-support"></a>Stöd för nätverk och åtkomst

### <a name="url-access"></a>URL-åtkomst

MARS-agenten behöver åtkomst till dessa webbadresser:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="throttling-support"></a>Begränsning av support

**Funktion** | **Detaljer**
--- | ---
Bandbreddskontroll | Stöds<br/><br/> Använd **ändra egenskaper för** i MARS-agenten om du vill justera bandbredden.
Nätverksbegränsning | Inte tillgängligt för säkerhetskopierade datorer som kör Windows Server 2008 R2, Windows Server 2008 SP2 eller Windows 7.

## <a name="support-for-direct-backups"></a>Stöd för säkerhetskopior

I följande tabell sammanfattas vilka operativsystem som körs på lokala datorer och virtuella Azure-datorer kan säkerhetskopieras direkt till Azure med MARS-agenten.

- Alla operativsystem är 64-bitars.
- Alla operativsystem ska köras den senaste services Pack och uppdateringar.
- Mer information om vilka DPM och MABS servrar kan säkerhetskopieras med MARS-agenten finns [den här tabellen](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

**Operativsystem** | **Filer/mappar** | **Systemtillstånd**
--- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Ja | Nej
Windows 8.1 (Enterprise, Pro)| Ja |Nej
Windows 8 (Enterprise, Pro) | Ja | Nej
Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Ja | Nej
Windows Server 2016 (Standard, Datacenter, Essentials) | Ja | Ja
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Ja | Ja
Windows Server 2012 (Standard, Datacenter, Foundation) | Ja | Ja
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Ja | Ja
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | Ja | Nej
Windows Storage Server 2016/2012 R2/2012(Standard, Workgroup | Ja | Nej


## <a name="backup-limits"></a>Begränsningar för säkerhetskopiering

Azure Backup placerar en gräns på storleken på en fil/mapp-datakälla som kan säkerhetskopieras. Storleken på objekt som valts för säkerhetskopiering från en enskild volym får inte överskrida de storlekar som sammanfattas i tabellen.

**Operativsystem** | **Storleksgräns**
--- | ---
Windows Server 2012 eller senare |  54 400 GB
Windows Server 2008 R2 SP1 |    1 700 GB
Windows Server 2008 SP2 | 1 700 GB
Windows 8 eller senare  | 54 400 GB
Windows 7   | 1 700 GB


## <a name="supported-file-types-for-backup"></a>Filtyper som stöds för säkerhetskopiering

**Typ** | **Stöds** 
--- | --- 
Krypterade   | Ja 
Komprimerade | Ja
Utspridda | Ja 
Komprimerade och sparse-filer | Ja
Hårda länkar  | Stöds inte<br/><br/> Hoppades över
Referenspunkt   | Stöds inte<br/><br/> Hoppades över
Krypterade och utspridda |  Stöds inte<br/><br/> Hoppades över
Komprimerad dataström   | Stöds inte<br/><br/> Hoppades över
Utspridd ström   | Stöds inte<br/><br/> Hoppades över
En enhet (synkroniserad filer är glesa strömmar)    | Stöds inte 

## <a name="supported-drivesvolumes-for-backup"></a>Stöds enheter/volymer för säkerhetskopiering

**Enhet/volym** | **Stöds** | **Detaljer**
--- | --- | ---
Skrivskyddade volymer   | Stöds inte | Volymen måste vara skrivbar för VSS ska fungera.
Offlinevolymer | Stöds inte |   Volymen måste vara online för att VSS ska fungera.
Nätverksresurs   | Stöds inte |   Volymen måste vara lokal på servern för säkerhetskopiering.
BitLocker-skyddade volymer | Stöds inte |   Volymen måste låsas innan för säkerhetskopiering ska fungera.
Identifiering av filsystem  | Stöds inte |   NTFS endast.
Flyttbart medium | Stöds inte |   Alla källor för säkerhetskopieringsobjekt måste ha status för fast.
Deduplicerade enheter | Stöds.<br/><br/> Azure Backup konverterar deduplicerade data till vanliga data. Det den optimerar data, krypterar dem, lagrar och skickar den till valvet.

## <a name="support-for-initial-offline-backup"></a>Stöd för inledande säkerhetskopiering offline

Azure Backup stöder ”offlineseeding” överför första säkerhetskopierade informationen till Azure med hjälp av diskar. Det här är användbart om den första säkerhetskopieringen förmodas vara inom intervallet för terabyte (TB). Offlinesäkerhetskopiering stöds för:

- Direkt säkerhetskopiering av filer och mappar på lokala datorer som kör MARS-agenten.
- Säkerhetskopiering av arbetsbelastningar och filer från en DPM-servern eller MABS.
- Offlinesäkerhetskopiering kan inte användas för systemtillståndsfiler.


## <a name="support-for-restore"></a>Stöd för återställning

- Den nya [omedelbar återställning](backup-instant-restore-capability.md) versionen av Azure Backup kan du återställa data innan den har kopierats till valvet.<br/><br/> Du använder den här funktionen för datorn som säkerhetskopieras med .NET Framework 4.5.2 eller senare.
- Säkerhetskopieringar kan inte återställas till en måldator som kör en tidigare version av operativsystemet. Till exempel kan en säkerhetskopia som gjorts från en dator med Windows 7 återställas på Windows 8 eller senare. En säkerhetskopia som gjorts från en Windows 8-dator kan dock inte återställas till en dator med Windows 7.


## <a name="next-steps"></a>Nästa steg
- [Läs mer](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) om arkitektur för säkerhetskopiering med MARS-agenten.
- [Lär dig](backup-support-matrix-mabs-dpm.md) vad som stöds när du kör MARS-agenten på Microsoft Azure Backup Server (MABS) eller System Center DPM.


