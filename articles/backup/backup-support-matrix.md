---
title: Stödmatris för Azure Backup
description: Innehåller en sammanfattning av stödinställningar och begränsningar för Azure Backup-tjänsten.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: ff4ee1d88bd13e647d0f6218d7e9c9b2c57a5a01
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429578"
---
# <a name="azure-backup-support-matrix"></a>Stödmatris för Azure Backup

Med [Azure Backup-tjänsten](backup-overview.md) kan du säkerhetskopiera data till Microsoft Azure-molnet. Den här artikeln sammanfattas stöd för allmänna inställningar och begränsningar för Azure Backup-scenarier och distributioner.

Andra support-matriser är tillgängliga:

[Stödmatris](backup-support-matrix-iaas.md) för virtuell Azure-säkerhetskopiering [stödmatris](backup-support-matrix-mabs-dpm.md) för säkerhetskopiering med System Center DPM-/ Microsoft Azure Backup server (MABS) [stödmatris](backup-support-matrix-mars-agent.md) för säkerhetskopiering med Microsoft Azure Recovery Services MARS-agenten

## <a name="vault-support"></a>Stöd för valv

Azure Backup använder Recovery Services-valv för att dirigera och hantera säkerhetskopior och för att lagra säkerhetskopierade data.

**Inställning** | **Detaljer**
--- | ---
**Valv i prenumerationen** | Upp till 500 Recovery Services-valv i en enstaka prenumeration.
**Datorer i ett valv** | Upp till 1 000 virtuella Azure-datorer ett enstaka valv.<br/><br/> Upp till 50 MABS kan servrar registreras i ett enda valv.
**Datakällor i valvet lagring** | Maximal 54 400 GB. Det finns ingen gräns för säkerhetskopieringar av virtuella Azure-datorer.
**Säkerhetskopieringar till valvet** | Virtuella Azure-datorer: en gång om dagen<br/><br/>Datorer som skyddas av DPM/MABS: två gånger per dag<br/><br/> Datorer som säkerhetskopieras direkt med hjälp av MARS-agenten: tre gånger per dag. 
**Säkerhetskopior mellan valv** | Backup är inom en region.<br/><br/> Du behöver ett valv i varje Azure-region som innehåller virtuella datorer du vill säkerhetskopiera. Du kan inte säkerhetskopiera till en annan region. 
**Flytta valv** | Du kan [flytta valv](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) mellan prenumerationer eller mellan resursgrupper i samma prenumeration.
**Flytta data mellan valv** | Det finns inte stöd för att flytta säkerhetskopierade data mellan valv.
**Ändra typ av valvet** | Du kan ändra lagringsreplikeringstyp (GRS/LRS) för ett valv innan säkerhetskopior lagras. När säkerhetskopiering börjar i valvet går det inte att ändra replikeringstypen.



## <a name="on-premises-backup-support"></a>Stöd för lokal säkerhetskopiering

Det här är vad som stöds om du vill säkerhetskopiera lokala datorer.

**Dator** | **Säkerhetskopieras** | **Plats** | **Funktioner**
--- | --- | --- | ---
**Direkt säkerhetskopiering för Windows-dator med MARS-agenten** | Filer, mappar, systemtillstånd | Säkerhetskopiera till Recovery services-valv | Säkerhetskopiera tre gånger per dag.<br/><br/> Ingen appmedvetna säkerhetskopiering.<br/><br/> Återställ fil, mapp, volym.
**Direkt säkerhetskopiering för Linux-dator med MARS-agenten** | Säkerhetskopiering stöds inte.
**Säkerhetskopiera till DPM** | Filer, mappar, volymer, systemtillstånd, appdata. | Säkerhetskopiera till lokala DPM-lagring. DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/>. Oracle stöds inte.
**Tillbaka till MABS** | Filer, mappar, volymer, systemtillstånd, appdata. | Säkerhetskopiera till MABS lokal lagring. MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/>. Oracle stöds inte.


## <a name="azure-vm-backup-support"></a>Stöd för säkerhetskopiering av Azure VM

### <a name="azure-vm-limits"></a>Begränsningar för virtuella Azure-datorer

**Gräns** | **Detaljer**
--- | ---
Datadiskar för virtuella Azure-datorer | Gräns på 16.
Datadiskstorlek för virtuella Azure-datorer | Enskilda disk kan vara upp till 4 095 GB.


### <a name="azure-vm-backup-options"></a>Alternativ för säkerhetskopiering av virtuella Azure-datorer

