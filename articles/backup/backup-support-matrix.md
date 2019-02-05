---
title: Stödmatris för Azure Backup
description: Innehåller en sammanfattning av stödinställningar och begränsningar för Azure Backup-tjänsten.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cb3a60995a4edfe5eb00f1a5e88812146816806a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883712"
---
# <a name="azure-backup-support-matrix"></a>Stödmatris för Azure Backup

Med [Azure Backup-tjänsten](backup-overview.md) kan du säkerhetskopiera data till Microsoft Azure-molnet. I den här artikeln sammanfattas supportinställningar och begränsningar för Azure Backup-scenarier och distributioner.

## <a name="vault-support"></a>Stöd för valv

Azure Backup använder Recovery Services-valv för att dirigera och hantera säkerhetskopior och för att lagra säkerhetskopierade data.

**Inställning** | **Detaljer**
--- | ---
Antal valv | Upp till 500 Recovery Services-valv i en enstaka prenumeration.
Datorer i ett valv | Upp till 1 000 virtuella Azure-datorer ett enstaka valv.<br/><br/> Upp till 50 lokala kan datorer som kör Azure Backup-agenten (Microsoft Azure Recovery Services-agent (MABS)) registreras i ett enstaka valv.
Datakällan i valvlagring | Maximal 54 400 GB. Det finns ingen gräns för säkerhetskopieringar av virtuella Azure-datorer.
Säkerhetskopieringar till valv | Virtuella Azure-datorer: en gång per dag. Datorer som skyddas av DPM/MABS: två gånger om dagen. Datorer som säkerhetskopieras direkt med hjälp av MARS-agenten: tre gånger per dag.  
Flytta valv | Om du vill flytta ett Recovery Services-valv måste du registrera dig i en privat förhandsgranskning. Om du vill testa den kan du skriva till AskAzureBackupTeam@microsoft.com.
Flytta data mellan valv | Det finns inte stöd för att flytta säkerhetskopierade data mellan valv.
Lagringsreplikeringstyp | Du kan ändra lagringsreplikeringstyp (GRS/LRS) för ett valv innan säkerhetskopior lagras. När säkerhetskopiering börjar i valvet går det inte att ändra replikeringstypen.



## <a name="on-premises-backup-support"></a>Stöd för lokal säkerhetskopiering

Det här är vad som stöds om du vill säkerhetskopiera lokala datorer.

