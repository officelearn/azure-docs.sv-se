---
title: Stöd för VMware-utvärdering och migrering i Azure Migrate
description: Läs mer om stöd för utvärdering av virtuella VMware-datorer/migrering i Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 135680a9b0b6c8b5520958c884d99a83f1f87c88
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196281"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Stödmatris för utvärdering och migrering av VMware

Du kan använda [Azure Migrate](migrate-overview.md) för att utvärdera och migrera datorer till Microsoft Azure molnet. Den här artikeln sammanfattar support inställningar och begränsningar för att utvärdera och migrera lokala virtuella VMware-datorer.


## <a name="vmware-scenarios"></a>VMware-scenarier

Tabellen sammanfattar scenarier som stöds för virtuella VMware-datorer.

**Distribution** | **Information**
--- | ---
**Utvärdera lokala virtuella VMware-datorer** | [Konfigurera](tutorial-prepare-vmware.md) din första utvärdering.<br/><br/> [Kör](scale-vmware-assessment.md) en storskalig bedömning.
**Migrera virtuella VMware-datorer** | Du kan migrera med hjälp av en agent utan migrering eller använda en agent-baserad migrering. [Läs mer](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Azure Migrate projekt

**Support** | **Information**
--- | ---
**Azure-behörigheter** | Du måste ha deltagar-eller ägar behörigheter i prenumerationen för att kunna skapa ett Azure Migrate-projekt.
**VMware-begränsningar**  | Utvärdera upp till 35 000 virtuella VMware-datorer i ett enda projekt. Du kan skapa flera projekt i en Azure-prenumeration. Ett projekt kan innehålla både virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna.
**Geografi** | [Granska](migrate-support-matrix.md#supported-geographies) stödda geografiska områden.

**Geografi** | **Lagrings plats för metadata**
--- | ---
Azure Government | Virginia (USA-förvaltad region)
Asien och stillahavsområdet | Asien, östra eller Sydostasien
Australien | Östra Australien eller Australien, sydöstra
Brasilien | Södra Brasilien
Kanada | Kanada, centrala eller Kanada, öst
Europa | Europa, norra eller Europa, västra
Frankrike | Frankrike, centrala
Indien | Centrala Indien eller södra Indien
Japan |  Japan, östra eller Japan, väst
Korea | Korea, centrala eller Korea, södra
Storbritannien | Storbritannien, södra eller Storbritannien, västra
USA | USA, centrala eller västra USA 2


 > [!NOTE]
 > Stöd för Azure Government är för närvarande bara tillgängligt för den [äldre versionen](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) av Azure Migrate.


## <a name="application-discovery"></a>Program identifiering

Azure Migrate: Server utvärdering kan identifiera appar, roller och funktioner. Genom att identifiera din program inventering kan du identifiera och planera en sökväg för migrering som är anpassad för dina lokala arbets belastningar. Azure Migrate: Server utvärderingen tillhandahåller identifiering utan agent, med autentiseringsuppgifter för dator gäst, fjärråtkomst till datorer med WMI och SSH-anrop.

**Support** | **Information**
--- | ---
Datorer som stöds | Lokala virtuella VMware-datorer
Datorns operativ system | Alla Windows-och Linux-versioner
Autentiseringsuppgifter | För närvarande har stöd för att använda en autentiseringsuppgift för alla Windows-servrar och en autentiseringsuppgift för alla Linux-servrar. Du skapar ett gäst användar konto för virtuella Windows-datorer och ett vanligt/vanligt användar konto (icke-sudo åtkomst) för alla virtuella Linux-datorer.
Dator gränser för app-Discovery | 10000 per apparat. 35000 per projekt

## <a name="assessment-vcenter-server-requirements"></a>Utvärdering – vCenter Server krav

I den här tabellen sammanfattas utvärderings support och begränsningar för VMware virtualization-servrar.

**Support** | **Information**
--- | ---
**vCenter-server** | Virtuella VMware-datorer som du vill utvärdera måste hanteras av en eller flera vCenter-servrar som kör 5,5, 6,0, 6,5 eller 6,7.

## <a name="assessment-vcenter-server-permissions"></a>Utvärdering – vCenter Server behörigheter

Azure Migrate behöver åtkomst till vCenter Server för att identifiera virtuella datorer för utvärdering och agent lös migrering.

- Om du planerar att identifiera program eller visualisera beroenden på ett agent sätt skapar du ett vCenter Server-konto med skrivskyddad åtkomst tillsammans med de behörigheter som har Aktiver ATS för **virtuella datorer** > **gäst åtgärder**.

  ![vCenter Server konto privilegier](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Om du inte planerar att utföra program identifiering och visualisering av beroenden för agenter, ställer du in ett skrivskyddat konto för vCenter Server.

## <a name="assessment-appliance-requirements"></a>Bedömnings krav

Azure Migrate kör en förenklad installation för att identifiera virtuella VMware-datorer och skicka VM-metadata och prestanda data till Azure Migrate. Installation av VMware distribueras med hjälp av en områdesmall som importer ATS till vCenter Server. I följande tabell sammanfattas kraven på installationen.

**Support** | **Information**
--- | ---
**Distribution av utrustning** | Du distribuerar installationen som en virtuell VMware-dator. Du behöver tillräckligt med resurser på vCenter Server för att allokera en virtuell dator med 32 GB RAM, 8 virtuella processorer, cirka 80 GB disk lagring och en extern virtuell växel.<br/><br/> Enheten kräver Internet åtkomst, antingen direkt eller via en proxyserver.<br/> Den virtuella datorn måste distribueras på en ESXi-värd som kör version 5,5 eller senare.
**Azure Migrate projekt** | En apparat kan associeras med ett enda projekt. <br/> Valfritt antal enheter kan associeras med ett enda projekt.<br/> Du kan utvärdera upp till 35 000 virtuella datorer i ett projekt.
**Identifikation** | En apparat kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter Server.<br/> En apparat kan ansluta till en enda vCenter Server.
**Utvärderings grupp** | Du kan lägga till upp till 35 000 datorer i en enda grupp.
**Utvärdering** | Du kan utvärdera upp till 35 000 virtuella datorer i en enda utvärdering.


## <a name="assessment-url-access-requirements"></a>Bedömnings krav för URL-åtkomst

Azure Migrate-utrustningen behöver anslutning till Internet.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad proxy för att ansluta till Internet kan du ge åtkomst till dessa URL: er och se till att proxyservern matchar eventuella CNAME-poster som tas emot när URL: erna genomsöks.

**URL** | **Information**  
--- | --- |
*.portal.azure.com  | Navigera till Azure Migrate i Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Logga in till din Azure-prenumeration.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
management.azure.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
dc.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.
*.servicebus.windows.net | Kommunikation mellan installations programmet och tjänsten Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate tjänst-URL: er.
*.blob.core.windows.net | Ladda upp data till lagrings konton.
https://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Används för uppdateringar av Azure Migrates enheten.

## <a name="assessment-port-requirements"></a>Utvärdering-port krav

**Anordningar** | **Anslutning**
--- | ---
Enhet | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/><br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med URL: en: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Utgående anslutningar på port 443, 5671 och 5672 för att skicka identifierings-och prestanda-metadata till Azure Migrate.
vCenter Server | Inkommande anslutningar på TCP-port 443 för att tillåta att installationen samlar in konfigurations-och prestanda-metadata för utvärderingar. <br/><br/> Enheten ansluter som standard till vCenter på port 443. Om vCenter-servern lyssnar på en annan port kan du ändra porten när du konfigurerar identifiering.

## <a name="assessment-dependency-visualization"></a>Bedömning-beroende visualisering

Beroende visualisering hjälper dig att visualisera beroenden mellan datorer som du vill utvärdera och migrera. Du använder vanligt vis beroende mappning när du vill utvärdera datorer med högre Tillförlitlighets nivåer. För virtuella VMware-datorer stöds beroende visualisering enligt följande:

- **Beroende visualisering för agent utan agent**: det här alternativet är för närvarande en för hands version. Du behöver inte installera några agenter på datorer.
    - Det fungerar genom att samla in data för TCP-anslutningen från datorer som den är aktive rad för. När beroende identifiering har startats samlar enheten in data från datorer vid ett avsöknings intervall på fem minuter.
    - Följande data samlas in:
        - TCP-anslutningar
        - Namn på processer som har aktiva anslutningar
        - Namn på installerade program som kör ovanstående processer
        - Nej. anslutningar som identifieras vid varje avsöknings intervall
- **Agent-baserad beroende visualisering**: om du vill använda en agent-baserad beroende visualisering måste du hämta och installera följande agenter på varje lokal dator som du vill analysera.
    - Microsoft Monitoring Agent (MMA) måste installeras på varje dator. [Läs mer](how-to-create-group-machine-dependencies.md#install-the-mma) om hur du installerar MMA-agenten.
    - Beroende agenten måste installeras på varje dator. [Läs mer](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) om hur du installerar beroende agenten.
    - Om du har datorer utan Internetanslutning måste du dessutom ladda ned och installera Log Analytics-gatewayen på dem.

## <a name="migration---limitations"></a>Migration-begränsningar
Du kan välja upp till 10 virtuella datorer på en gång för replikering. Om du vill migrera fler datorer kan du replikera i grupper om 10. För VMware-agent lös migrering kan du köra upp till 100 replikeringar samtidigt.

## <a name="agentless-migration-vmware-server-requirements"></a>Agent lös migrering – krav för VMware-servrar

I den här tabellen sammanfattas utvärderings support och begränsningar för VMware virtualization-servrar.

**Support** | **Information**
--- | ---
vCenter Server | Version 5,5, 6,0, 6,5 eller 6,7.
VMware vSphere | Version 5,5, 6,0, 6,5 eller 6,7,

## <a name="agentless-migration-vcenter-server-permissions"></a>Agent utan migrering – vCenter Server behörigheter

**Behörigheter** | **Information**
--- | ---
Datastore.Browse | Tillåt bläddring av VM-loggfiler för att felsöka skapande och borttagning av ögonblicks bilder.
Datastore.LowLevelFileOperations | Tillåt Läs-/skriv-/borttagnings-/namnbytes åtgärder i data lager läsaren för att felsöka skapande och borttagning av ögonblicks bilder.
VirtualMachine.Configuration.DiskChangeTracking | Tillåt aktivering eller inaktive ring av ändrings spårning av virtuella dator diskar, för att hämta ändrade block med data mellan ögonblicks bilder
VirtualMachine.Configuration.DiskLease | Tillåt disk låne åtgärder för en virtuell dator för att läsa disken med hjälp av VMware vSphere Virtual Disk Development Kit (VDDK).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Tillåt att en disk öppnas på en virtuell dator för att läsa disken med hjälp av VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Tillåter Läs åtgärder på filer som är associerade med en virtuell dator, för att ladda ned loggarna och felsöka om det uppstår fel.
VirtualMachine.SnapshotManagement.* | Tillåt skapande och hantering av VM-ögonblicksbilder för replikering.
Virtuell dator. interaktion. avstängning | Tillåt att den virtuella datorn stängs av under migreringen till Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Agent utan virtuell migrering – krav för VMware VM

**Support** | **Information**
--- | ---
**Operativsystem som stöds** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem som stöds av Azure kan migreras med hjälp av en migrering utan agent.
**Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för följande operativ system:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> För andra operativ system måste du göra justeringar manuellt innan migreringen. Relevanta artiklar innehåller instruktioner om hur du gör detta.
**Linux-start** | Om/boot finns på en dedikerad partition bör den finnas på OS-disken och inte spridas över flera diskar.<br/> Om/Boot är en del av rot-partitionen (/) bör partitionen/-partitionen finnas på OS-disken och inte omfatta andra diskar.
**UEFI-start** | Virtuella datorer med UEFI-start stöds inte för migrering.
**Disk storlek** | 2 TB OS-disk; 4 TB för data diskar.
**Disk gränser** |  Upp till 60 diskar per virtuell dator.
**Krypterade diskar/volymer** | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
**Delat disk kluster** | Stöds ej.
**Oberoende diskar** | Stöds ej.
**RDM/passthrough-diskar** | Om de virtuella datorerna har RDM-eller passthrough-diskar replikeras inte dessa diskar till Azure.
**NFS** | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte.
**iSCSI-mål** | Virtuella datorer med iSCSI-mål stöds inte för migrering utan agent.
**Multipath i/o** | Stöds ej.
**Lagrings vMotion** | Stöds ej. Det går inte att använda replikering om en virtuell dator använder Storage vMotion.
**Grupperade nätverkskort** | Stöds ej.
**IPv6** | Stöds ej.
**Mål disk** | Virtuella datorer kan bara migreras till Managed disks (standard HDD, Premium SSD) i Azure.
**Samtidig replikering** | 100 virtuella datorer per vCenter Server. Om du har fler kan du migrera dem i batchar på 100.


## <a name="agentless-migration-appliance-requirements"></a>Krav för migrering utan agent


**Support** | **Information**
--- | ---
**ESXi** | Den virtuella datorn måste distribueras på en ESXi-värd som kör version 5,5 eller senare.
**Azure Migrate projekt** | En apparat kan associeras med ett enda projekt.
**vCenter Server** | En apparat kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter Server.<br/> En installation kan ansluta till en vCenter Server.
**VDDK** | Om du kör en agent lös migrering med Azure Migrate Server-migrering, måste VMware vSphere VDDK installeras på den virtuella datorn.

## <a name="agentless-migration-url-access-requirements"></a>Åtkomst krav för agent utan agent-URL

Azure Migrate-enheten behöver Internet anslutning till Internet.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad proxy kan du ge åtkomst till dessa URL: er och se till att proxyservern matchar eventuella CNAME-poster som tas emot när URL: erna genomsöks.

**URL** | **Information**  
--- | ---
*.portal.azure.com | Navigera till Azure Migrate i Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Logga in till din Azure-prenumeration.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
management.azure.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
dc.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.
*.servicebus.windows.net | Kommunikation mellan installations programmet och tjänsten Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate tjänst-URL: er.
*.blob.core.windows.net | Ladda upp data till lagrings konton.
https://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Används för uppdateringar av Azure Migrates enheten.


## <a name="agentless-migration-port-requirements"></a>Agent utan agent-migrering – port krav

**Anordningar** | **Anslutning**
--- | ---
Enhet | Utgående anslutningar på port 443 för att överföra replikerade data till Azure och för att kommunicera med Azure Migrate tjänster som dirigerar replikering och migrering.
vCenter Server | Inkommande anslutningar på port 443 för att tillåta att enheten dirigerar replikering – skapa ögonblicks bilder, kopiera data, versions ögonblicks bilder
vSphere/EXSI-värd | Inkommande på TCP-port 902 för att enheten ska replikera data från ögonblicks bilder.


## <a name="agent-based-migration-vmware-server-requirements"></a>Agent-baserad migrering – krav för VMware-servrar

I den här tabellen sammanfattas utvärderings support och begränsningar för VMware virtualization-servrar.

**Support** | **Information**
--- | ---
vCenter Server | Version 5,5, 6,0, 6,5 eller 6,7.
VMware vSphere | Version 5,5, 6,0, 6,5 eller 6,7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Agent-baserad migrering – vCenter Server behörigheter

Ett skrivskyddat konto för vCenter Server.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Krav för agent-baserad migrering-replikering

Kraven för den [replikeringsprincip](migrate-replication-appliance.md) som används för agent-baserad migrering av virtuella VMware-datorer och fysiska servrar med Azure Migrate Server-migrering sammanfattas i tabellen.

> [!NOTE]
> När du ställer in replikeringen med hjälp av den ägg-mall som finns i Azure Migrate Hub, kör enheten Windows Server 2016 och uppfyller support kraven. Om du konfigurerar replikeringen manuellt på en fysisk server kontrollerar du att den uppfyller kraven.



**Komponent** | **Krav**
--- | ---
 | **VMware-inställningar** (VMware VM-installation)
PowerCLI | [PowerCLI version 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) bör installeras om Replikeringshanteraren körs på en virtuell VMware-dator.
Typ av nätverkskort | VMXNET3 (om installationen är en virtuell VMware-dator)
 | **Maskin varu inställningar**
Processorkärnor | 8
RAM | 16 GB
Antal diskar | Tre: OS-disken, cache-disken för processervern och lagrings enheten.
Ledigt disk utrymme (cache) | 600 GB
Ledigt disk utrymme (kvarhållning av disk) | 600 GB
**Program varu inställningar** |
Operativsystem | Windows Server 2016 eller Windows Server 2012 R2
Nationella inställningar för operativsystem | Engelska (en-us)
TLS | TLS 1,2 ska vara aktiverat.
.NET Framework | .NET Framework 4,6 eller senare bör installeras på datorn (med stark kryptering aktiverat.
MySQL | MySQL bör installeras på enheten.<br/> MySQL ska installeras. Du kan installera manuellt, eller så kan Site Recovery installera det under installationen av produkten.
Andra appar | Kör inte andra appar på replikerings enheten.
Windows Server-roller | Aktivera inte följande roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Grup principer | Aktivera inte dessa grup principer: <br> -Förhindra åtkomst till kommando tolken. <br> -Förhindra åtkomst till verktyg för redigering av registret. <br> – Förtroende logik för bifogade filer. <br> – Aktivera skript körning. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Ingen befintlig standard webbplats <br> -Ingen befintlig webbplats/program som lyssnar på port 443 <br>-Aktivera [Anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -inställning
**Nätverks inställningar** |
IP-adresstyp | Statisk
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)
Typ av nätverkskort | VMXNET3

### <a name="replication-appliance-url-access"></a>URL-åtkomst till replikeringsfil

Replication-enheten behöver åtkomst till dessa URL: er.

**URL** | **Information**
--- | ---
\*.backup.windowsazure.com | Används för replikerad data överföring och samordning
\*.store.core.windows.net | Används för replikerad data överföring och samordning
\*.blob.core.windows.net | Används för att komma åt lagrings kontot som lagrar replikerade data
\*.hypervrecoverymanager.windowsazure.com | Används för hanterings åtgärder och samordning av replikering
https:\//management.azure.com | Används för hanterings åtgärder och samordning av replikering
*.services.visualstudio.com | Används för telemetri (är valfritt)
time.nist.gov | Används för att kontrollera tidssynkronisering mellan system och global tid.
time.windows.com | Används för att kontrollera tidssynkronisering mellan system och global tid.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVF-installationen behöver åtkomst till dessa URL: er. De används för åtkomst kontroll och identitets hantering genom Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Slutföra MySQL-nedladdning


#### <a name="mysql-installation-options"></a>Installations alternativ för MySQL

MySQL kan installeras på replikeringsprincipen med någon av dessa metoder.

**Metod** | **Information**
--- | ---
Ladda ned och installera manuellt | Hämta MySQL-programmet & Placera det i mappen C:\Temp\ASRSetup och installera manuellt.<br/> När du konfigurerar installationen av MySQL visas som redan installerad.
Utan nedladdning online | Placera programmet MySQL installations program i mappen C:\Temp\ASRSetup. När du installerar installationen och klickar för att ladda ned och installera MySQL använder installations programmet det installations program som du har lagt till.
Hämta och installera i Azure Migrate | När du installerar installationen och tillfrågas om MySQL väljer du **Ladda ned och installera**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Agent-baserad migrering – krav för VMware VM

**Support** | **Information**
--- | ---
**Dator arbets belastning** | Azure Migrate stöder migrering av arbets belastningar (t. ex. Active Directory, SQL Server osv.) som körs på en dator som stöds.
**Operativ system** | Du hittar den senaste informationen i [operativ systemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) för Site Recovery. Azure Migrate tillhandahåller identiskt stöd för virtuella dator operativ system.
**Linux-filsystem/gäst lagring** | Du hittar den senaste informationen i [Linux-filsystemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) för Site Recovery. Azure Migrate har samma stöd för Linux-filsystem.
**Nätverk/lagring** | För den senaste informationen granskar du kraven för [nätverk](../site-recovery/vmware-physical-azure-support-matrix.md#network) och [lagring](../site-recovery/vmware-physical-azure-support-matrix.md#storage) för Site Recovery. Azure Migrate tillhandahåller identiska nätverks-/lagrings krav.
**Krav för Azure** | Du hittar den senaste informationen i [Azure-nätverket](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [lagrings](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)-och [beräknings](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) kraven för Site Recovery. Azure Migrate har identiska krav för VMware-migrering.
**Mobilitetstjänsten** | Mobilitets tjänst agenten måste vara installerad på varje virtuell dator som du vill migrera.
**UEFI-start** | Den migrerade virtuella datorn i Azure kommer automatiskt att konverteras till en virtuell dator med BIOS-start.<br/><br/> OS-disken bör ha upp till fyra partitioner och volymer ska formateras med NTFS.
**Mål disk** | Virtuella datorer kan bara migreras till Managed disks (standard HDD, Premium SSD) i Azure.
**Disk storlek** | 2 TB OS-disk; 8 TB för data diskar.
**Disk gränser** |  Upp till 63 diskar per virtuell dator.
**Krypterade diskar/volymer** | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
**Delat disk kluster** | Stöds ej.
**Oberoende diskar** | Stöds.
**Genom strömnings diskar** | Stöds.
**NFS** | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte.
**iSCSI-mål** | Virtuella datorer med iSCSI-mål stöds inte för migrering utan agent.
**Multipath i/o** | Stöds ej.
**Lagrings vMotion** | Stöds
**Grupperade nätverkskort** | Stöds ej.
**IPv6** | Stöds ej.




## <a name="agent-based-migration-url-access-requirements"></a>Agent-baserad migrering-URL åtkomst krav

Mobilitets tjänsten som körs på virtuella VMware-datorer måste vara ansluten till Internet.

När du distribuerar mobilitets tjänsten kontrollerar den anslutningen till URL: erna som sammanfattas i tabellen nedan.


**URL** | **Information**  
--- | ---
*.portal.azure.com | Navigera till Azure Migrate i Azure Portal.
*.windows.net | Logga in till din Azure-prenumeration.
*.microsoftonline.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
management.azure.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
dc.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.
*.servicebus.windows.net | Kommunikation mellan installations programmet och tjänsten Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate tjänst-URL: er.
*.blob.core.windows.net | Ladda upp data till lagrings konton.

## <a name="agent-based-migration-port-requirements"></a>Agent-baserad migrering – port krav

**Anordningar** | **Anslutning**
--- | ---
Virtuella datorer | Mobilitets tjänsten som körs på virtuella datorer kommunicerar med den lokala replikerings enheten (konfigurations servern) på port HTTPS 443 inkommande, för hantering av replikering.<br/><br/> Virtuella datorer skickar replikeringsdata till processervern (körs på konfigurations serverdatorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
Replikeringsfil | Replikeringstjänsten dirigerar replikering med Azure över Port HTTPS 443 utgående.
Processerver | Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.<br/> Som standard körs processervern på replikerings enheten.

## <a name="azure-vm-requirements"></a>Krav för virtuell Azure-dator

Alla lokala virtuella datorer som replikeras till Azure måste uppfylla de krav för virtuella Azure-datorer som sammanfattas i den här tabellen. När Site Recovery kör en krav kontroll för replikering kommer kontrollen att Miss klar om några av kraven inte uppfylls.

**Komponent** | **Krav** | **Information**
--- | --- | ---
Gäst operativ system | Kontrol lera att operativ system som stöds för [virtuella VMware-datorer med hjälp av replikering utan agent](#agentless-migration-vmware-vm-requirements)och för [virtuella VMware-datorer som använder agent-baserad replikering](#agent-based-migration-vmware-vm-requirements).<br/> Du kan migrera alla arbets belastningar som körs på ett operativ system som stöds. | Kontrollen Miss lyckas om den inte stöds.
Gäst operativ systemets arkitektur | 64-bitars. | Kontrollen Miss lyckas om den inte stöds.
Storlek på operativ system disk | Upp till 2 048 GB. | Kontrollen Miss lyckas om den inte stöds.
Antal operativ system diskar | 1 | Kontrollen Miss lyckas om den inte stöds.
Antal data diskar | 64 eller mindre. | Kontrollen Miss lyckas om den inte stöds.
Data disk storlek | Upp till 4 095 GB | Kontrollen Miss lyckas om den inte stöds.
Nätverkskort | Flera nätverkskort stöds. |
Delad virtuell hård disk | Stöds ej. | Kontrollen Miss lyckas om den inte stöds.
FC-disk | Stöds ej. | Kontrollen Miss lyckas om den inte stöds.
BitLocker | Stöds ej. | BitLocker måste inaktive ras innan du aktiverar replikering för en dator.
VM-namn | Mellan 1 och 63 tecken.<br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Dator namnet måste börja och sluta med en bokstav eller en siffra. |  Uppdatera värdet i dator egenskaperna i Site Recovery.
Anslut efter migreringen – Windows | Ansluta till virtuella Azure-datorer som kör Windows efter migrering:<br/> -Innan migreringen aktiverar RDP på den lokala virtuella datorn. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.<br/> För plats-till-plats-VPN-åtkomst aktiverar du RDP och Tillåt RDP i **Windows-brandväggen** -> **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk. Dessutom kontrollerar du att operativ systemets SAN-princip är inställd på **OnlineAll**. [Läs mer](prepare-for-migration.md). |
Anslut efter migreringen – Linux | Ansluta till virtuella Azure-datorer efter migrering med SSH:<br/> Innan migreringen går du till den lokala datorn, kontrollerar att tjänsten Secure Shell är inställt på Start och att brand Väggs reglerna tillåter en SSH-anslutning.<br/> Efter redundansväxlingen på den virtuella Azure-datorn tillåter du inkommande anslutningar till SSH-porten för reglerna för nätverks säkerhets grupper på den misslyckade virtuella datorn och för det Azure-undernät som den är ansluten till. Lägg också till en offentlig IP-adress för den virtuella datorn. |  


## <a name="next-steps"></a>Nästa steg

[Förbered för VMware](tutorial-prepare-vmware.md) -utvärdering och migrering.
