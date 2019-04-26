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
ms.openlocfilehash: 51bd4b935b32bea20d3f5de0b8cda62dfdbf07b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236629"
---
# <a name="azure-backup-support-matrix"></a>Stödmatris för Azure Backup

Du kan använda [Azure Backup](backup-overview.md) att säkerhetskopiera data till Microsoft Azure-molnplattformen. Den här artikeln sammanfattas de stöd för allmänna inställningar och begränsningar för Azure Backup-scenarier och distributioner.

Andra support-matriser är tillgängliga:

- Stöd matrix för [säkerhetskopiering av Azure virtuella datorer (VM)](backup-support-matrix-iaas.md)
- Stödmatris för säkerhetskopiering med hjälp av [System Center Data Protection Manager (DPM) / Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Stödmatris för säkerhetskopiering med hjälp av den [Microsoft Azure Recovery Services MARS-agenten](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>Stöd för valv

Azure Backup använder Recovery Services-valv för att dirigera och hantera säkerhetskopior. Den använder även valv för att lagra säkerhetskopierade data. 

I följande tabell beskrivs funktioner i Recovery Services-valv:

**Funktion** | **Detaljer**
--- | ---
**Valv i prenumerationen** | Upp till 500 Recovery Services-valv i en enstaka prenumeration.
**Datorer i ett valv** | Upp till 1 000 virtuella datorer i Azure i ett enda valv.<br/><br/> Upp till 50 MABS kan servrar registreras i ett enda valv.
**Datakällor i valvet lagring** | Maximal 54,400 GB. Det finns ingen gräns för säkerhetskopieringar av virtuella Azure-datorer.
**Säkerhetskopieringar till valvet** | **Virtuella Azure-datorer:** En gång om dagen.<br/><br/>**Datorer som skyddas av DPM/MABS:** Två gånger per dag.<br/><br/> **Datorer som säkerhetskopieras direkt med hjälp av MARS-agenten:** Tre gånger per dag. 
**Säkerhetskopior mellan valv** | Backup är inom en region.<br/><br/> Du behöver ett valv i varje Azure-region som innehåller virtuella datorer du vill säkerhetskopiera. Du kan inte säkerhetskopiera till en annan region. 
**Flytta valv** | Du kan [flytta valv](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) mellan prenumerationer eller mellan resursgrupper i samma prenumeration.
**Flytta data mellan valv** | Flytta säkerhetskopierade data mellan valv stöds inte.
**Ändra typ av valvet** | Du kan ändra lagringsreplikeringstyp (geo-redundant lagring eller lokalt redundant lagring) för ett valv innan säkerhetskopior lagras. När säkerhetskopiering börjar i valvet går det inte att ändra replikeringstypen.

## <a name="on-premises-backup-support"></a>Stöd för lokal säkerhetskopiering

Här är vad som stöds om du vill säkerhetskopiera lokala datorer:

**Dator** | **Vad som säkerhetskopieras** | **Plats** | **Funktioner**
--- | --- | --- | ---
**Direkt säkerhetskopiering för Windows-dator med MARS-agenten** | Filer, mappar, systemtillstånd | Säkerhetskopiera till Recovery Services-valvet. | Säkerhetskopiera tre gånger per dag<br/><br/> Inga app-medvetna säkerhetskopiering<br/><br/> Återställa filer, mappar, volymer
**Direkt säkerhetskopiering för Linux-dator med MARS-agenten** | Säkerhetskopiering stöds inte
**Säkerhetskopiera till DPM** | Filer, mappar, volymer, systemtillståndet eller AppData | Säkerhetskopiera till lokala DPM-lagring. DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning<br/><br/> Linux som stöds för virtuella datorer (Hyper-V/VMware)<br/><br/> Oracle stöds inte
**Säkerhetskopiera till MABS** | Filer, mappar, volymer, systemtillståndet eller AppData | Säkerhetskopiera till MABS lokal lagring. MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning<br/><br/> Linux som stöds för virtuella datorer (Hyper-V/VMware)<br/><br/> Oracle stöds inte

## <a name="azure-vm-backup-support"></a>Stöd för säkerhetskopiering av Azure VM

### <a name="azure-vm-limits"></a>Begränsningar för virtuella Azure-datorer

**Gräns** | **Detaljer**
--- | ---
**Azure VM-datadiskar** | Gränsen på 16
**Azure VM-datadiskstorleken** | Individuella kan vara upp till 4095 GB

### <a name="azure-vm-backup-options"></a>Alternativ för säkerhetskopiering av virtuella Azure-datorer

Här är vad som stöds om du vill säkerhetskopiera virtuella Azure-datorer:

**Dator** | **Vad som säkerhetskopieras** | **Plats** | **Funktioner**
--- | --- | --- | ---
**Azure säkerhetskopiering av virtuella datorer med hjälp av VM-tillägg** | Hel virtuell dator | Säkerhetskopiera till valvet. | Tillägget installeras när du aktiverar säkerhetskopiering för en virtuell dator.<br/><br/> Säkerhetskopiera en gång om dagen.<br/><br/> App-medvetna säkerhetskopiering för Windows-datorer filkonsekvent säkerhetskopiering för virtuella Linux-datorer. Du kan konfigurera app-konsekvens för Linux-datorer med hjälp av anpassade skript.<br/><br/> Återställ virtuell dator eller disk.<br/><br/> Det går inte att säkerhetskopiera en Azure-dator till en lokal plats.
**Azure säkerhetskopiering av virtuella datorer med hjälp av MARS-agenten** | Filer, mappar | Säkerhetskopiera till valvet. | Säkerhetskopiera tre gånger per dag.<br/><br/> Om du vill säkerhetskopiera specifika filer eller mappar i stället för hela VM kan MARS-agenten köra tillsammans med VM-tillägget.
**Virtuell Azure-dator med DPM** | Filer, mappar, volymer, systemtillståndet eller AppData | Säkerhetskopiera till lokal lagring på Azure virtuella datorer som kör DPM. DPM säkerhetskopieras sedan till valvet. | App-medvetna ögonblicksbilder.<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.
**Virtuell Azure-dator med MABS** | Filer, mappar, volymer, systemtillståndet eller AppData | Säkerhetskopiera till lokal lagring på Azure virtuella datorer som kör MABS. MABS säkerhetskopierar sedan till valvet. | App-medvetna ögonblicksbilder.<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.

## <a name="linux-backup-support"></a>Stöd för Linux-säkerhetskopiering

Här är vad som stöds om du vill säkerhetskopiera Linux-datorer:

**Typ av säkerhetskopiering** | **Linux (Azure-godkänt)**
--- | ---
**Direkt säkerhetskopiering för den lokala datorn som kör Linux** | Stöds ej. MARS-agenten kan installeras endast på Windows-datorer.
**Säkerhetskopiera virtuella Azure-datorer som kör Linux med agent-tillägget** | Programkonsekvent säkerhetskopiering med hjälp av [anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Återställning på filnivå.<br/><br/> Återställ genom att skapa en virtuell dator från en återställningspunkt eller disk.
**Med hjälp av DPM för att säkerhetskopiera lokala eller virtuella Azure-datorer som kör Linux** | Filkonsekvent säkerhetskopiering av virtuella Linux-gästdatorer på Hyper-V och VMWare.<br/><br/> Återställning av virtuell dator för Hyper-V och VMWare Linux-gästdatorer.<br/><br/> Filkonsekvent säkerhetskopiering är inte tillgängligt för virtuella Azure-datorer.
**Säkerhetskopiera en lokal dator eller virtuell Azure-dator som kör Linux med MABS** | Filkonsekvent säkerhetskopiering av virtuella Linux-gästdatorer på Hyper-V och VMWare.<br/><br/> Återställning av virtuell dator för Hyper-V och VMWare Linux-gästdatorer.<br/><br/> Filkonsekvent säkerhetskopiering är inte tillgängligt för virtuella Azure-datorer.

## <a name="daylight-saving-time-support"></a>Stöd för sommartid

Azure Backup stöder inte automatisk klockan justering för sommartid för Virtuella Azure-säkerhetskopieringar. Ändra principer för säkerhetskopiering manuellt vid behov.

## <a name="disk-deduplication-support"></a>Stöd för deduplicering av disk

Stöd för diskdeduplicering är följande:

- Diskdeduplicering är lokal när du använder DPM- eller MABs för säkerhetskopiering av Hyper-V-datorer som kör Windows. Windows Server utför datadeduplicering (på värdnivå) på virtuella hårddiskar (VHD) som är kopplade till den virtuella datorn som lagringsutrymme för säkerhetskopior.
- Deduplicering stöds inte i Azure för säkerhetskopieringskomponenter. När DPM och MABS distribueras i Azure, går inte att deduplicera lagringsdiskarna som är kopplade till den virtuella datorn.

## <a name="security-and-encryption-support"></a>Stöd för säkerhet och kryptering

Azure Backup stöder kryptering för data under överföring och i vila.

### <a name="network-traffic-to-azure"></a>Trafik till Azure

- Säkerhetskopieringstrafik från servrar till Recovery Services-valvet krypteras med hjälp av Advanced Encryption Standard 256.
- Säkerhetskopierade data skickas via en säker HTTPS-anslutning.
- Säkerhetskopierade data lagras i Recovery Services-valvet i krypterad form.
- Endast du har tillgång till den lösenfras som krävs för att låsa upp dessa data. Microsoft kan aldrig dekryptera säkerhetskopierade data.

    > [!WARNING]
    > När du har konfigurerat valvet har bara du åtkomst till krypteringsnyckeln. Microsoft sparar aldrig någon kopia och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan Microsoft inte återställa dina säkerhetskopierade data.

### <a name="data-security"></a>Datasäkerhet

- När du säkerhetskopierar virtuella Azure-datorer, måste du ställa in kryptering *inom* den virtuella datorn.
- Azure Backup stöder Azure Disk Encryption, som använder BitLocker på virtuella Windows-datorer och **dm-crypt** på virtuella Linux-datorer.
- På serverdelen, Azure Backup använder [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md), som skyddar data i vila.

**Dator** | **Under överföring** | **I vila**
--- | --- | ---
**Lokala Windows-datorer utan DPM/MABS** | ![Ja][green] | ![Ja][green]
**Virtuella Azure-datorer** | ![Ja][green] | ![Ja][green]
**Lokala Windows-datorer eller virtuella Azure-datorer med DPM** | ![Ja][green] | ![Ja][green]
**Lokala Windows-datorer eller virtuella Azure-datorer med MABS** | ![Ja][green] | ![Ja][green]

## <a name="compression-support"></a>Stöd för komprimering

Säkerhetskopiering stöder komprimering av säkerhetskopieringstrafik, som sammanfattas i tabellen nedan.

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure storage-kontot i lagringsnätverket, så det är inte nödvändigt att komprimera den här trafiken.
- Om du använder DPM- eller MABS, kan du spara bandbredd genom att komprimera data innan den säkerhetskopieras.

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera till valv (HTTPS)**
--- | --- | ---
**Direkt säkerhetskopiering av lokala Windows-datorer** | Ej tillämpligt | ![Ja][green]
**Säkerhetskopiering av virtuella Azure-datorer med hjälp av VM-tillägg** | Ej tillämpligt | Ej tillämpligt
**Säkerhetskopieringen på från lokalt/Azure-datorer med hjälp av MABS/DPM** | ![Ja][green] | ![Ja][green]

## <a name="retention-limits"></a>Gräns för kvarhållning

**Inställning** | **Begränsningar**
--- | ---
**Högsta antal återställningspunkter per skyddad instans (dator eller en arbetsbelastning)** | 9,999
**Max förfallotiden för en återställningspunkt** | Obegränsad
**Högsta säkerhetskopieringsfrekvensen till DPM/MABS** | Varje kvart för SQL Server<br/><br/> En gång i timmen för andra arbetsbelastningar
**Högsta säkerhetskopieringsfrekvensen till valvet** | **Lokal Windows-datorer eller virtuella Azure-datorer som kör MARS:** Tre per dag<br/><br/> **DPM/MABS:** Två per dag<br/><br/> **Azure VM-säkerhetskopiering:** En per dag
**Kvarhållning av återställningspunkt** | Varje dag, varje vecka, varje månad, varje år
**Högsta kvarhållningsperiod** | Beror på säkerhetskopieringsfrekvensen
**Återställningspunkter på DPM/MABS-disk** | 64 för filservrar; 448 för app-servrar <br/><br/>Obegränsade bandåterställningspunkter för den lokala DPM

## <a name="next-steps"></a>Nästa steg

- [Granska stöd matrix](backup-support-matrix-iaas.md) för virtuell Azure-säkerhetskopiering.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
