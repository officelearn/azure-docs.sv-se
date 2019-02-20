---
title: Stöd matrix för Microsoft Azure Backup server och System Center DPM
description: Den här artikeln sammanfattas Azure Backup stöd när du använder Microsoft Azure Backup Server eller System Center DPM för säkerhetskopiering av lokala och Virtuella Azure-resurser.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 74b6d953939a569a3240c9d64134d143dc4f179e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430852"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-serversystem-center-dpm"></a>Stödmatris för säkerhetskopiering med Microsoft Azure Backup Server/System Center DPM

Du kan använda den [Azure Backup-tjänsten](backup-overview.md) för säkerhetskopiering av lokala datorer och arbetsbelastningar och virtuella Azure-datorer. Den här artikeln sammanfattas support inställningar och begränsningar för säkerhetskopiering av datorer med Microsoft Azure Backup Server (MABS) eller System Center Data Protection Manager (DPM) och Azure Backup.

## <a name="about-mabsdpm"></a>Om MABS/DPM

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) är en lösning som konfigurerar, underlättar och hanterar säkerhetskopiering och återställning av enterprise-datorer och data. Det är en del av den [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) -produkter.


Microsoft Azure Backup Server (MABS) är en serverprodukt som kan användas för säkerhetskopiering av lokala fysiska servrar, virtuella datorer (VM) och appar som körs på dem och virtuella Azure-datorer.

- MABS baseras på System Center Data Protection Manager (DPM) och ger liknande funktionalitet med några skillnader:
    - Ingen System Center-licens krävs för att köra MABS.
    - För både MABS och DPM Azure tillhandahåller långsiktig lagring av säkerhetskopior. DPM kan dessutom kan du säkerhetskopiera data för långsiktig lagring på band. MABS ger inte den här funktionen.
