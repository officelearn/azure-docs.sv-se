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
ms.openlocfilehash: 3e2c6a550a9358656fd0870c7e785d131c5b6380
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894401"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Stödmatris för säkerhetskopiering med Microsoft Azure Recovery Services MARS-agenten

Du kan använda den [Azure Backup-tjänsten](backup-overview.md) för säkerhetskopiering av lokala datorer och appar och att säkerhetskopiera virtuella Azure-datorer (VM). Den här artikeln sammanfattas support inställningar och begränsningar när du använder Microsoft Azure Recovery Services MARS-agenten för att säkerhetskopiera virtuella datorer.

## <a name="the-mars-agent"></a>MARS-agenten

Azure Backup använder MARS-agenten för att säkerhetskopiera data från lokala datorer och virtuella Azure-datorer till säkerhetskopieringsvalvet i Azure Recovery Services. MARS-agenten kan:
- Köra på den lokala Windows-datorer så att de kan säkerhetskopiera direkt till ett säkerhetskopieringsvalv i Azure Recovery Services.
- Kör på virtuella Windows-datorer så att de kan säkerhetskopiera direkt till ett valv.
- Kör på Microsoft Azure Backup Server (MABS) eller en server för System Center Data Protection Manager (DPM). I det här scenariot säkerhetskopiera datorer och arbetsbelastningar MABS eller DPM-server. MARS-agenten säkerhetskopierar sedan på den här servern till ett valv i Azure. 

