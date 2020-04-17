---
title: Stödmatris för Azure Backup
description: Innehåller en sammanfattning av stödinställningar och begränsningar för Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: b87430c9ff7d806fd1fc74d5d2a8270f9db43b53
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537329"
---
# <a name="support-matrix-for-azure-backup"></a>Supportmatris för Azure Backup

Du kan använda [Azure Backup](backup-overview.md) för att säkerhetskopiera data till Microsoft Azure-molnplattformen. Den här artikeln sammanfattar allmänna supportinställningar och begränsningar för Scenarier och distributioner för Azure Backup.

Andra supportmatriser finns tillgängliga:

- Stödmatris för säkerhetskopiering av [virtuella Azure-datorer](backup-support-matrix-iaas.md)
- Stödmatris för säkerhetskopiering med hjälp av [DPM (System Center Data Protection Manager)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Stödmatris för säkerhetskopiering med hjälp av [MARS-agenten (Microsoft Azure Recovery Services)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Stöd för valv

Azure Backup använder Recovery Services-valv för att dirigera och hantera säkerhetskopior. Den använder också valv för att lagra säkerhetskopierade data.

I följande tabell beskrivs funktionerna i Recovery Services-valv:

**Funktion** | **Detaljer**
--- | ---
**Valv i prenumeration** | Upp till 500 Recovery Services-valv i en enstaka prenumeration.
**Datorer i ett valv** | Upp till 1 000 virtuella Azure-datorer i ett enda valv.<br/><br/> Upp till 50 MABS-servrar kan registreras i ett enda valv.
**Datakällor** | Maximal storlek för en enskild [datakälla](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) är 54 400 GB. Den här gränsen gäller inte för Azure VM-säkerhetskopior. Inga gränser gäller för den totala mängden data som du kan säkerhetskopiera till valvet.
**Säkerhetskopieringar till valv** | **Virtuella Azure-datorer:** En gång om dagen.<br/><br/>**Maskiner som skyddas av DPM/MABS:** Två gånger om dagen.<br/><br/> **Maskiner som säkerhetskopieras direkt med marsagenten:** Tre gånger om dagen.
**Säkerhetskopior mellan valv** | Säkerhetskopieringen finns inom en region.<br/><br/> Du behöver ett valv i alla Azure-regioner som innehåller virtuella datorer som du vill säkerhetskopiera. Du kan inte säkerhetskopiera till en annan region.
**Flytta valv** | Du kan [flytta valv](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) mellan prenumerationer eller mellan resursgrupper i samma prenumeration. Det går dock inte att flytta valv mellan regioner.
**Flytta data mellan valv** | Det går inte att flytta säkerhetskopierade data mellan valv.
**Ändra lagringstyp för arkiv** | Du kan ändra lagringsreplikeringstypen (antingen geo-redundant lagring eller lokalt redundant lagring) för ett valv innan säkerhetskopior lagras. När säkerhetskopiering börjar i valvet går det inte att ändra replikeringstypen.

## <a name="on-premises-backup-support"></a>Stöd för lokal säkerhetskopiering

Det här är vad som stöds om du vill säkerhetskopiera lokala datorer:

**Dator** | **Vad är säkerhetskopierat** | **Location** | **Funktioner**
--- | --- | --- | ---
**Direkt säkerhetskopiering av Windows-dator med MARS-agent** | Filer, mappar, systemtillstånd | Säkerhetskopiera till Recovery Services-valvet. | Säkerhetskopiera tre gånger om dagen<br/><br/> Ingen appmedveten säkerhetskopiering<br/><br/> Återställa fil, mapp, volym
**Direkt säkerhetskopiering av Linux-maskin med MARS-agent** | Säkerhetskopiering stöds inte
**Säkerhetskopiera till DPM** | Filer, mappar, volymer, systemtillstånd, appdata | Säkerhetskopiera till lokal DPM-lagring. DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware)<br/><br/> Oracle stöds inte
**Säkerhetskopiera till MABS** | Filer, mappar, volymer, systemtillstånd, appdata | Säkerhetskopiera till MABS lokal lagring. MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware)<br/><br/> Oracle stöds inte

## <a name="azure-vm-backup-support"></a>Stöd för säkerhetskopiering av Azure VM

### <a name="azure-vm-limits"></a>Begränsningar för virtuella Azure-datorer

**Gräns** | **Detaljer**
--- | ---
**Datadiskar för virtuella Azure-datorer** | Gräns på 16 <br> För att registrera dig för den privata förhandsversionen av virtuella datorer med 16+ diskar (upp till 32 diskar) skriver du till oss på AskAzureBackupTeam@microsoft.com
**Datadiskstorlek för virtuella Azure-datorer** | Individuell diskstorlek kan vara upp till 32 TB och högst 256 TB kombinerat för alla diskar i en virtuell dator.