Det här är vad som stöds om du vill säkerhetskopiera virtuella Azure-datorer.

**Dator** | **Säkerhetskopieras** | **Plats** | **Funktioner**
--- | --- | --- | ---
**Azure säkerhetskopiering av virtuella datorer med hjälp av VM-tillägg** | Hel virtuell dator | Säkerhetskopiering till valvet | Tillägget installeras när du aktiverar säkerhetskopiering för en virtuell dator.<br/><br/> Säkerhetskopieras en gång om dagen.<br/><br/> Appmedveten säkerhetskopiering för virtuella Windows-datorer, filkonsekvent säkerhetskopiering för virtuella Linux-datorer. Du kan konfigurera appkonsekvens för Linux-datorer med hjälp av anpassade skript.<br/><br/> Återställ virtuell dator/disk.<br/><br/> Det går inte att säkerhetskopiera på virtuella Azure-datorer till en lokal plats.
**Azure säkerhetskopiering av virtuella datorer med hjälp av MARS-agenten** | Filer, mappar | Säkerhetskopiering till valvet | Säkerhetskopiera tre gånger per dag.<br/><br/> MARS-agenten kan köras tillsammans med VM-tillägget om du vill säkerhetskopiera specifika filer/mappar i stället för hela VM.
**Virtuell Azure-dator med DPM** | Filer, mappar, volymer, systemtillstånd, appdata. | Säkerhetskopiera till lokal lagring för virtuell Azure-dator som kör DPM. DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/>. Oracle stöds inte.
**Virtuell Azure-dator med MABS** | Filer, mappar, volymer, systemtillstånd, appdata. | Säkerhetskopieras till lokal lagring för virtuell Azure-dator som kör MABS. MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.





## <a name="linux-backup-support"></a>Stöd för Linux-säkerhetskopiering

Det här är vad som stöds om du vill säkerhetskopiera Linux-datorer.

**Säkerhetskopiering** | **Linux (Azure-godkänt)**
--- | ---
**Direkt säkerhetskopiering av en lokal dator som kör Linux**. | Nej. MARS-agenten kan endast installeras på Windows-datorer.
**Säkerhetskopiera virtuella Azure-datorer som kör Linux (med tillägget agent)** | Appkonsekvent säkerhetskopiering med hjälp av [anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Återställning på filnivå.<br/><br/> Återställ genom att skapa en virtuell dator från en återställningspunkt eller disk.
**Säkerhetskopiera lokala eller virtuella Azure-datorer som kör Linux med hjälp av DPM** | Filkonsekvent säkerhetskopiering av virtuella Linux-gästdatorer på Hyper-V och VMWare<br/><br/> Återställning av virtuella Linux-gästdatorer på Hyper-V och VMWare</br></br> Filkonsekvent säkerhetskopiering är inte tillgängligt för virtuella Azure-datorer
**Säkerhetskopiera den lokala datorn/Azure virtuell dator som kör Linux med hjälp av MABS** | Filkonsekvent säkerhetskopiering av virtuella Linux-gästdatorer på Hyper-V och VMWare<br/><br/> Återställning av virtuella Linux-gästdatorer på Hyper-V och VMWare</br></br> Filkonsekvent säkerhetskopiering är inte tillgängligt för virtuella Azure-datorer.

## <a name="daylight-saving-support"></a>Stöd för sommartid

Azure Backup stöder inte automatisk klockan justering för sommartid ändringar för Virtuella Azure-säkerhetskopieringar. Ändra principer för säkerhetskopiering manuellt vid behov.


## <a name="disk-deduplication-support"></a>Stöd för deduplicering av disk

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

Säkerhetskopiering stöder komprimering av säkerhetskopieringstrafik, vilket sammanfattas i tabellen nedan. 

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure Storage-kontot över lagringsnätverket. Därför behöver du inte komprimera den här trafiken.
- Om du använder DPM eller MABS kan du komprimera data innan de säkerhetskopieras till DPM/MABS för att spara bandbredd.

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera (HTTPS) till valv**
--- | --- | ---
**Direkt säkerhetskopiering av lokala Windows-datorer** | Ej tillämpligt | Ja
**Säkerhetskopiera virtuella Azure-datorer med hjälp av VM-tillägg** | Ej tillämpligt | Ej tillämpligt
** Säkerhetskopiera på från lokalt/Azure-datorer med hjälp av MABS/DPM | ![Ja][green] | ![Ja][green]



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

- [Granska stöd matrix](backup-support-matrix-iaas.md) för virtuell Azure-säkerhetskopiering.


[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