Alternativ för säkerhetskopiering beror på där agenten är installerad. Mer information finns i [Azure Backup-arkitektur med MARS-agenten](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Information om MABS och DPM backup-arkitekturen finns i [säkerhetskopiera till DPM- eller MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Se även [krav](backup-support-matrix-mabs-dpm.md) för arkitektur för säkerhetskopiering.

**Installation** | **Detaljer**
--- | ---
Hämta den senaste MARS-agenten | Du kan hämta den senaste versionen av agenten från valvet eller [ladda ned den direkt](https://aka.ms/azurebackup_agent).
Installera direkt på en dator | Du kan installera MARS-agenten direkt på en lokal Windows server eller på en Windows-dator som kör någon av de [operativsystem som stöds](https://docs.microsoft.com/en-us/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Installera på en sekundär server | När du ställer in DPM- eller MABS att säkerhetskopiera till Azure, hämta och installera MARS-agenten på servern. Du kan installera agenten på [operativsystem som stöds](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) i matrisen säkerhetskopieringsserver support.

> [!NOTE]
> Som standard har en Azure Backup-tillägget-installation av Azure virtuella datorer som är aktiverade för säkerhetskopiering. Det här tillägget säkerhetskopierar hela VM. Du kan installera och köra MARS-agenten på en virtuell Azure-dator tillsammans med tillägget om du vill säkerhetskopiera specifika mappar och filer, i stället fullständig virtuell dator.
> När du kör MARS-agenten på en Azure-dator kan säkerhetskopierar den filer eller mappar som finns i tillfällig lagring på den virtuella datorn. Säkerhetskopieringen misslyckas om de filer och mappar tas bort från temporär lagring eller temporär lagring tas bort.


## <a name="cache-folder-support"></a>Stöd för cache-mapp

När du använder MARS-agenten för säkerhetskopiering av data kan agenten tar en ögonblicksbild av data och lagrar den i en mapp i lokalt cacheminne innan den skickar data till Azure. Cachemappen (grunden) har flera krav:

**Cache** | **Detaljer**
--- | ---
Storlek |  Ledigt utrymme i cachemappen bör vara minst 5 – 10 procent av den totala storleken på dina säkerhetskopierade data. 
Plats | Cachemappen måste lagras lokalt på datorn som säkerhetskopieras och det måste vara online. Cachemappen får inte vara på en nätverksresurs, på flyttbara medier eller på en offline-volym. 
Mapp | Cachemappen ska krypteras på en deduplicerad volym eller i en mapp som är komprimerade, som är gles eller som har en referenspunkt.
Platsförändringar av | Du kan ändra placeringen i cachen genom att stoppa backup-motorn (`net stop bengine`) och kopierar cachelagringsmappen till en ny enhet. (Se till att den nya enheten har tillräckligt med utrymme.) Uppdatera sedan två registerposter under **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** och **Config/CloudBackupProvider/ScratchLocation**) till den nya platsen och starta om motorn.

## <a name="networking-and-access-support"></a>Stöd för nätverk och åtkomst

### <a name="url-access"></a>URL-åtkomst

MARS-agenten behöver åtkomst till dessa webbadresser:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Begränsning av support

**Funktion** | **Detaljer**
--- | ---
Bandbreddskontroll | Stöds. I MARS-agenten använder **ändra egenskaper för** vill justera bandbredden.
Nätverksbegränsning | Inte tillgängligt för säkerhetskopierade virtuella datorer som kör Windows Server 2008 R2, Windows Server 2008 SP2 eller Windows 7.

## <a name="support-for-direct-backups"></a>Stöd för säkerhetskopior

Du kan använda MARS-agenten för att säkerhetskopiera direkt till Azure på vissa operativsystem som körs på lokala datorer och virtuella Azure-datorer. Operativsystemen måste vara 64-bitars och bör vara som kör den senaste services Pack och uppdateringar. I följande tabell sammanfattas dessa operativsystem:

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
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Ja | Nej

Mer information finns i [stöds MABS- och DPM operativsystem](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Begränsningar för säkerhetskopiering

Azure Backup begränsar storleken på en fil eller mapp datakälla som kan säkerhetskopieras. De objekt som du säkerhetskopiera från en enskild volym får inte överskrida de storlekar som sammanfattas i den här tabellen:

**Operativsystem** | **Storleksgräns**
--- | ---
Windows Server 2012 eller senare |  54 400 GB
Windows Server 2008 R2 SP1 |    1,700 GB
Windows Server 2008 SP2 | 1,700 GB
Windows 8 eller senare  | 54 400 GB
Windows 7   | 1,700 GB


## <a name="supported-file-types-for-backup"></a>Filtyper som stöds för säkerhetskopiering

**Typ** | **Support** 
--- | --- 
Krypterade   | Stöds. 
Komprimerade | Stöds.
Utspridda | Stöds.
Komprimerade och sparse-filer | Stöds.
Hårda länkar  | Stöds ej. Hoppades över.
Referenspunkt   | Stöds ej. Hoppades över.
Krypterade och utspridda |  Stöds ej. Hoppades över.
Komprimerad dataström   | Stöds ej. Hoppades över.
Utspridd ström   | Stöds ej. Hoppades över.
OneDrive (synkroniserade filer är glesa strömmar)  | Stöds ej. 

## <a name="supported-drives-or-volumes-for-backup"></a>De enheter som stöds eller volymer för säkerhetskopiering

**Enhet/volym** | **Support** | **Detaljer**
--- | --- | ---
Skrivskyddade volymer   | Stöds inte | Volume Copy Shadow Service (VSS) fungerar bara om volymen är skrivskyddad.
Offlinevolymer | Stöds inte |   VSS fungerar bara om volymen är online.
Nätverksresurs   | Stöds inte |   Volymen måste vara lokal på servern.
BitLocker-skyddade volymer | Stöds inte |   Volymen måste vara upplåst innan säkerhetskopieringen startar.
Identifiering av filsystem  | Stöds inte |   Endast NTFS stöds.
Flyttbart medium | Stöds inte |   Alla källor för säkerhetskopieringsobjekt måste ha en *fast* status.
Deduplicerade enheter | Stöds | Azure Backup konverterar deduplicerade data till vanliga data. Det optimerar, krypterar, lagrar och skickar data till valvet.

## <a name="support-for-initial-offline-backup"></a>Stöd för inledande säkerhetskopiering offline

Azure Backup stöder *seeding offline* överföra första säkerhetskopierade informationen till Azure med hjälp av diskar. Det här stödet är användbar om den första säkerhetskopieringen troligen kommer att vara i intervallet storlek terabyte (TB). Offlinesäkerhetskopiering stöds för:

- Direkt säkerhetskopiering av filer och mappar på lokala datorer som kör MARS-agenten.
- Säkerhetskopiering av arbetsbelastningar och filer från en DPM-servern eller MABS.

Offlinesäkerhetskopiering kan inte användas för systemtillståndsfiler.

## <a name="support-for-data-restoration"></a>Stöd för återställning av data

Med hjälp av den [omedelbar återställning](backup-instant-restore-capability.md) funktionen Azure Backup kan du återställa data innan den kopieras till valvet. Den dator som du säkerhetskopierar måste köra .NET Framework 4.5.2 eller senare.

Säkerhetskopieringar kan inte återställas till en måldator som kör en tidigare version av operativsystemet. Till exempel kan en säkerhetskopia som gjorts från en dator som kör Windows 7 återställas på Windows 8 eller senare. Men det går inte att återställa en säkerhetskopia som gjorts från en dator som kör Windows 8 på en dator som kör Windows 7.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [arkitektur som använder MARS-agenten för säkerhetskopiering](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Lär dig vad har stöds när du [köra MARS-agenten på MABS eller en DPM-server](backup-support-matrix-mabs-dpm.md).