### <a name="azure-vm-backup-options"></a>Alternativ för säkerhetskopiering av virtuella Azure-datorer

Det här är vad som stöds om du vill säkerhetskopiera virtuella Azure-datorer:

**Dator** | **Vad är säkerhetskopierat** | **Location** | **Funktioner**
--- | --- | --- | ---
**Azure VM-säkerhetskopiering med hjälp av VM-tillägg** | Hela den virtuella datorn | Tillbaka till valvet. | Tillägg installeras när du aktiverar säkerhetskopiering för en virtuell dator.<br/><br/> Backa en gång om dagen.<br/><br/> Appmedveten säkerhetskopiering för virtuella Windows-datorer. filkonsekvent säkerhetskopiering för virtuella Linux-datorer. Du kan konfigurera appkonsekvens för Linux-datorer med hjälp av anpassade skript.<br/><br/> Återställ virtuell dator eller disk.<br/><br/> Det går inte att säkerhetskopiera en virtuell Azure-dator till en lokal plats.
**Azure VM-säkerhetskopiering med marsagent** | Filer, mappar, systemtillstånd | Tillbaka till valvet. | Backa tre gånger om dagen.<br/><br/> Om du vill säkerhetskopiera specifika filer eller mappar i stället för hela den virtuella datorn kan MARS-agenten köras tillsammans med vm-tillägget.
**Virtuell Azure-dator med DPM** | Filer, mappar, volymer, systemtillstånd, appdata | Säkerhetskopiera till lokal lagring av Azure VM som kör DPM. DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder.<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.
**Virtuell Azure-dator med MABS** | Filer, mappar, volymer, systemtillstånd, appdata | Säkerhetskopiera till lokal lagring av Azure VM som kör MABS. MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder.<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.

## <a name="linux-backup-support"></a>Stöd för Linux-säkerhetskopiering

Det här är vad som stöds om du vill säkerhetskopiera Linux-datorer:

