---
title: MABS & System Center DPM-stödmatris
description: I den här artikeln sammanfattas Azure Backup-stöd när du använder MABS (Microsoft Azure Backup Server) eller System Center DPM för att säkerhetskopiera lokala resurser och Azure VM-resurser.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6664f7b226b75b364fd1c83f2abc56b5a275eff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582661"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Stödmatris för säkerhetskopiering med Microsoft Azure Backup Server eller System Center DPM

Du kan använda [Azure Backup-tjänsten](backup-overview.md) för att säkerhetskopiera lokala datorer och arbetsbelastningar och virtuella Azure-datorer (VIRTUELLA datorer). I den här artikeln sammanfattas supportinställningar och begränsningar för säkerhetskopiering av datorer med hjälp av Microsoft Azure Backup Server (MABS) eller System Center Data Protection Manager (DPM) och Azure Backup.

## <a name="about-dpmmabs"></a>Om DPM/MABS

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) är en företagslösning som konfigurerar, underlättar och hanterar säkerhetskopiering och återställning av företagsdatorer och data. Det är en del av [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) svit av produkter.

MABS är en serverprodukt som kan användas för att säkerhetskopiera lokala fysiska servrar, virtuella datorer och appar som körs på dem.

MABS är baserat på System Center DPM och ger liknande funktioner med några skillnader:

- Ingen System Center-licens krävs för att köra MABS.
- För både MABS och DPM tillhandahåller Azure långsiktig säkerhetskopieringslagring. Dessutom kan DPM du säkerhetskopiera data för långsiktig lagring på band. MABS tillhandahåller inte den här funktionen.
- Du kan säkerhetskopiera en primär DPM-server med en sekundär DPM-server. Den sekundära servern skyddar den primära serverns databas och datakällreplikerna som lagras på den primära servern. Om den primära servern slutar fungera kan den sekundära servern ta över och fortsätta skydda arbetsbelastningarna som skyddas av den primära servern tills den primära servern är tillgänglig igen.  MABS tillhandahåller inte den här funktionen.

Du hämtar MABS från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57520). Den kan köras lokalt eller på en Azure VM.

DPM och MABS stöder säkerhetskopiering av en mängd olika appar samt server- och klientoperativsystem. De tillhandahåller flera scenarier för säkerhetskopiering:

- Du kan säkerhetskopiera på maskinnivå med säkerhetskopiering av systemtillstånd eller bare-metal.
- Du kan säkerhetskopiera specifika volymer, resurser, mappar och filer.
- Du kan säkerhetskopiera specifika appar med optimerade appmedvetna inställningar.

## <a name="dpmmabs-backup"></a>DPM/MABS-säkerhetskopiering

Säkerhetskopiering med DPM/MABS och Azure Backup fungerar på följande sätt:

1. DPM/MABS-skyddsagenten är installerad på varje dator som ska säkerhetskopieras.
1. Datorer och appar säkerhetskopieras till lokal lagring på DPM/MABS.
1. Mars-agenten (Microsoft Azure Recovery Services) är installerad på DPM-servern/MABS.
1. MARS-agenten säkerhetskopierar DPM/MABS-diskarna till ett backup Recovery Services-valv i Azure med hjälp av Azure Backup.

Mer information:

