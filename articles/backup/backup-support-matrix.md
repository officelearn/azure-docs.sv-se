---
title: Stödmatris för Azure Backup
description: Innehåller en sammanfattning av stödinställningar och begränsningar för Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: 37347e6febdfc3500c218238606fc96463da631c
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936236"
---
# <a name="support-matrix-for-azure-backup"></a>Support mat ris för Azure Backup

Du kan använda [Azure Backup](backup-overview.md) för att säkerhetskopiera data till Microsoft Azure moln plattform. Den här artikeln sammanfattar allmänna support inställningar och begränsningar för Azure Backup scenarier och distributioner.

Andra support-matriser är tillgängliga:

- Support mat ris för [säkerhets kopiering av virtuella Azure-datorer](backup-support-matrix-iaas.md)
- Support mat ris för säkerhets kopiering med [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (Mabs)](backup-support-matrix-mabs-dpm.md)
- Support mat ris för säkerhets kopiering med hjälp av [Microsoft Azure Recovery Services (mars)-agenten](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Stöd för valv

Azure Backup använder Recovery Services valv för att dirigera och hantera säkerhets kopior. Det använder också valv för att lagra säkerhetskopierade data.

I följande tabell beskrivs funktionerna i Recovery Services-valv:

**Funktion** | **Detaljer**
--- | ---
**Valv i prenumerationen** | Upp till 500 Recovery Services-valv i en enstaka prenumeration.
**Datorer i ett valv** | Upp till 1 000 virtuella Azure-datorer i ett enda valv.<br/><br/> Upp till 50 MABS-servrar kan registreras i ett enda valv.
**Data källor i valv lagring** | Maximalt 54 400 GB. Det finns ingen gräns för säkerhetskopieringar av virtuella Azure-datorer.
**Säkerhets kopieringar till valv** | **Virtuella Azure-datorer:** En gång om dagen.<br/><br/>**Datorer som skyddas av DPM/Mabs:** Två gånger om dagen.<br/><br/> **Datorer som har säkerhetskopierats direkt med hjälp av mars-agenten:** Tre gånger per dag.
**Säkerhets kopieringar mellan valv** | Säkerhets kopiering är inom en region.<br/><br/> Du behöver ett valv i varje Azure-region som innehåller de virtuella datorer som du vill säkerhetskopiera. Du kan inte säkerhetskopiera till en annan region.
**Flytta valv** | Du kan [Flytta valv](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) mellan prenumerationer eller mellan resurs grupper i samma prenumeration. Det finns dock inte stöd för att flytta valv mellan regioner.
**Flytta data mellan valv** | Det finns inte stöd för att flytta säkerhetskopierade data mellan valv.
**Ändra lagrings typ för valv** | Du kan ändra typen av lagrings replik (antingen Geo-redundant lagring eller lokalt redundant lagring) för ett valv innan säkerhets kopiorna lagras. När säkerhetskopiering börjar i valvet går det inte att ändra replikeringstypen.

## <a name="on-premises-backup-support"></a>Stöd för lokal säkerhetskopiering

Här är what's som stöds om du vill säkerhetskopiera lokala datorer:

**Dator** | **Vad har säkerhetskopierats** | **Plats** | **Funktioner**
--- | --- | --- | ---
**Direkt säkerhets kopiering av Windows-dator med MARS-agent** | Filer, mappar, systemtillstånd | Säkerhetskopiera till Recovery Services Vault. | Säkerhetskopiera tre gånger per dag<br/><br/> Ingen app-medveten säkerhets kopiering<br/><br/> Återställa fil, mapp, volym
**Direkt säkerhets kopiering av Linux-datorer med MARS-agent** | Säkerhets kopiering stöds inte
**Säkerhetskopiera till DPM** | Filer, mappar, volymer, system tillstånd, AppData | Säkerhetskopiera till den lokala DPM-lagringen. DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhets kopiering och återställning<br/><br/> Linux som stöds för virtuella datorer (Hyper-V/VMware)<br/><br/> Oracle stöds inte
**Säkerhetskopiera till MABS** | Filer, mappar, volymer, system tillstånd, AppData | Säkerhetskopiera till MABS lokal lagring. MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhets kopiering och återställning<br/><br/> Linux som stöds för virtuella datorer (Hyper-V/VMware)<br/><br/> Oracle stöds inte

## <a name="azure-vm-backup-support"></a>Support för Azure VM-säkerhetskopiering

### <a name="azure-vm-limits"></a>Begränsningar för virtuella Azure-datorer

**Gräns** | **Detaljer**
--- | ---
**Data diskar för virtuella Azure-datorer** | Gräns på 16 <br> För att registrera dig för den privata förhandsversionen av virtuella datorer med 16+ diskar (upp till 32 diskar) skriver du till oss på AskAzureBackupTeam@microsoft.com
**Data disk storlek för virtuell Azure-dator** | Enskild disk storlek kan vara upp till 32 TB och högst 256 TB kombinerat för alla diskar i en virtuell dator.

### <a name="azure-vm-backup-options"></a>Alternativ för säkerhetskopiering av virtuella Azure-datorer

Här är what's som stöds om du vill säkerhetskopiera virtuella Azure-datorer:

**Dator** | **Vad har säkerhetskopierats** | **Plats** | **Funktioner**
--- | --- | --- | ---
**VM-säkerhetskopiering i Azure med hjälp av VM-tillägget** | Hela VM | Säkerhetskopiera till valvet. | Tillägg som installeras när du aktiverar säkerhets kopiering för en virtuell dator.<br/><br/> Säkerhetskopiera en gång om dagen.<br/><br/> App-medveten säkerhets kopiering för virtuella Windows-datorer; filkonsekvent säkerhets kopiering för virtuella Linux-datorer. Du kan konfigurera program konsekvens för Linux-datorer med hjälp av anpassade skript.<br/><br/> Återställ virtuell dator eller disk.<br/><br/> Det går inte att säkerhetskopiera en virtuell Azure-dator till en lokal plats.
**Virtuell Azure VM-säkerhetskopiering med MARS-agenten** | Filer, mappar, systemtillstånd | Säkerhetskopiera till valvet. | Säkerhetskopiera tre gånger per dag.<br/><br/> Om du vill säkerhetskopiera vissa filer eller mappar i stället för hela den virtuella datorn, kan MARS-agenten köras tillsammans med VM-tillägget.
**Virtuell Azure-dator med DPM** | Filer, mappar, volymer, system tillstånd, AppData | Säkerhetskopiera till lokal lagring av virtuell Azure-dator som kör DPM. DPM säkerhetskopieras sedan till valvet. | App-medvetna ögonblicks bilder.<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.
**Virtuell Azure-dator med MABS** | Filer, mappar, volymer, system tillstånd, AppData | Säkerhetskopiera till lokal lagring av virtuell Azure-dator som kör MABS. MABS säkerhetskopierar sedan till valvet. | App-medvetna ögonblicks bilder.<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.

## <a name="linux-backup-support"></a>Stöd för Linux-säkerhetskopiering

Här är what's som stöds om du vill säkerhetskopiera Linux-datorer:

**Typ av säkerhets kopiering** | **Linux (Azure-godkänt)**
--- | ---
**Direkt säkerhets kopiering av lokal dator som kör Linux** | Stöds inte. MARS-agenten kan bara installeras på Windows-datorer.
**Använda agent tillägget för att säkerhetskopiera virtuell Azure-dator som kör Linux** | Programkonsekvent säkerhets kopiering med hjälp av [anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Återställning på filnivå.<br/><br/> Återställ genom att skapa en virtuell dator från en återställningspunkt eller disk.
**Använda DPM för att säkerhetskopiera lokalt eller virtuell Azure-dator som kör Linux** | Filkonsekvent säkerhets kopiering av virtuella Linux-gäst datorer i Hyper-V och VMWare.<br/><br/> VM-återställning av virtuella Hyper-V-och VMWare Linux-gäst datorer.<br/><br/> Filkonsekvent säkerhets kopiering är inte tillgänglig för virtuell Azure-dator.
**Använda MABS för att säkerhetskopiera en lokal dator eller virtuell Azure-dator som kör Linux** | Filkonsekvent säkerhets kopiering av virtuella Linux-gäst datorer i Hyper-V och VMWare.<br/><br/> VM-återställning av virtuella Hyper-V-och VMWare Linux-gäst datorer.<br/><br/> Filkonsekvent säkerhetskopiering är inte tillgängligt för virtuella Azure-datorer.

## <a name="daylight-saving-time-support"></a>Stöd för sommar tid

Azure Backup stöder inte automatisk klock justering för sommar tid för virtuella Azure-säkerhetskopieringar. Ändra säkerhets kopierings principer manuellt om det behövs.

## <a name="disk-deduplication-support"></a>Stöd för disk deduplicering

Stöd för diskdeduplicering är följande:

- Disk deduplicering stöds lokalt när du använder DPM eller MABs för att säkerhetskopiera virtuella Hyper-V-datorer som kör Windows. Windows Server utför datadeduplicering (på värdnivå) på virtuella hård diskar (VHD: er) som är kopplade till den virtuella datorn som säkerhets kopierings lagring.
- Deduplicering stöds inte i Azure för säkerhetskopieringskomponenter. När DPM och MABS distribueras i Azure går det inte att deduplicera lagrings diskarna som är anslutna till den virtuella datorn.

## <a name="security-and-encryption-support"></a>Säkerhet och krypterings stöd

Azure Backup stöder kryptering för överförings-och rest-data.

### <a name="network-traffic-to-azure"></a>Nätverks trafik till Azure

- Säkerhets kopierings trafik från servrar till Recovery Services-valvet krypteras med hjälp av Advanced Encryption Standard 256.
- Säkerhetskopierade data skickas via en säker HTTPS-anslutning.
- Säkerhetskopierade data lagras i Recovery Services valvet i krypterad form.
- Endast du har tillgång till den lösenfras som krävs för att låsa upp dessa data. Microsoft kan aldrig dekryptera säkerhetskopierade data.

    > [!WARNING]
    > När du har konfigurerat valvet har bara du åtkomst till krypteringsnyckeln. Microsoft sparar aldrig någon kopia och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan Microsoft inte återställa dina säkerhetskopierade data.

### <a name="data-security"></a>Datasäkerhet

- När du säkerhetskopierar virtuella Azure-datorer måste du konfigurera kryptering *i* den virtuella datorn.
- Azure Backup stöder Azure Disk Encryption, som använder BitLocker på virtuella Windows-datorer och **dm-crypt** på virtuella Linux-datorer.
- På Server sidan använder Azure Backup [Azure Storage tjänst kryptering](../storage/common/storage-service-encryption.md)som skyddar data i vila.

**Dator** | **Under överföring** | **I vila**
--- | --- | ---
**Lokala Windows-datorer utan DPM/MABS** | ![Ja][green] | ![Ja][green]
**Virtuella Azure-datorer** | ![Ja][green] | ![Ja][green]
**Lokala Windows-datorer eller virtuella Azure-datorer med DPM** | ![Ja][green] | ![Ja][green]
**Lokala Windows-datorer eller virtuella Azure-datorer med MABS** | ![Ja][green] | ![Ja][green]

## <a name="compression-support"></a>Stöd för komprimering

Säkerhets kopiering stöder komprimering av säkerhets kopierings trafik, som sammanfattas i följande tabell.

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure Storage-kontot över lagrings nätverket, så det är inte nödvändigt att komprimera den här trafiken.
- Om du använder DPM eller MABS kan du spara bandbredd genom att komprimera data innan de säkerhets kopie ras.

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera till valv (HTTPS)**
--- | --- | ---
**Direkt säkerhets kopiering av lokala Windows-datorer** | Ej tillämpligt | ![Ja][green]
**Säkerhets kopiering av virtuella Azure-datorer med hjälp av VM-tillägg** | Ej tillämpligt | Ej tillämpligt
**Säkerhetskopiera lokalt/Azure-datorer med hjälp av MABS/DPM** | ![Ja][green] | ![Ja][green]

## <a name="retention-limits"></a>Gräns för kvarhållning

**Inställning** | **Begränsningar**
--- | ---
**Högsta antal återställnings punkter per skyddad instans (dator eller arbets belastning)** | 9 999
**Maximal förfallo tid för en återställnings punkt** | Obegränsat
**Maximal säkerhets kopierings frekvens till DPM/MABS** | Varje kvart för SQL Server<br/><br/> En gång i timmen för andra arbets belastningar
**Maximal säkerhets kopierings frekvens till valv** | **Lokala Windows-datorer eller virtuella Azure-datorer som kör mars:** Tre per dag<br/><br/> **DPM/Mabs:** Två per dag<br/><br/> **Azure VM-säkerhetskopiering:** En per dag
**Kvarhållning av återställnings punkt** | Varje dag, varje vecka, varje månad, varje år
**Maximal kvarhållningsperiod** | Beror på säkerhetskopieringsfrekvensen
**Återställnings punkter på DPM/MABS disk** | 64 för fil servrar; 448 för App-servrar <br/><br/>Obegränsade band återställnings punkter för lokal DPM

## <a name="cross-region-restore"></a>Återställning mellan regioner

Azure Backup har lagt till funktionen för återställning av kors region för att förstärka data tillgänglighet och återhämtnings kapacitet, vilket ger kunderna fullständig kontroll över att återställa data till en sekundär region. Om du vill konfigurera den här funktionen går [du till artikeln ange återställning mellan regioner.](backup-create-rs-vault.md#set-cross-region-restore).. Den här funktionen stöds för följande hanterings typer:

| Typ av säkerhets kopierings hantering | Stöds                                                    | Regioner som stöds |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Ja. Offentlig begränsad för hands version som stöds för krypterade virtuella datorer och virtuella datorer med mindre än 4 TB diskar | USA, västra centrala   |
| MARS-agent/lokalt | Inga                                                           | Gäller inte               |
| SQL-/SAP HANA          | Inga                                                           | Gäller inte               |
| AFS                    | Inga                                                           | Gäller inte               |



## <a name="next-steps"></a>Nästa steg

- [Granska support mat ris](backup-support-matrix-iaas.md) för säkerhets kopiering av virtuella Azure-datorer.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