- Du kan hämta MABS-server från den [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Den kan köras lokalt eller på en virtuell Azure-dator i Azure.

DPM och MABS stöd för säkerhetskopiering av en mängd olika appar, och server och klientoperativsystem. De tillhandahåller flera olika scenarier för säkerhetskopiering:
    - Du kan säkerhetskopiera på datornivå med systemtillstånd eller bare metal-säkerhetskopiering.
    - Du kan säkerhetskopiera specifika volymer, resurser, mappar och filer.
    - Du kan säkerhetskopiera specifika appar med hjälp av optimerad app-anpassade inställningar.

## <a name="mabsdpm-backup"></a>MABS/DPM-säkerhetskopiering

Säkerhetskopiering med MABS/DPM och Azure Backup fungerar på följande sätt:

1. DPM/MABS-skyddsagenten installerad på varje dator som ska säkerhetskopieras. 
2. Datorer/appar säkerhetskopieras till lokal lagring på DPM/MABS.
3. Microsoft Azure Recovery Services MARS-agenten är installerad på DPM-server/MABS.
4. MARS-agenten säkerhetskopierar DPM/MABS-diskar till ett backup Recovery Services-valv i Azure med Azure Backup.

Mer information:
- [Läs mer](backup-architecture.md#architecture-back-up-to-dpmmabs) om MABS-arkitekturen.
- [Granska](backup-support-matrix-mars-agent.md) vad som stöds för MARS-agenten.

## <a name="supported-scenarios"></a>Scenarier som stöds 

**Scenario** | **Agent** | **Plats**
--- | --- | ---
**Säkerhetskopiera lokala datorer/arbetsbelastningar** | DPM/MABS-skyddsagenten körs på datorer som du vill säkerhetskopiera<br/><br/> MARS-agenten på MABS/DPM-server | MABS/DPM måste köras lokalt
**Säkerhetskopiering av Azure virtuella datorer-arbetsbelastningar** | DPM/MABS-skyddsagenten på skyddad dator<br/><br/> MARS-agenten på MABS/DPM | DPM/MABS måste köras på en virtuell Azure-dator.

## <a name="supported-deployments"></a>Stödda distributioner

DPM/MABS kan distribueras som sammanfattas i tabellen.

**Distribution** | **Support** | **Detaljer**
--- | --- | ---
**Distribueras lokalt** | Fysisk server<br/><br/>Hyper-V-dator<br/><br/> Virtuell VMware-dator | Om DPM/MABS är installerad som en VMware VM det endast säkerhetskopierar virtuella VMware-datorer och arbetsbelastningar som körs på dessa virtuella datorer.
**Distribueras som en virtuell dator med Azure Stack** | Endast MABS | DPM kan inte användas för säkerhetskopiering av virtuella datorer i Azure Stack.
**Distribueras som en Azure virtuell dator** | Skyddar virtuella Azure-datorer och arbetsbelastningar som körs på dessa virtuella datorer. | MABS/DPM som körs i Azure kan inte säkerhetskopiera lokala datorer.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Operativsystem som stöds MABS och DPM

Azure Backup kan säkerhetskopiera MABS/DPM som kör något av följande operativsystem. Operativsystem som ska köras den senaste servicepack och uppdateringar.

**Scenario** | **MABS/DPM** 
--- | --- 
**MABS på en Azure virtuell dator** | Windows Server 2012 R2<br/><br/> Windows 2016 Datacenter<br/><br/> Windows 2019 Datacenter<br/><br/> Vi rekommenderar att du börjar med en avbildning från marketplace.<br/><br/> Minsta A2 Standard med två kärnor och 3,5 GB RAM-minne. 
**DPM på en Azure virtuell dator** | System Center 2012 R2 med uppdatering 3 eller senare.<br/><br/> Windows-operativsystemet som [krävs av System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Vi rekommenderar att du börjar med en avbildning från marketplace.<br/><br/> Minsta A2 Standard med två kärnor och 3,5 GB RAM-minne. 
**MABS på plats** | 64-bitars operativsystem som stöds:<br/><br/> -MABS v3 och senare: Windows Server 2019 (Standard, Datacenter, Essentials) <br/><br/> MABS v2 och senare: Windows Server 2016 (Standard, Datacenter, Essentials)<br/><br/> Alla MABS-versioner: Windows Server 2012 R2/2012 (Standard, Datacenter, Foundation)<br/><br/>Alla MABS-versioner: Windows Storage Server 2012 R2/2012 (Standard/Workgroup)
**DPM på plats** | Fysiska servern/Hyper-V virtuell dator: System Center 2012 SP1 eller senare.<br/><br/> VMware VM: System Center 2012 R2 med uppdatering 5 eller senare. 



## <a name="management-support"></a>Support för
**Problemet** | **Detaljer**
--- | ---
**Installation** | DPM/MABS ska installeras på en dator med enda syfte.<br/><br/> Installera inte DPM/MABS på en domänkontrollant, på en dator med rollinstallationen Application Server, på en dator som kör Exchange Server eller System Center Operations Manager eller på en klusternod.<br/><br/> [Granska](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) alla DPM-systemkraven.
**Domän** | DPM-server/MABS ska anslutas till en domän. Installera först och sedan ansluta DPM/MABS till en domän. Flytta DPM/MABS till en ny domän efter distribution inte stöds.
**Storage** | Modern backup storage (MBS) stöds från DPM 2016/MABS v2 och senare. Det är inte tillgängligt för MABS v1.
**MABS-uppgradering** | Du kan direkt installera MABS v3 eller uppgradera till MABS v3 från MABS v2. [Läs mer](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Flytta MABS** | Flytta MABS till en ny server samtidigt som du behåller lagring stöds om du använder MB.<br/><br/> Servern måste ha samma namn som ursprungligt. Du kan inte ändra namnet om du vill behålla samma lagringspool och använda samma MABS-databas för att lagra återställningspunkter för data.<br/><br/> Du behöver en säkerhetskopia av MABS-databasen eftersom du måste återställa den.




## <a name="mabs-support-on-azure-stack"></a>MABS-stöd på Azure Stack

Du kan distribuera MABS på en Azure Stack-VM så att du kan hantera säkerhetskopiering av virtuella datorer i Azure Stack och arbetsbelastningar från en enda plats.

**Komponent** | **Detaljer**
--- | --- 
**MABS på Azure Stack VM** | Minst A2-storlek. Vi rekommenderar att du börjar med en Windows Server 2012 R2 eller Windows Server 2016-avbildning från Azure marketplace.<br/><br/> Du bör inte installera något annat på MABS-VM.
**MABS-lagring** | Använd ett separat lagringskonto för MAB VM. MARS-agenten som körs på MABS behöver tillfällig lagring för en cacheplats och för att lagra dataåterställning från molnet.
**MABS-lagringspoolen** | Storleken på lagringspoolen MABS bestäms av antalet och storleken på diskar som är anslutna till MABS-VM. Varje Azure Stack VM-storlek har ett maximalt antal diskar. A2 är till exempel fyra diskar.
**MABS kvarhållning** | Du bör inte behålla säkerhetskopierade data på de lokala MABS-diskarna i mer än fem dagar.
**MABS skala upp** | Du kan öka storleken på MABS VM för att skala upp din distribution. Till exempel från A till D-serien.<br/><br/> Du kan också se till att du avlastning data med säkerhetskopiering till Azure regelbundet och om det behövs kan du distribuera ytterligare MABS-servrar.
.NET framework på MABS | MABS VM måste .NET Framework 3.3 SP1 eller installerat senare på den.
**MABS-domän** | MABS VM måste vara ansluten till en domän. En domänanvändare med administratörsprivilegier måste installera MABS på den virtuella datorn.
**Azure Stack-VM-säkerhetskopiering** | Du kan säkerhetskopiera filer, mappar och appar.
**Stöds backup** | Dessa operativsystem har stöd för virtuella datorer du vill säkerhetskopiera:<br/><br/> – Windows Server Halvårskanal (Datacenter/Enterprise/Standard)<br/><br/> Windows Server 2016/2012 R2/2012/2008 R2.
**SQL-stöd för virtuella datorer i Azure Stack** | Säkerhetskopiera SQL Server 2016/2014/2012 SP1.<br/><br/> Säkerhetskopiera och återställa databasen.
**SharePoint-stöd för virtuella datorer i Azure Stack** | SharePoint 2016/2013/2010.<br/><br/> Säkerhetskopiera och återställa servergrupp, databas, klientdel, webbserver.
**Krav för säkerhetskopierade virtuella datorer** | Alla virtuella datorer i Azure Stack-arbetsbelastningen måste vara till samma virtuella nätverk och tillhöra samma prenumeration.


## <a name="dpmmabs-networking-support"></a>Stöd för DPM/MABS-nätverk

### <a name="url-access"></a>URL-åtkomst

DPM-server/MABS måste ha åtkomst till dessa webbadresser:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS-anslutning till Azure Backup

Anslutningen till Azure Backup-tjänsten krävs för säkerhetskopiering ska fungera ordentligt och Azure-prenumerationen ska vara aktiv. I följande tabell visas beteendet om det inte händer för dessa två saker.

**MABS till Azure** | **Prenumeration** | **Säkerhetskopiering/återställning** 
--- | --- | --- 
Ansluten | Active | Säkerhetskopiera till DPM/MABS-disk<br/><br/> Säkerhetskopiering till Azure<br/><br/> Återställa från disk<br/><br/> Återställa från Azure
Ansluten | Har upphört att gälla/avetableras | Ingen säkerhetskopiering till disk- eller Azure.<br/><br/> Om prenumerationen har upphört att gälla kan du återställa från Azure.<br/><br/> Om prenumerationen är inaktiverad kan du återställa från Azure. Azure återställningspunkter tas bort.
Ingen anslutning i mer än 15 dagar | Active | Ingen säkerhetskopiering till disk- eller Azure.<br/><br/> Du kan återställa från Azure.
Ingen anslutning i mer än 15 dagar | Har upphört att gälla/avetableras | Ingen säkerhetskopiering till disk- eller Azure.<br/><br/> Om prenumerationen har upphört att gälla kan du återställa från Azure.<br/><br/> Om prenumerationen är inaktiverad kan du återställa från Azure. Azure återställningspunkter tas bort.



## <a name="dpmmabs-storage-support"></a>Support för DPM/MABS-lagring

Data som säkerhetskopieras till DPM/MABS lagras på lokalt diskutrymme. 

**Storage** | **Detaljer**
--- | ---
**MBS** | Modern backup storage (MBS) stöds från DPM 2016/MABS v2 och senare. Det är inte tillgängligt för MABS v1.
**MABS lagring på Azure VM** | Data lagras på Azure-diskar kopplade till DPM/MABS-VM och hanteras i DPM/MABS. Antalet diskar som kan användas för lagringspoolen i DPM/MABS är begränsat av storleken på den virtuella datorn.<br/><br/> A2 VM: 4 disks; A3 VM: 8 diskar; A4 VM: 16 diskar med en maximal storlek på 1 TB för varje disk. Detta avgör den totala säkerhetskopiering tillgängliga lagringspoolen.<br/><br/> Mängden data som du kan säkerhetskopiera beror på antalet och storleken på de anslutna diskarna.
**MABS datakvarhållning på Azure VM** | Vi rekommenderar att du behåller data under en dag på DPM/MABS-Azure-diskar och säkerhetskopiera från DPM/MABS till valvet för längre kvarhållning. Därför kan du skydda en större mängd data genom att överföra det till Azure Backup.


### <a name="modern-backup-storage-mbs"></a>Modern backup storage (MBS)
Från DPM 2016/MABS v2 (som körs på Windows Server 2016) och senare kan du kan dra nytta av modern backup storage (MBS).

- MB säkerhetskopior lagras på ett flexibelt filsystem (ReFS)-disk.
- MB använder ReFS blockera kloning och för snabbare säkerhetskopiering och effektivare användning av lagringsutrymme.
- När du lägger till volymer i lagringspoolen för lokal DPM/MABS konfigurerat du dem med enhetsbeteckningar. Du kan sedan konfigurera arbetsbelastningen lagring i olika volymer.
- När du skapar skyddsgrupper för att säkerhetskopiera data till DPM/MABS kan välja du den enhet som du vill använda. Du kan till exempel lagra säkerhetskopior för SQL eller andra arbetsbelastningar med hög IOPS en enhet med höga prestanda och arbetsbelastningar som säkerhetskopieras mer sällan på en enhet med lägre prestanda.


## <a name="supported-backups-to-mabs"></a>Stöds säkerhetskopieringar till MABS

I följande tabell sammanfattas vad kan säkerhetskopieras till MABS från lokala datorer och virtuella Azure-datorer.


**Säkerhetskopiering** | **Versioner** | **MABS** | **Detaljer**
--- | --- | --- | ---
**Windows 10, 8.1, 8, 7**<br/><br/>(32/64-bitars) | MABS v3, v2, V1 | Lokal | Volym/resurs/mapp/fil<br/><br/> Deduplicerade volymer som stöds<br/><br/> Volymer måste vara minst 1 GB och NTFS.
**Windows Server 2016 (Datacenter, Standard, not Nano)**<br/><br/> 64/32-bitars | MABS v3, v2 | På lokal/Azure virtuell dator.| Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> Deduplicerade volymer stöds.
**Windows Server 2012 R2 (Datacenter och Standard)**<br/><br/> 64/32-bitars | MABS v3, v2, v1 | På lokal/Azure virtuell dator. | **Lokalt skydd**: Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> **Azure VMprotection**: Volym/resurs/mapp/fil<br/><br/> Deduplicerade volymer stöds.
**Windows Server 2012 med SP1 (Datacenter och Standard)**<br/><br/> 64/32-bitars | MABS v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) måste vara installerad. | På lokal/Azure virtuell dator. | **Lokalt skydd**: Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> **Azure VM-protection**: Volym/resurs/mapp/fil<br/><br/> Deduplicerade volymer stöds.
**Windows Server 2012 (Datacenter och Standard)**<br/><br/> 64/32-bitars | MABS v1 | På lokal/Azure virtuell dator. | **Lokalt skydd**: Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> **Azure VM-protection**: Volym/resurs/mapp/fil<br/><br/> Deduplicerade volymer stöds.
**Windows 2008 R2 med SP1 (Standard och Enterprise)**<br/><br/> 64/32-bitars | Stöds av MABS v3, v2 och v1.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) måste vara installerad. | På lokal/Azure virtuell dator. |   **Lokalt skydd**: Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> **Azure VM-protection**: Volym/resurs/mapp/fil<br/><br/> Deduplicerade volymer stöds.
**Windows 2008 R2 (Standard och Enterprise)**<br/><br/> 64/32-bitars | MABS v1. För MABS kör v2/v3 Operativsystemet SP1. | På lokal/Azure virtuell dator. | **Lokalt skydd**: Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> **Azure VM-protection**: Volym/resurs/mapp/fil<br/><br/> Deduplicerade volymer stöds.
**Windows Server 2008 med SP2**<br/><br/> 64/32-bitars | MABS v1, v2, v3 | Endast v1 stöds när MABS distribueras som en lokal fysisk eller Hyper-V virtuell dator.<br/><br/> MABS v1, 2, 3 stöds när MABS distribueras som en VMware VM.<br/><br/> Stöds inte för MABS som körs på virtuella Azure-datorer | Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem.
**Windows Storage Server 2008** | MABS v1, v2, v3 | MABS som lokala fysiska servern/Hyper-V virtuell dator | Stöds inte för MABS som körs på virtuella Azure-datorer. | Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem.
**SQL Server 2017** | MABS v3 | På lokal/Azure virtuell dator.| Säkerhetskopiera SQL Server-databas.<br/><br/> SQL Server-kluster säkerhetskopiering stöds.<br/><br/>Databaser som lagras på CSV: er stöds inte.
**SQL Server 2016/2016 med SP1** | MABS v3, v2 | På lokal/Azure virtuell dator.| Säkerhetskopiera SQL Server-databas.<br/><br/> SQL Server-kluster säkerhetskopiering stöds.<br/><br/>Databaser som lagras på CSV: er stöds inte.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2, v1 | På lokal/Azure virtuell dator.| Säkerhetskopiera SQL Server-databas.<br/><br/> SQL Server-kluster säkerhetskopiering stöds.<br/><br/>Databaser som lagras på CSV: er stöds inte.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2, V1 | Lokala. | Säkerhetskopiera fristående Exchange-server, databas under en DAG<br/><br/> Återställ postlådan, postlådedatabas under en DAG.<br/><br/> ReFS inte stöds.<br/><br/> Säkerhetskopiera icke delade diskkluster.<br/><br/> Säkerhetskopiera Exchange-Server som konfigurerats för kontinuerlig replikering.
**SharePoint 2016**<br/><br/> **Sharepoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2, v1 | På lokal/Azure VM | Säkerhetskopiera servergrupp, webbserver för klientdel.<br/><br/> Återställa servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, webbserver för klientdel.<br/><br/> Du kan inte säkerhetskopiera en servergrupp som använder AlwaysOn för innehållsdatabaserna.
**Hyper-V på Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (med SP1)** | MABS v3, v2, v1 | Lokal | **MABS-agenten på Hyper-V-värd**: Säkerhetskopiera hela virtuella datorer och datafiler för värden. Säkerhetskopiera virtuella datorer med lokal lagring, virtuella datorer i kluster med CSV-lagring, virtuella datorer med SMB-filserverlagring.<br/><br/> **MABS-agenten på den Virtuella gästdatorn**: Säkerhetskopiera arbetsbelastningar som körs på den virtuella datorn. CSVs.<br/><br/> **Recovery**: Den virtuella datorn, objektnivååterställning av VHD/volym/mappar/filer.<br/><br/> **Virtuella Linux-datorer**: Säkerhetskopiera när Hyper-V körs på Windows Server 2012 R2 och senare. Återställning av virtuella Linux-datorer är för hela datorn.
**VMware VMs: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> MABS v1 måste Samlad uppdatering 1) | Lokal | Säkerhetskopiera virtuella VMware-datorer på klusterdelade volymer, NFS och SAN-lagring.<br/><br/> Återställa en hel virtuell dator.<br/><br/> Windows-/ Linux-säkerhetskopiering.<br/><br/> Objektnivååterställning av mappen/endast-filer för virtuella Windows-datorer.<br/><br/> VMware vApps stöds inte.<br/><br/> Återställning av virtuella Linux-datorer är för hela datorn.



## <a name="supported-backups-to-dpm"></a>Säkerhetskopior av DPM som stöds

I följande tabell sammanfattas vad kan säkerhetskopieras till DPM från lokala datorer och virtuella Azure-datorer.



**Säkerhetskopiering** | **DPM** | **Detaljer**
--- | --- | --- 
**Windows 10, 8.1, 8, 7**<br/><br/>(32/64-bitars) | Endast lokalt.<br/><br/> För att säkerhetskopiera Windows 10 med DPM 2012 R2 vi rekommenderar att du installerar [uppdatering 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Volym/resurs/mapp/fil<br/><br/> Deduplicerade volymer som stöds<br/><br/> Volymer måste vara minst 1 GB och NTFS.
**Windows Server 2016 (Datacenter, Standard, not Nano)**<br/><br/> 64/32-bitars | På lokal/Azure virtuell dator.<br/><br/> DPM-2016.| Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> Deduplicerade volymer stöds.
**Windows Server 2012 R2 (Datacenter och Standard)**<br/><br/> 64/32-bitars | På lokal/Azure VM | **Lokalt skydd**: Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> **Azure VM-protection**: Volym/resurs/mapp/fil<br/><br/> Deduplicerade volymer stöds med DPM 2012 R2 och senare.
**Windows Server 2012 med SP1 (Datacenter och Standard)**<br/><br/> 64/32-bitars | På lokal/Azure virtuell dator. | **Lokalt skydd**: Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> **Azure VM-protection**: Volym/resurs/mapp/fil<br/><br/> Deduplicerade volymer stöds med DPM 2012 R2 och senare.
**Windows Server 2012 (Datacenter och Standard)**<br/><br/> 64/32-bitars |  På lokal/Azure virtuell dator. | **Lokalt skydd**: Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> **Azure VM-protection**: Volym/resurs/mapp/fil<br/><br/> Deduplicerade volymer stöds med DPM 2012 R2 och senare.
**Windows 2008 R2 med SP1 (Standard och Enterprise)**<br/><br/> 64/32-bitars | På lokal/Azure VM <br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) måste vara installerad. |   **Lokalt skydd**: Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem<br/><br/> **Azure VM-protection**: Volym/resurs/mapp/fil.
**Windows 2008 R2 (Standard och Enterprise)**<br/><br/> 64/32-bitars | Lokala.<br/><br/> DPM kan inte installeras som en VMware-VM.<br/><br/> DPM som körs på en Azure virtuell dator stöds inte. | **Lokalt skydd**: Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem
**Windows Server 2008 med SP2**<br/><br/> 64/32-bitars | Endast lokalt.<br/><br/> DPM stöds vid körning som en VMware VM. Körs som en fysisk server eller Hyper-V virtuell dator stöds inte. | Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem.
**Windows Storage Server 2008** | DPM lokala körs som en fysisk server eller Hyper-V-dator. | Volym/resurs/mappen/filen; system-tillstånd/utan operativsystem.
**SQL Server 2017** | DPM SAC; DPM 2016 köra Samlad uppdatering 5 eller senare<br/><br/> På lokal/Azure virtuell dator.| Säkerhetskopiera SQL Server-databas.<br/><br/> SQL Server-kluster säkerhetskopiering stöds.<br/><br/>Databaser som lagras på CSV: er stöds inte.
**SQLServer 2016 med SP1** | Stöds inte för DPM 2012 R2; Stöd för DPM SAC, DPM 2016 köra Samlad uppdatering 4 eller senare.<br/><br/> På lokal/Azure virtuell dator.| Säkerhetskopiera SQL Server-databas.<br/><br/> SQL Server-kluster säkerhetskopiering stöds.<br/><br/>Databaser som lagras på CSV: er stöds inte.
**SQL Server 2016** | Stöds inte för DPM 2012 R2. Stöd för DPM SAC, DPM 2016 från Samlad uppdatering 2 och senare.<br/><br/> På lokal/Azure virtuell dator.| Säkerhetskopiera SQL Server-databas.<br/><br/> SQL Server-kluster säkerhetskopiering stöds.<br/><br/>Databaser som lagras på CSV: er stöds inte.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 med DPM 2012 R2 med Samlad uppdatering 4 och senare.<br/><br/> På lokal/Azure virtuell dator.| Säkerhetskopiera SQL Server-databas.<br/><br/> SQL Server-kluster säkerhetskopiering stöds.<br/><br/>Databaser som lagras på CSV: er stöds inte.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | För Exchange 2016 måste DPM 2012 R2 Samlad uppdatering 9 eller senare.<br/><br/> Lokal | Säkerhetskopiera fristående Exchange-server, databas under en DAG<br/><br/> Återställ postlådan, postlådedatabas under en DAG.<br/><br/> ReFS inte stöds.<br/><br/> Säkerhetskopiera icke delade diskkluster.<br/><br/> Säkerhetskopiera Exchange-Server som konfigurerats för kontinuerlig replikering.
**SharePoint 2016**<br/><br/> **Sharepoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 DPM 2016 och senare.<br/><br/>På lokal/Azure VM | Säkerhetskopiera servergrupp, webbserver för klientdel.<br/><br/> Återställa servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, webbserver för klientdel.<br/><br/> Du kan inte säkerhetskopiera en servergrupp som använder AlwaysOn för innehållsdatabaserna.
**Hyper-V på Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (med SP1)** | Hyper-V på 2016 som stöds för DPM 2016 och senare.<br/><br/> Lokal | **MABS-agenten på Hyper-V-värd**: Säkerhetskopiera hela virtuella datorer och datafiler för värden. Säkerhetskopiera virtuella datorer med lokal lagring, virtuella datorer i kluster med CSV-lagring, virtuella datorer med SMB-filserverlagring.<br/><br/> **MABS-agenten på den Virtuella gästdatorn**: Säkerhetskopiera arbetsbelastningar som körs på den virtuella datorn. CSVs.<br/><br/> **Recovery**: Den virtuella datorn, objektnivååterställning av VHD/volym/mappar/filer.<br/><br/> **Virtuella Linux-datorer**: Säkerhetskopiera när Hyper-V körs på Windows Server 2012 R2 och senare. Återställning av virtuella Linux-datorer är för hela datorn.
**VMware VMs: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> DPM 2012 R2 måste Samlad uppdatering 1 för System Center) | Lokal | Säkerhetskopiera virtuella VMware-datorer på klusterdelade volymer, NFS och SAN-lagring.<br/><br/> Återställa en hel virtuell dator.<br/><br/> Windows-/ Linux-säkerhetskopiering.<br/><br/> Objektnivååterställning av mappen/endast-filer för virtuella Windows-datorer.<br/><br/> VMware vApps stöds inte.<br/><br/> Återställning av virtuella Linux-datorer är för hela datorn.


- Observera att klustrade arbetsbelastningar som säkerhetskopieras av DPM/MABS bör i samma domän som DPM/MABS eller i en underordnad/betrodd domän. 
- Du kan använda NTLM/certifikat autentisering för att säkerhetskopiera data i obetrodda domäner eller arbetsgrupper.



## <a name="next-steps"></a>Nästa steg

- [Läs mer](backup-architecture.md#architecture-back-up-to-dpmmabs) om MABS-arkitekturen.
- [Granska](backup-support-matrix-mars-agent.md) vad som stöds för MARS-agenten.
- [Konfigurera](backup-azure-microsoft-azure-backup.md) en MABS-server.
- [Konfigurera] (https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180