**Typ av säkerhetskopiering** | **Linux (Azure-godkänt)**
--- | ---
**Direkt säkerhetskopiering av lokal dator som kör Linux** | Stöds inte. MARS-agenten kan endast installeras på Windows-datorer.
**Använda agenttillägg för att säkerhetskopiera Azure VM som kör Linux** | Appkonsekvent säkerhetskopiering med hjälp av [anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Återställning på filnivå.<br/><br/> Återställ genom att skapa en virtuell dator från en återställningspunkt eller disk.
**Använda DPM för att säkerhetskopiera lokala datorer som kör Linux** | Filkonsekvent säkerhetskopiering av virtuella Linux-gäst-datorer på Hyper-V och VMWare.<br/><br/> Vm-återställning av virtuella datorer med Hyper-V och VMWare Linux Guest.
**Använda MABS för att säkerhetskopiera lokala datorer som kör Linux** | Filkonsekvent säkerhetskopiering av virtuella Linux-gäst-datorer på Hyper-V och VMWare.<br/><br/> Vm-återställning av Virtuella linux- och VMWare Linux-gäst-virtuella datorer.
**Använda MABS eller DPM för att säkerhetskopiera virtuella Linux Azure-datorer** | Stöds inte.

## <a name="daylight-saving-time-support"></a>Stöd för sommartid

Azure Backup stöder inte automatisk klockjustering för sommartid för Azure VM-säkerhetskopior. Det skiftar inte timmen för säkerhetskopian framåt eller bakåt. Om du vill vara säkra på att säkerhetskopieringen körs vid önskad tidpunkt ändrar du principerna för säkerhetskopiering manuellt efter behov.

## <a name="disk-deduplication-support"></a>Stöd för diskduplicering

Stöd för diskdeduplicering är följande:

- Diskduplicering stöds lokalt när du använder DPM eller MABS för att säkerhetskopiera virtuella hyper-virtuella datorer som kör Windows. Windows Server utför dataduplicering (på värdnivå) på virtuella hårddiskar (VHD) som är kopplade till den virtuella datorn som lagring av säkerhetskopior.
- Deduplicering stöds inte i Azure för säkerhetskopieringskomponenter. När DPM och MABS distribueras i Azure kan lagringsdiskarna som är anslutna till den virtuella datorn inte dedupliceras.

## <a name="security-and-encryption-support"></a>Stöd för säkerhet och kryptering

Azure Backup stöder kryptering för in-transit- och i vilande data.

### <a name="network-traffic-to-azure"></a>Nätverkstrafik till Azure

- Säkerhetskopieringstrafik från servrar till Recovery Services-valvet krypteras med hjälp av Advanced Encryption Standard 256.
- Säkerhetskopierade data skickas via en säker HTTPS-anslutning.
- Säkerhetskopierade data lagras i Recovery Services-valvet i krypterad form.
- Endast du har tillgång till den lösenfras som krävs för att låsa upp dessa data. Microsoft kan aldrig dekryptera säkerhetskopierade data.

    > [!WARNING]
    > När du har konfigurerat valvet har bara du åtkomst till krypteringsnyckeln. Microsoft sparar aldrig någon kopia och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan Microsoft inte återställa dina säkerhetskopierade data.

### <a name="data-security"></a>Datasäkerhet

- När du säkerhetskopierar virtuella Azure-datorer måste du konfigurera kryptering *inom* den virtuella datorn.
- Azure Backup stöder Azure Disk Encryption, som använder BitLocker på virtuella Windows-datorer och **dm-crypt** på virtuella Linux-datorer.
- På baksidan använder Azure Backup [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md), som skyddar data i vila.

**Dator** | **Under överföring** | **I vila**
--- | --- | ---
**Lokala Windows-datorer utan DPM/MABS** | ![Ja][green] | ![Ja][green]
**Virtuella Azure-datorer** | ![Ja][green] | ![Ja][green]
**Lokala Windows-datorer eller virtuella Azure-datorer med DPM** | ![Ja][green] | ![Ja][green]
**Lokala Windows-datorer eller virtuella Azure-datorer med MABS** | ![Ja][green] | ![Ja][green]

## <a name="compression-support"></a>Stöd för komprimering

Säkerhetskopiering stöder komprimering av säkerhetskopieringstrafik, som sammanfattas i följande tabell.

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure-lagringskontot över lagringsnätverket, så det är inte nödvändigt att komprimera den här trafiken.
- Om du använder DPM eller MABS kan du spara bandbredd genom att komprimera data innan de säkerhetskopieras.

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera till valv (HTTPS)**
--- | --- | ---
**Direkt säkerhetskopiering av lokala Windows-datorer** | Ej tillämpligt | ![Ja][green]
**Säkerhetskopiering av virtuella Azure-datorer med hjälp av vm-tillägg** | Ej tillämpligt | Ej tillämpligt
**Säkerhetskopiering på lokala/Azure-datorer med hjälp av MABS/DPM** | ![Ja][green] | ![Ja][green]

## <a name="retention-limits"></a>Gräns för kvarhållning

**Inställning** | **Begränsningar**
--- | ---
**Max återställningspunkter per skyddad instans (maskin eller arbetsbelastning)** | 9,999
**Maximal förfallotid för en återställningspunkt** | Obegränsad
**Högsta säkerhetskopieringsfrekvens till DPM/MABS** | Varje kvart för SQL Server<br/><br/> En gång i timmen för andra arbetsbelastningar
**Högsta säkerhetskopieringsfrekvens till valv** | **Lokala Windows-datorer eller virtuella Azure-datorer som kör MARS:** Tre per dag<br/><br/> **DPM/MABS:** Två per dag<br/><br/> **Säkerhetskopiering av Azure VM:** En per dag
**Kvarhållning av återställningspunkt** | Varje dag, varje vecka, varje månad, varje år
**Högsta kvarhållningsperiod** | Beror på säkerhetskopieringsfrekvensen
**Återställningspunkter på DPM-/MABS-disk** | 64 för filservrar; 448 för appservrar <br/><br/>Obegränsada bandåterställningspunkter för lokalt DPM

## <a name="cross-region-restore"></a>Återställning mellan regioner

Azure Backup har lagt till funktionen Återställning av flera regioner för att stärka datatillgängligheten och återhämtningskapaciteten, vilket ger kunderna full kontroll över att återställa data till en sekundär region. Om du vill konfigurera den här funktionen besöker du [artikeln Ange återställning mellan regioner.](backup-create-rs-vault.md#set-cross-region-restore) Den här funktionen stöds för följande hanteringstyper:

| Typ av säkerhetskopieringshantering | Stöds                                                    | Regioner som stöds |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Ja.   Stöds för krypterade virtuella datorer och virtuella datorer med mindre än 4 TB diskar | Alla offentliga Azure-regioner.  |
| MARS Agent/På plats | Inga                                                           | Ej tillämpligt               |
| SQL /SAP HANA          | Inga                                                           | Ej tillämpligt               |
| Afs                    | Inga                                                           | Ej tillämpligt               |

## <a name="next-steps"></a>Nästa steg

- [Granska supportmatris för](backup-support-matrix-iaas.md) säkerhetskopiering av Azure VM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
