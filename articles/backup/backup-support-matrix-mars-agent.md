---
title: Stödmatris för MARS-agenten
description: I den här artikeln sammanfattas Azure Backup-stöd när du säkerhetskopierar datorer som kör MARS-agenten (Microsoft Azure Recovery Services).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 6085bc647c06b5907282460a2d8706b8549e1bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247870"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Stödmatris för säkerhetskopiering med MARS-agenten (Microsoft Azure Recovery Services)

Du kan använda [Azure Backup-tjänsten](backup-overview.md) för att säkerhetskopiera lokala datorer och appar och för att säkerhetskopiera virtuella Azure-datorer (VMs). Den här artikeln sammanfattar supportinställningar och begränsningar när du använder MARS-agenten (Microsoft Azure Recovery Services) för att säkerhetskopiera datorer.

## <a name="the-mars-agent"></a>MARS-agenten

Azure Backup använder MARS-agenten för att säkerhetskopiera data från lokala datorer och virtuella Azure-datorer till ett backup Recovery Services-valv i Azure. MARS-agenten kan:

- Kör på lokala Windows-datorer så att de kan säkerhetskopiera direkt till ett backup Recovery Services-valv i Azure.
- Kör på virtuella Windows-datorer så att de kan säkerhetskopiera direkt till ett valv.
- Kör på EN MABS (Microsoft Azure Backup Server) eller en DPM-server (System Center Data Protection Manager). I det här fallet säkerhetskopierar datorer och arbetsbelastningar upp till MABS eller DPM-servern. MARS-agenten säkerhetskopierar sedan den här servern till ett valv i Azure.

> [!NOTE]
>Azure Backup stöder inte automatisk justering av klockan för sommartid (DST). Ändra principen för att säkerställa att sommartid beaktas för att förhindra avvikelser mellan den faktiska tiden och schemalagd säkerhetskopieringstid.