- [Läs mer](backup-architecture.md#architecture-back-up-to-dpmmabs) om MABS-arkitektur.
- [Granska vad som stöds](backup-support-matrix-mars-agent.md) för MARS-agenten.

## <a name="supported-scenarios"></a>Scenarier som stöds

**Scenario** | **Agent** | **Location**
--- | --- | ---
**Säkerhetskopiera lokala datorer/arbetsbelastningar** | DPM/MABS-skyddsagent körs på de datorer som du vill säkerhetskopiera.<br/><br/> MARS-agenten på DPM/MABS-servern.<br/> Den minsta versionen av Microsoft Azure Recovery Services-agenten eller Azure Backup-agenten som krävs för att aktivera den här funktionen är 2.0.8719.0.  | DPM/MABS måste köras lokalt.

## <a name="supported-deployments"></a>Distributioner som stöds

DPM/MABS kan distribueras enligt följande tabell.

**Distribution** | **Support** | **Detaljer**
--- | --- | ---
**Distribueras lokalt** | Fysisk server<br/><br/>Hyper-V VM<br/><br/> Virtuella datorer med VMware | Om DPM/MABS installeras som en virtuell virtuell VMware säkerhetskopierar den bara virtuella datorer och arbetsbelastningar för VMware som körs på dessa virtuella datorer.
**Distribueras som en virtuell azure stack-dator** | ENDAST MABS | DPM kan inte användas för att säkerhetskopiera virtuella Azure Stack-datorer.
**Distribueras som en virtuell Azure-dator** | Skyddar virtuella Azure-datorer och arbetsbelastningar som körs på dessa virtuella datorer | DPM/MABS som körs i Azure kan inte säkerhetskopiera lokala datorer.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Mabs- och DPM-operativsystem som stöds

Azure Backup kan säkerhetskopiera DPM/MABS-instanser som kör något av följande operativsystem. Operativsystemen bör köra de senaste Service Pack-versionerna och uppdateringarna.

**Scenario** | **DPM/MABS**
--- | ---
**MABS på en virtuell Azure-dator** |  Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> Vi rekommenderar att du börjar med en avbildning från marknadsplatsen.<br/><br/> Minsta Standard_A4_v2 med fyra kärnor och 8 GB RAM.
**DPM på en virtuell Azure-dator** | System Center 2012 R2 med uppdatering 3 eller senare.<br/><br/> Windows-operativsystem [som krävs av System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Vi rekommenderar att du börjar med en avbildning från marknadsplatsen.<br/><br/> Minsta Standard_A4_v2 med fyra kärnor och 8 GB RAM.
**MABS lokalt** |  MABS v3 och senare: Windows Server 2016 eller Windows Server 2019
**DPM lokalt** | Fysisk server/Hyper-V VM: System Center 2012 SP1 eller senare.<br/><br/> Virtuell VMware: System Center 2012 R2 med uppdatering 5 eller senare.

>[!NOTE]
>Installation av Azure Backup Server stöds inte på Windows Server Core eller Microsoft Hyper-V Server.

## <a name="management-support"></a>Ledningsstöd

**Problem** | **Detaljer**
--- | ---
**Installation** | Installera DPM/MABS på en enfunktionsmaskin.<br/><br/> Installera inte DPM/MABS på en domänkontrollant, på en dator med rollinstallationen av Application Server, på en dator som kör Microsoft Exchange Server eller System Center Operations Manager eller på en klusternod.<br/><br/> [Granska alla DPM-systemkrav](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domain** | DPM/MABS ska anslutas till en domän. Installera först och anslut sedan DPM/MABS till en domän. Det går inte att flytta DPM/MABS till en ny domän efter distributionen.
**Lagring** | Modern lagring av säkerhetskopiering (MBS) stöds från DPM 2016/MABS v2 och senare. Den är inte tillgänglig för MABS v1.
**MABS uppgradering** | Du kan direkt installera MABS v3, eller uppgradera till MABS v3 från MABS v2. [Läs mer](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Flytta MABS** | Det går inte att flytta MABS till en ny server samtidigt som lagringen behålls om du använder MBS.<br/><br/> Servern måste ha samma namn som originalet. Du kan inte ändra namnet om du vill behålla samma lagringspool och använda samma MABS-databas för att lagra dataåterställningspunkter.<br/><br/> Du behöver en säkerhetskopia av MABS-databasen eftersom du måste återställa den.

## <a name="mabs-support-on-azure-stack"></a>MABS-stöd på Azure Stack

Du kan distribuera MABS på en virtuell Azure Stack-dator så att du kan hantera säkerhetskopiering av virtuella Azure Stack-datorer och arbetsbelastningar från en enda plats.

**Komponent** | **Detaljer**
--- | ---
**MABS på Azure Stack VM** | Minst storlek A2. Vi rekommenderar att du börjar med en Windows Server 2012 R2- eller Windows Server 2016-avbildning från Azure Marketplace.<br/><br/> Installera inget annat på MABS VM.
**MABS lagring** | Använd ett separat lagringskonto för den virtuella datorn för MABS. MARS-agenten som körs på MABS behöver tillfällig lagring för en cacheplats och för att lagra data som återställs från molnet.
**MABS-lagringspool** | Storleken på MABS-lagringspoolen bestäms av antalet diskar och storleken på diskar som är kopplade till den virtuella datorn för MABS. Varje Azure Stack VM-storlek har ett maximalt antal diskar. A2 är till exempel fyra diskar.
**MABS-lagring** | Behåll inte säkerhetskopierade data på de lokala MABS-diskarna i mer än fem dagar.
**MABS skala upp** | Om du vill skala upp distributionen kan du öka storleken på den virtuella datorn för MABS. Du kan till exempel ändra från A till D-serien.<br/><br/> Du kan också se till att du avlastar data med säkerhetskopiering till Azure regelbundet. Om det behövs kan du distribuera ytterligare MABS-servrar.
**.NET-ramverket för MABS** | DEN VM-datorn för MABS behöver .NET Framework 3.3 SP1 eller senare installerad på den.
**MABS-domän** | DEN VIRTUELLA MABS-datorn måste anslutas till en domän. En domänanvändare med administratörsbehörighet måste installera MABS på den virtuella datorn.
**Säkerhetskopiering av Azure Stack VM-data** | Du kan säkerhetskopiera filer, mappar och appar.
**Säkerhetskopiering som stöds** | Dessa operativsystem stöds för virtuella datorer som du vill säkerhetskopiera:<br/><br/> Windows Server Halvårskanal (Datacenter, Företag, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**SQL Server-stöd för virtuella Azure Stack-datorer** | Säkerhetskopiera SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Säkerhetskopiera och återställa en databas.
**SharePoint-stöd för virtuella Azure Stack-datorer** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Säkerhetskopiera och återställa en servergrupp, databas, klientdel och webbserver.
**Nätverkskrav för säkerhetskopierade virtuella datorer** | Alla virtuella datorer i Azure Stack-arbetsbelastningen måste tillhöra samma virtuella nätverk och tillhör samma prenumeration.

## <a name="dpmmabs-networking-support"></a>Stöd för DPM/MABS-nätverk

### <a name="url-access"></a>URL-åtkomst

DPM-servern/MABS behöver åtkomst till dessa url:er:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Support för Azure ExpressRoute

Du kan säkerhetskopiera dina data via Azure ExpressRoute med offentlig peering (tillgänglig för gamla kretsar) och Microsoft-peering. Säkerhetskopiering via privat peering stöds inte.

Med offentlig peering: Se till att du får tillgång till följande domäner/adresser:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Med Microsoft-peering väljer du följande tjänster/regioner och relevanta community-värden:

* Azure Active Directory (12076:5060)
* Microsoft Azure-region (beroende på var ditt Recovery Services-valv finns)
* Azure Storage (beroende på platsen för ditt Recovery Services-valv)

Mer information finns i [Routningskraven för ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>Offentlig peering är föråldrad för nya kretsar.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS-anslutning till Azure Backup

Anslutning till Azure Backup-tjänsten krävs för att säkerhetskopior ska fungera korrekt och Azure-prenumerationen ska vara aktiv. I följande tabell visas beteendet om dessa två saker inte inträffar.

**MABS till Azure** | **Prenumeration** | **Säkerhetskopiering/återställning**
--- | --- | ---
Ansluten | Active | Säkerhetskopiera till DPM/MABS-disken.<br/><br/> Säkerhetskopiera till Azure.<br/><br/> Återställ från disken.<br/><br/> Återställ från Azure.
Ansluten | Utgångna/avetablerade | Ingen säkerhetskopiering till disk eller Azure.<br/><br/> Om prenumerationen har upphört att gälla kan du återställa från disk eller Azure.<br/><br/> Om prenumerationen är inaktiverad kan du inte återställa från disk eller Azure. Azure-återställningspunkterna tas bort.
Ingen anslutning på mer än 15 dagar | Active | Ingen säkerhetskopiering till disk eller Azure.<br/><br/> Du kan återställa från disk eller Azure.
Ingen anslutning på mer än 15 dagar | Utgångna/avetablerade | Ingen säkerhetskopiering till disk eller Azure.<br/><br/> Om prenumerationen har upphört att gälla kan du återställa från disk eller Azure.<br/><br/> Om prenumerationen är inaktiverad kan du inte återställa från disk eller Azure. Azure-återställningspunkterna tas bort.

## <a name="dpmmabs-storage-support"></a>DPM/MABS-lagringsstöd

Data som säkerhetskopieras till DPM/MABS lagras på lokal disklagring.

**Lagring** | **Detaljer**
--- | ---
**Mbs** | Modern lagring av säkerhetskopiering (MBS) stöds från DPM 2016/MABS v2 och senare. Den är inte tillgänglig för MABS v1.
**MABS-lagring på Azure VM** | Data lagras på Azure-diskar som är kopplade till DPM/MABS-datorn och som hanteras i DPM/MABS. Antalet diskar som kan användas för DPM/MABS-lagringspoolen begränsas av storleken på den virtuella datorn.<br/><br/> A2 VM: 4 diskar; A3 VM: 8 diskar; A4 VM: 16 diskar, med en maximal storlek på 1 TB för varje disk. Detta avgör den totala poolen för säkerhetskopieringslagring som är tillgänglig.<br/><br/> Hur mycket data du kan säkerhetskopiera beror på antalet och storleken på de anslutna diskarna.
**MABS-datalagring på Azure VM** | Vi rekommenderar att du behåller data under en dag på DPM/MABS Azure-disken och säkerhetskopierar från DPM/MABS till valvet för längre kvarhållning. Du kan på så sätt skydda en större mängd data genom att avlasta den till Azure Backup.

### <a name="modern-backup-storage-mbs"></a>Modern lagring av säkerhetskopiering (MBS)

Från DPM 2016/MABS v2 (som körs på Windows Server 2016) och senare kan du dra nytta av modern lagring av säkerhetskopiering (MBS).

- MBS-säkerhetskopior lagras på en ReFS-disk (Resilient File System).
- MBS använder ReFS-blockkloning för snabbare säkerhetskopiering och effektivare användning av lagringsutrymme.
- När du lägger till volymer i den lokala DPM/MABS-lagringspoolen konfigurerar du dem med enhetsbeteckningar. Du kan sedan konfigurera arbetsbelastningslagring på olika volymer.
- När du skapar skyddsgrupper för att säkerhetskopiera data till DPM/MABS väljer du den enhet du vill använda. Du kan till exempel lagra säkerhetskopior för SQL eller andra höga IOPS-arbetsbelastningar på en högpresterande enhet och lagra arbetsbelastningar som säkerhetskopieras mindre ofta på en enhet med lägre prestanda.

## <a name="supported-backups-to-mabs"></a>Säkerhetskopiering som stöds till MABS

Information om de olika servrar och arbetsbelastningar som du kan skydda med Azure Backup Server finns i [Azure Backup Server Protection Matrix](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Säkerhetskopieringar som stöds till DPM

Information om de olika servrar och arbetsbelastningar som du kan skydda med Data Protection Manager finns i artikeln [Vad kan DPM säkerhetskopiera?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- Klustrade arbetsbelastningar som backas upp av DPM/MABS bör finnas i samma domän som DPM/MABS eller i en underordnad/betrodd domän.
- Du kan använda NTLM/certifikatautentisering för att säkerhetskopiera data i domäner som inte är betrodda eller arbetsgrupper.

## <a name="next-steps"></a>Nästa steg

- [Läs mer](backup-architecture.md#architecture-back-up-to-dpmmabs) om MABS-arkitektur.
- [Granska](backup-support-matrix-mars-agent.md) vad som stöds för MARS-agenten.
- [Konfigurera](backup-azure-microsoft-azure-backup.md) en MABS-server.
- [Konfigurera DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