**Dator** | **Plats** | **Säkerhetskopiera** | **Funktioner**
--- | --- | --- | ---
**Fysisk/virtuell med Windows (ingen säkerhetskopieringsserver)** | Filer, mappar, systemtillstånd | Säkerhetskopieras till Recovery Services-valv | Säkerhetskopiera tre gånger per dag.<br/><br/> Ingen appmedvetna säkerhetskopiering.<br/><br/> Återställ fil, mapp, volym.
**Fysisk/virtuell med Linux (ingen säkerhetskopieringsserver)** | Säkerhetskopiering stöds inte.
**Fysisk/virtuell med DPM** | Filer, mappar, volymer, systemtillstånd, appdata. | Säkerhetskopieras till DPM (till en disk som är lokalt ansluten till DPM-servern eller till band.<br/><br/> DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/>. Oracle stöds inte.
**Fysisk virtuell med MABS** | Filer, mappar, volymer, systemtillstånd, appdata. | Säkerhetskopieras till MABS (till en disk som är lokalt ansluten till MABS-servern). Band stöds inte<br/><br/> MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/>. Oracle stöds inte.


## <a name="azure-vms"></a>Virtuella Azure-datorer

### <a name="azure-vm-limits"></a>Begränsningar för virtuella Azure-datorer

**Gräns** | **Detaljer**
--- | ---
Datadiskar för virtuella Azure-datorer | Gräns på 16.
Datadiskstorlek för virtuella Azure-datorer | Enskilda disk kan vara upp till 4 095 GB.


### <a name="azure-vm-backup-options"></a>Alternativ för säkerhetskopiering av virtuella Azure-datorer

Det här är vad som stöds om du vill säkerhetskopiera virtuella Azure-datorer.

**Dator** | **Plats** | **Säkerhetskopiera** | **Funktioner**
--- | --- | --- | ---
**Virtuella Azure-datorer (ingen säkerhetskopieringsserver)** | Filer, mappar, systemtillstånd | Säkerhetskopieras till valv. | Säkerhetskopieras en gång om dagen.<br/><br/> Appmedveten säkerhetskopiering för virtuella Windows-datorer, filkonsekvent säkerhetskopiering för virtuella Linux-datorer. Du kan konfigurera appkonsekvens för Linux-datorer med hjälp av anpassade skript.<br/><br/> Återställ virtuell dator/disk.<br/><br/> Det går inte att säkerhetskopiera på virtuella Azure-datorer till en lokal plats.
**Virtuell Azure-dator med DPM** | Filer, mappar, volymer, systemtillstånd, appdata. | Säkerhetskopieras till DPM som kör i Azure (till en disk som är lokalt ansluten till DPM-servern). Band stöds inte.<br/><br/> DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/>. Oracle stöds inte.
**Virtuell Azure-dator med MABS** | Filer, mappar, volymer, systemtillstånd, appdata. | Säkerhetskopieras till MABS som kör i Azure (till en disk som är lokalt ansluten till MABS-servern). Band stöds inte<br/><br/> MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/>. Oracle stöds inte.





## <a name="linux-backup-support"></a>Stöd för Linux-säkerhetskopiering

Det här är vad som stöds om du vill säkerhetskopiera Linux-datorer.

**Säkerhetskopiering** | **Linux (Azure-godkänt)**
--- | ---
**Lokal Linux-dator (utan DPM eller MABS)**. | Nej. MARS-agenten kan endast installeras på Windows-datorer.
**Virtuell Azure-dator (utan DPM eller MABS)** | Appkonsekvent säkerhetskopiering med hjälp av [anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Återställning på filnivå.<br/><br/> Återställ genom att skapa en virtuell dator från en återställningspunkt eller disk.
**Lokal dator/virtuell Azure-dator med DPM** | Filkonsekvent säkerhetskopiering av virtuella Linux-gästdatorer på Hyper-V och VMWare<br/><br/> Återställning av virtuella Linux-gästdatorer på Hyper-V och VMWare</br></br> Filkonsekvent säkerhetskopiering är inte tillgängligt för virtuella Azure-datorer
**Lokal dator/virtuell Azure-dator med MABS** | Filkonsekvent säkerhetskopiering av virtuella Linux-gästdatorer på Hyper-V och VMWare<br/><br/> Återställning av virtuella Linux-gästdatorer på Hyper-V och VMWare</br></br> Filkonsekvent säkerhetskopiering är inte tillgängligt för virtuella Azure-datorer.

## <a name="disk-support"></a>Stöd för diskar

Stöd för diskdeduplicering är följande:
- Diskdeduplicering stöds lokalt när du använder DPM eller MABS för att säkerhetskopiera virtuella Hyper-V-datorer som kör Windows. Windows Server utför datadeduplicering (på värdnivå) på virtuella hårddiskar (VHD) som är kopplade till den virtuella datorn som lagringsutrymme för säkerhetskopior.
- Deduplicering stöds inte i Azure för säkerhetskopieringskomponenter. Om System Center DPM och Backup Server distribueras i Azure går det inte att deduplicera de lagringsdiskar som är kopplade till den virtuella datorn.


## <a name="securityencryption-support"></a>Stöd för säkerhet/kryptering

Azure Backup stöder kryptering för data under överföring och i vila:

Nätverkstrafik till Azure:
- Säkerhetskopieringstrafik från servrar till Recovery Services-valvet krypteras med hjälp av Advanced Encryption Standard 256.
- Säkerhetskopierade data skickas via en säker HTTPS-anslutning.
- Säkerhetskopierade data lagras i Recovery Services-valvet i krypterad form.
- Endast du har tillgång till den lösenfras som krävs för att låsa upp dessa data. Microsoft kan aldrig dekryptera säkerhetskopierade data.
    > [!WARNING]
    > När du har konfigurerat valvet har bara du åtkomst till krypteringsnyckeln. Microsoft sparar aldrig någon kopia och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan Microsoft inte återställa dina säkerhetskopierade data.
Datasäkerhet:
- När du säkerhetskopierar virtuella Azure-datorer behöver du konfigurera kryptering *på* den virtuella datorn.
- Azure Backup stöder Azure Disk Encryption, som använder BitLocker på virtuella Windows-datorer och **dm-crypt** på virtuella Linux-datorer.
- På serverdelen använder Azure Backup [Azure Storage Service-kryptering](../storage/common/storage-service-encryption.md), som skyddar vilande data.


**Dator** | **Under överföring** | **I vila**
--- | --- | ---
Lokala Windows-datorer utan DPM/MABS | ![Ja][green] | ![Ja][green]
Virtuella Azure-datorer | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med DPM | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med MABS | ![Ja][green] | ![Ja][green]



## <a name="compression-support"></a>Stöd för komprimering

Säkerhetskopiering stöder komprimering av säkerhetskopieringstrafik, vilket sammanfattas i tabellen nedan. Tänk på följande:

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure Storage-kontot över lagringsnätverket. Därför behöver du inte komprimera den här trafiken.
- Om du använder DPM eller MABS kan du komprimera data innan de säkerhetskopieras till DPM/MABS för att spara bandbredd.

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera (HTTPS) till valv**
--- | --- | ---
Lokala Windows-datorer utan DPM/MABS | Ej tillämpligt | Ja
Virtuella Azure-datorer | Ej tillämpligt | Ej tillämpligt
Lokala/virtuella Azure-datorer med DPM | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med MABS | ![Ja][green] | ![Ja][green]



## <a name="retention-limits"></a>Gräns för kvarhållning

**Inställning** | **Begränsningar**
--- | ---
Högsta antal återställningspunkter per skyddad instans (dator/arbetsbelastning | 9999
Maximal förfallotid för en återställningspunkt | Obegränsad
Högsta säkerhetskopieringsfrekvens till DPM/MABS | Varje kvart för SQL Server<br/><br/> En gång per timme för andra arbetsbelastningar.
Högsta säkerhetskopieringsfrekvens till valv | Lokala Windows-datorer/virtuella Azure-datorer som kör MARS: tre per dag<br/><br/> DPM/MABS: Två per dag<br/><br/> Säkerhetskopiering av virtuell Azure-dator: En gång om dagen
Kvarhållning av återställningspunkt | Varje dag, varje vecka, varje månad, varje år.
Högsta kvarhållningsperiod | Beror på säkerhetskopieringsfrekvensen.
Återställningspunkter på DPM-/MABS-disk | 64 för filservrar, 448 för appservrar.<br/><br/> Bandåterställningspunkter är obegränsade för lokal DPM.

## <a name="next-steps"></a>Nästa steg

- [Säkerhetskopiera virtuella Azure-datorer](backup-azure-arm-vms-prepare.md)
- [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md) utan en reservserver.
- [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till MABS.
- [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