Alternativen för säkerhetskopiering beror på var agenten är installerad. Mer information finns i [Azure Backup-arkitektur med MARS-agenten](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Information om MABS- och DPM-säkerhetskopieringsarkitektur finns i [Säkerhetskopiera till DPM eller MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Se även [krav](backup-support-matrix-mabs-dpm.md) för säkerhetskopieringsarkitekturen.

**Installation** | **Detaljer**
--- | ---
Ladda ner den senaste MARS-agenten | Du kan hämta den senaste versionen av agenten från valvet eller [ladda ner](https://aka.ms/azurebackup_agent)den direkt .
Installera direkt på en maskin | Du kan installera MARS-agenten direkt på en lokal Windows-server eller på en Windows-virtuell dator som kör något av de [operativsystem som stöds](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Installera på en säkerhetskopieringsserver | När du konfigurerar DPM eller MABS för att säkerhetskopiera till Azure hämtar och installerar du MARS-agenten på servern. Du kan installera agenten på [operativsystem som stöds](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) i stödmatrisen för säkerhetskopieringsservern.

> [!NOTE]
> Som standard har virtuella Azure-datorer som är aktiverade för säkerhetskopiering en azure backup-tilläggsinstallation. Det här tillägget säkerhetskopierar hela den virtuella datorn. Du kan installera och köra MARS-agenten på en Azure VM tillsammans med tillägget om du vill säkerhetskopiera specifika mappar och filer i stället för den fullständiga virtuella datorn.
> När du kör MARS-agenten på en Virtuell Azure säkerhetskopierar den filer eller mappar som finns i tillfällig lagring på den virtuella datorn. Säkerhetskopior misslyckas om filerna eller mapparna tas bort från det tillfälliga lagringsutrymmet eller om det tillfälliga lagringsutrymmet tas bort.

## <a name="cache-folder-support"></a>Stöd för cachemapp

När du använder MARS-agenten för att säkerhetskopiera data tar agenten en ögonblicksbild av data och lagrar dem i en lokal cachemapp innan den skickar data till Azure. Cachemappen (scratch) har flera krav:

**Cache** | **Detaljer**
--- | ---
Storlek |  Ledigt utrymme i cachemappen bör vara minst 5 till 10 procent av den totala storleken på dina säkerhetskopierade data.
Location | Cachemappen måste lagras lokalt på den dator som säkerhetskopieras och den måste vara online. Cachemappen ska inte finnas på en nätverksresurs, på flyttbara media eller på en offlinevolym.
Mapp | Cachemappen bör inte krypteras på en deduplicerad volym eller i en mapp som är komprimerad, som är gles eller som har en reparspunkt.
Platsändringar | Du kan ändra cacheplatsen genom att`net stop bengine`stoppa reservmotorn ( ) och kopiera cachemappen till en ny enhet. (Se till att den nya enheten har tillräckligt med utrymme.) Uppdatera sedan två registerposter under **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** and **Config/CloudBackupProvider/ScratchLocation**) till den nya platsen och starta om motorn.

## <a name="networking-and-access-support"></a>Stöd för nätverk och åtkomst

### <a name="url-and-ip-access"></a>URL och IP-åtkomst

MARS-agenten behöver åtkomst till dessa webbadresser:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net

Och till dessa IP-adresser:

- 20.190.128.0/18
- 40.126.0.0/18

Åtkomst till alla webbadresser och IP-adresser som anges ovan använder HTTPS-protokollet på port 443.

### <a name="azure-expressroute-support"></a>Support för Azure ExpressRoute

Du kan säkerhetskopiera dina data via Azure ExpressRoute med offentlig peering (tillgänglig för gamla kretsar) och Microsoft-peering. Säkerhetskopiering via privat peering stöds inte.

Med offentlig peering: Se till att du får tillgång till följande domäner/adresser:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

Med Microsoft-peering väljer du följande tjänster/regioner och relevanta community-värden:

- Azure Active Directory (12076:5060)
- Microsoft Azure-region (beroende på var ditt Recovery Services-valv finns)
- Azure Storage (beroende på platsen för ditt Recovery Services-valv)

Mer information finns i [ExpressRoute-routningskraven](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>Offentlig peering är föråldrad för nya kretsar.

### <a name="throttling-support"></a>Stöd för begränsning

**Funktion** | **Detaljer**
--- | ---
Bandbreddskontroll | Stöds. I MARS-agenten använder du **Ändra egenskaper** för att justera bandbredden.
Nätverksbegränsning | Det är inte tillgängligt för säkerhetskopierade datorer som kör Windows Server 2008 R2, Windows Server 2008 SP2 eller Windows 7.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

>[!NOTE]
> MARS-agenten stöder inte Windows Server Core SKU:er.

Du kan använda MARS-agenten för att säkerhetskopiera direkt till Azure på de operativsystem som anges nedan och som körs på:

1. Lokala Windows-servrar
2. Virtuella Azure-datorer som kör Windows

Operativsystemen måste vara 64 bitar och ska köra de senaste servicepaketen och uppdateringarna. I följande tabell sammanfattas dessa operativsystem:

**Operativsystem** | **Filer/mappar** | **Systemtillstånd** | **Krav på programvara/modul**
--- | --- | --- | ---
Windows 10 (Företag, Pro, Hem) | Ja | Inga |  Kontrollera om det finns krav på programvara/modul i motsvarande serverversion
Windows 8.1 (Företag, Pro)| Ja |Inga | Kontrollera om det finns krav på programvara/modul i motsvarande serverversion
Windows 8 (Företag, Proffs) | Ja | Inga | Kontrollera om det finns krav på programvara/modul i motsvarande serverversion
Windows Server 2016 (Standard, Datacenter, Essentials) | Ja | Ja | - .NET 4.5 <br> - Windows PowerShell <br> - Senaste kompatibla Microsoft VC ++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Ja | Ja | - .NET 4.5 <br> - Windows PowerShell <br> - Senaste kompatibla Microsoft VC ++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Ja | Ja |- .NET 4.5 <br> -Windows PowerShell <br> - Senaste kompatibla Microsoft VC ++ Redistributable <br> - Microsoft Management Console (MMC) 3.0 <br> - Driftsättningsavbildning Service och hantering (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Arbetsgrupp) | Ja | Inga | - .NET 4.5 <br> - Windows PowerShell <br> - Senaste kompatibla Microsoft VC ++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Ja | Ja | - .NET 4.5 <br> - Windows PowerShell <br> - Senaste kompatibla Microsoft VC ++ Redistributable <br> - Microsoft Management Console (MMC) 3.0

Mer information finns i [MABS- och DPM-operativsystem som stöds](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Operativsystem i slutet av supporten

Följande operativsystem är i slutet av supporten och det rekommenderas starkt att uppgradera operativsystemet för att fortsätta att skyddas.

Om befintliga åtaganden förhindrar uppgradering av operativsystemet kan du överväga att migrera Windows-servrarna till virtuella Azure-datorer och utnyttja Azure VM-säkerhetskopior för att fortsätta skyddas. Besök [migreringssidan här](https://azure.microsoft.com/migration/windows-server/) om du vill ha mer information om hur du migrerar Din Windows-server.

För lokala eller värdbaserade miljöer, där du inte kan uppgradera operativsystemet eller migrera till Azure, aktiverar du utökade säkerhetsuppdateringar för datorerna för att fortsätta skyddas och stöds. Observera att endast specifika utgåvor är berättigade till utökade säkerhetsuppdateringar. Besök [faq-sidan](https://www.microsoft.com/cloud-platform/extended-security-updates) om du vill veta mer.

| **Operativsystem**                                       | **Filer/mappar** | **Systemtillstånd** | **Krav på programvara/modul**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Ja               | Inga                 | Kontrollera om det finns krav på programvara/modul i motsvarande serverversion |
| Windows Server 2008 R2 (Standard, Företag, Datacenter, Foundation) | Ja               | Ja                | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Kompatibel Microsoft VC++ Redistributable <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Driftsättningsavbildning Service och hantering (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Ja               | Inga                 | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Kompatibel Microsoft VC++ Redistributable <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Driftsättningsavbildning Service och hantering (DISM.exe) <br>  - Virtual Server 2005 bas + KB KB948515 |

## <a name="backup-limits"></a>Begränsningar för säkerhetskopiering

### <a name="size-limits"></a>Storleksbegränsningar

Azure Backup begränsar storleken på en fil- eller mappdatakälla som kan säkerhetskopieras. De objekt som du säkerhetskopierar från en enskild volym kan inte överskrida de storlekar som sammanfattas i den här tabellen:

**Operativsystem** | **Storleksgräns**
--- | ---
Windows Server 2012 eller senare |54 400 GB
Windows Server 2008 R2 SP1 |1 700 GB
Windows Server 2008 SP2| 1 700 GB
Windows 8 eller senare| 54 400 GB
Windows 7| 1 700 GB

### <a name="other-limitations"></a>Andra begränsningar

- MARS stöder inte skydd av flera datorer med samma namn till ett enda valv.

## <a name="supported-file-types-for-backup"></a>Filtyper som stöds för säkerhetskopiering

**Typ** | **Support**
--- | ---
Krypterade<sup>*</sup>| Stöds.
Komprimerade | Stöds.
Utspridda | Stöds.
Komprimerad och gles |Stöds.
Hårda länkar| Stöds inte. Överhoppade.
Referenspunkt| Stöds inte. Överhoppade.
Krypterad och gles |Stöds inte. Överhoppade.
Komprimerad ström| Stöds inte. Överhoppade.
Gles ström| Stöds inte. Överhoppade.
OneDrive (synkroniserade filer är glesa strömmar)| Stöds inte.
Mappar med DFS Replication aktiverat | Stöds inte.

\*Kontrollera att MARS-agenten har åtkomst till de certifikat som krävs för att komma åt de krypterade filerna. Otillgängliga filer hoppas över.

## <a name="supported-drives-or-volumes-for-backup"></a>Enheter eller volymer som stöds för säkerhetskopiering

**Enhet/volym** | **Support** | **Detaljer**
--- | --- | ---
Skrivskyddade volymer| Stöds inte | VSS (Volume Copy Shadow Service) fungerar bara om volymen är skrivbar.
Offlinevolymer| Stöds inte |VSS fungerar bara om volymen är online.
Nätverksresurs| Stöds inte |Volymen måste vara lokal på servern.
BitLocker-låsta volymer| Stöds inte |Volymen måste låsas upp innan säkerhetskopian startar.
Identifiering av filsystem| Stöds inte |Endast NTFS stöds.
Flyttbara medier| Stöds inte |Alla källor för säkerhetskopieringsobjekt måste ha en *fast* status.
Deduplicerade enheter | Stöds | Azure Backup konverterar deduplicerade data till normala data. Den optimerar, krypterar, lagrar och skickar data till valvet.

## <a name="support-for-initial-offline-backup"></a>Stöd för inledande offline säkerhetskopiering

Azure Backup stöder *offline seedning* för att överföra initiala säkerhetskopieringsdata till Azure med hjälp av diskar. Det här stödet är användbart om den första säkerhetskopian sannolikt ligger i storleksintervallet för terabyte (TBs). Offline säkerhetskopiering stöds för:

- Direkt säkerhetskopiering av filer och mappar på lokala datorer som kör MARS-agenten.
- Säkerhetskopiering av arbetsbelastningar och filer från en DPM-server eller MABS.

Offlinesäkerhetskopiering kan inte användas för systemtillståndsfiler.

## <a name="support-for-data-restoration"></a>Stöd för dataåterställning

Genom att använda [funktionen Direktåterställning i](backup-instant-restore-capability.md) Azure Backup kan du återställa data innan de kopieras till valvet. Datorn som du säkerhetskopierar måste köra .NET Framework 4.5.2 eller senare.

Säkerhetskopior kan inte återställas till en måldator som kör en tidigare version av operativsystemet. En säkerhetskopia som tas från en dator med Windows 7 kan till exempel återställas i Windows 8 eller senare. Men en säkerhetskopia som tas från en dator som kör Windows 8 kan inte återställas på en dator som kör Windows 7.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [säkerhetskopieringsarkitektur som använder MARS-agenten](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Läs om vad som stöds när du [kör MARS-agenten på MABS eller en DPM-server](backup-support-matrix-mabs-dpm.md).
