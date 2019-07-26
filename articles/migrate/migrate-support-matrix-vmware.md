---
title: Azure Migrate support mat ris för VMware-utvärdering och migrering
description: Sammanfattar support inställningar och begränsningar för utvärdering och migrering av virtuella VMware-datorer till Azure med hjälp av tjänsten Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 00ca474a6cb32c7ad3e47aef750126e958e43501
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372442"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Stödmatris för utvärdering och migrering av VMware

Du kan använda [tjänsten Azure Migrate](migrate-overview.md) för att utvärdera och migrera datorer till Microsoft Azure molnet. Den här artikeln sammanfattar support inställningar och begränsningar för att utvärdera och migrera lokala virtuella VMware-datorer.


## <a name="vmware-scenarios"></a>VMware-scenarier

Tabellen sammanfattar scenarier som stöds för virtuella VMware-datorer.

**Distribution** | **Detaljer**
--- | ---
**Utvärdera lokala virtuella VMware-datorer** | [Konfigurera](tutorial-prepare-vmware.md) din första utvärdering.<br/><br/> [Kör](scale-vmware-assessment.md) en storskalig bedömning.
**Migrera virtuella VMware-datorer** | Du kan migrera med hjälp av en agent utan migrering, med vissa begränsningar eller använda en agent-baserad migrering. [Läs mer](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Azure Migrate projekt

**Support** | **Detaljer**
--- | ---
Azure-behörigheter | Du måste ha deltagar-eller ägar behörigheter i prenumerationen för att kunna skapa ett Azure Migrate-projekt.
VMware-begränsningar  | Utvärdera upp till 35 000 virtuella VMware-datorer i ett enda projekt.

Ett projekt kan innehålla både virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna.

**Placering** Det finns ett antal geografiska områden där ett Azure Migrate-projekt kan skapas. Även om du bara kan skapa projekt i dessa geografiska områden kan du fortfarande utvärdera eller migrera dina datorer för andra mål platser. Projektets geografi används bara för att lagra identifierade metadata.


 **Geografi** | **Lagrings plats för metadata**
 --- | ---
 Azure Government | Virginia (USA-förvaltad region)
 Asien och stillahavsområdet | Sydostasien eller Asien, östra
 Europa | Europa, södra eller Västeuropa
 Storbritannien och Nordirland | Storbritannien, södra eller Storbritannien, västra
 USA | USA, centrala eller västra USA 2


 > [!NOTE]
 > Stöd för Azure Government är för närvarande bara tillgängligt för den [äldre versionen](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) av Azure Migrate.


## <a name="assessment-vmware-server-requirements"></a>Bedömning – krav för VMware-servrar

I den här tabellen sammanfattas utvärderings support och begränsningar för VMware virtualization-servrar.

**Support** | **Detaljer**
--- | ---
**vCenter-server** | Virtuella VMware-datorer som du vill utvärdera måste hanteras av en eller flera vCenter-servrar som kör 5,5, 6,0, 6,5 eller 6,7.

## <a name="assessment-vcenter-server-permissions"></a>Utvärdering – vCenter Server behörigheter

För utvärdering behöver du ett skrivskyddat konto för vCenter Server.

## <a name="assessment-appliance-requirements"></a>Bedömnings krav

**Support** | **Detaljer**
--- | ---
**ESXi** | Den virtuella datorn måste distribueras på en ESXi-värd som kör version 5,5 eller senare.
**Azure Migrate projekt** | En apparat kan associeras med ett enda projekt.
**vCenter Server** | En apparat kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter Server.<br/> En installation kan ansluta till en vCenter Server.


## <a name="assessment-url-access-requirements"></a>Bedömnings krav för URL-åtkomst

Azure Migrate-enheten behöver Internet anslutning till Internet.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad brand vägg. proxy kan du ge åtkomst till dessa URL: er och se till att proxyservern matchar eventuella CNAME-poster som tas emot när URL: erna genomsöks.

**URL** | **Detaljer**  
--- | --- |
*.portal.azure.com  | Navigera till Azure Migrate i Azure Portal.
*.windows.net | Logga in till din Azure-prenumeration.
*.microsoftonline.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
management.azure.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
dc.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.
*.servicebus.windows.net | Kommunikation mellan installations programmet och tjänsten Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate tjänst-URL: er.
*.blob.core.windows.net | Ladda upp data till lagrings konton.


## <a name="assessment-port-requirements"></a>Utvärdering-port krav

**Anordningar** | **anslutning**
--- | ---
Installation | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/> Inkommande anslutningar på port 44368 för fjärråtkomst till program hanterings appen med URL: en: https://<-IP-eller-Name >: 44368 <br/>Utgående anslutningar på port 443 för att skicka metadata för identifiering och prestanda till Azure Migrate.
vCenter Server | Inkommande anslutningar på TCP-port 443 för att tillåta att installationen samlar in konfigurations-och prestanda-metadata för utvärderingar. <br/> Enheten ansluter som standard till vCenter på port 443. Om vCenter-servern lyssnar på en annan port kan du ändra porten när du konfigurerar identifiering.


## <a name="agentless-migration-vmware-server-requirements"></a>Agent lös migrering – krav för VMware-servrar

I den här tabellen sammanfattas utvärderings support och begränsningar för VMware virtualization-servrar.

**Support** | **Detaljer**
--- | ---
**vCenter-server** | Virtuella VMware-datorer som du migrerar med hjälp av en agent-migrering måste hanteras av en eller flera vCenter-servrar som kör 5,5, 6,0, 6,5 eller 6,7.

## <a name="agentless-migration-vcenter-server-permissions"></a>Agent utan migrering – vCenter Server behörigheter

**Behörigheter** | **Detaljer**
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

**Support** | **Detaljer**
--- | ---
**Operativsystem som stöds** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem som stöds av Azure kan migreras med hjälp av en migrering utan agent.
**Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för följande operativ system:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> För andra operativ system måste du göra justeringar manuellt innan migreringen. Relevanta artiklar innehåller instruktioner om hur du gör detta.
**Linux-start** | Om/boot finns på en dedikerad partition bör den finnas på OS-disken och inte spridas över flera diskar.<br/> Om/Boot är en del av rot-partitionen (/) bör partitionen/-partitionen finnas på OS-disken och inte omfatta andra diskar.
**UEFI-start** | Virtuella datorer med UEFI-start stöds inte för migrering.
**Krypterade diskar/volymer** | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
**RDM/passthrough-diskar** | Om de virtuella datorerna har RDM-eller passthrough-diskar replikeras inte dessa diskar till Azure.
**NFS** | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte.
**Mål disk** | Virtuella datorer kan bara migreras till Managed disks (standard HHD, Premium SSD) i Azure.


## <a name="agentless-migration-appliance-requirements"></a>Krav för migrering utan agent


**Support** | **Detaljer**
--- | ---
**ESXi** | Den virtuella datorn måste distribueras på en ESXi-värd som kör version 5,5 eller senare.
**Azure Migrate projekt** | En apparat kan associeras med ett enda projekt.
**vCenter Server** | En apparat kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter Server.<br/> En installation kan ansluta till en vCenter Server.
**VDDK** | Om du kör en migrering utan agent med Azure Migrate Server-migrering, måste VMware vSphere Virtual Disk Development Kit (VDDK) installeras på den virtuella datorn.

## <a name="agentless-migration-url-access-requirements"></a>Åtkomst krav för agent utan agent-URL

Azure Migrate-enheten behöver Internet anslutning till Internet.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad brand vägg. proxy kan du ge åtkomst till dessa URL: er och se till att proxyservern matchar eventuella CNAME-poster som tas emot när URL: erna genomsöks.

**URL** | **Detaljer**  
--- | ---
*.portal.azure.com | Navigera till Azure Migrate i Azure Portal.
*.windows.net | Logga in till din Azure-prenumeration.
*.microsoftonline.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
management.azure.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
dc.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.
*.servicebus.windows.net | Kommunikation mellan installations programmet och tjänsten Azure Migrate.
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate tjänst-URL: er.
*.blob.core.windows.net | Ladda upp data till lagrings konton.


## <a name="agentless-migration-port-requirements"></a>Agent utan agent-migrering – port krav

**Anordningar** | **anslutning**
--- | ---
Installation | Utgående TCP-port 3389 för att överföra replikerade data till Azure och för att kommunicera med Azure Migrate för replikering och migrering.
vCenter Server | Inkommande anslutningar på TCP-port 443 för att tillåta att enheten dirigerar replikering – skapa ögonblicks bilder, kopiera data, versions ögonblicks bilder
vSphere/EXSI-värd | Inkommande på TCP-port 902 för att enheten ska replikera data från ögonblicks bilder.


## <a name="agent-based-migration-vmware-server-requirements"></a>Agent-baserad migrering – krav för VMware-servrar

I den här tabellen sammanfattas utvärderings support och begränsningar för VMware virtualization-servrar.

**Support** | **Detaljer**
--- | ---
**vCenter-Server/ESXI** | Virtuella VMware-datorer som du migrerar måste hanteras av en eller flera vCenter-servrar som kör 5,5, 6,0, 6,5 eller 6,7, eller som körs på en ESXI-värd med vSphere version 5,5, 6,0, 6,5 eller 6,7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Agent-baserad migrering – vCenter Server behörigheter

**Behörigheter** | **Detaljer**
--- | ---
Data lager. AllocateSpace | Tillåt utrymmes tilldelning på ett data lager för en virtuell dator, ögonblicks bild, kloning eller virtuell disk.
Datastore.Browse | Tillåt bläddring av VM-loggfiler för att felsöka skapande och borttagning av ögonblicks bilder.
Datastore.LowLevelFileOperations | Tillåt åtgärder för att läsa, skriva, ta bort och byta namn i listan över data lager för att felsöka skapande/borttagning av ögonblicks bilder.
Datastore.UpdateVirtualMachineFiles | Tillåt uppdatering av sökvägar till VM-filer i ett data lager efter att data lagret har omsignerats.
Network. AssignNetwork | Tillåt att ett nätverk tilldelas en VM-resurs.
AssignVirtualMachineToResourcePool | Tillåt tilldelning av en virtuell dator till en resurspool.
Resource.MigratePoweredOffVirtualMachine | Tillåt migrering av en avstängd virtuell dator till en annan resurspool eller värd.
Resource.MigratePoweredOnVirtualMachine | Tillåt migrering med vMotion, med en inbyggd virtuell dator till en annan resurspool eller värd.
Uppgifter. CreateTask | Tillåt ett tillägg för att skapa en användardefinierad uppgift.
Uppgifter. UpdateTask | Tillåt ett tillägg att uppdatera en användardefinierad uppgift.
VirtualMachine.Configuration. | Tillåt konfigurering av VM-alternativ och-enheter.
Virtuell Machine. action. AnswerQuestion | Tillåt lösning av problem med överföringar av VM-tillstånd eller körnings fel.
Virtual Machine.Interaction.DeviceConnection | Tillåt ändring av anslutet tillstånd för en virtuell dators frånkopplade virtuella enheter.
Virtuell Machine. action. ConfigureCDMedia | Tillåt konfiguration av en virtuell DVD-eller CD-ROM-enhet.
Virtuell Machine. action. ConfigureFloppyMedia | Tillåt konfiguration av en virtuell diskett enhet.
Virtual Machine.Interaction.PowerOff | Tillåter att den virtuella datorn stängs av under migreringen till Azure.
Virtual Machine.Interaction.PowerOn | Tillåt strömförsörjning på en avstängd virtuell dator och återuppta en pausad virtuell dator.
Virtual Machine.Interaction.VMwareToolsInstall | Tillåt montering och demontering av VMware Tools CD Installer som CD-ROM för gäst operativ systemet.
VirtualMachine.Inventory.CreateNew | Tillåt att en virtuell dator och tilldelning av nödvändiga resurser skapas.
VirtualMachine.Inventory.Register | Tillåt att du lägger till en befintlig virtuell dator till en vCenter Server eller värd lager.
VirtualMachine.Inventory.Unregister | Tillåt avregistrering av en VMe från en vCenter Server eller värd inventering.
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | Tillåt Skriv åtgärder på filer som är associerade med en virtuell dator, inklusive VMX, diskar, loggar och NVRAM.
VirtualMachine.Provisioning.AllowVirtualMachineDownload | Tillåt Läs åtgärder på filer som är associerade med en virtuell dator för att hämta loggarna för fel sökning.
VirtualMachine.SnapshotManagement.RemoveSnapshot | Tillåt borttagning av en ögonblicks bild från ögonblicks bilds historiken.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Krav för agent-baserad migrering-replikering

Kraven för den [replikeringsprincip](migrate-replication-appliance.md) som används för agent-baserad migrering av virtuella VMware-datorer och fysiska servrar med Azure Migrate Server-migrering sammanfattas i tabellen.

> [!NOTE]
> När du ställer in replikeringen med hjälp av den ägg-mall som finns i Azure Migrate Hub, kör enheten Windows Server 2016 och uppfyller support kraven. Om du konfigurerar replikeringen manuellt på en fysisk server kontrollerar du att den uppfyller kraven.



**Komponent** | **Krav**
--- | ---
 | **VMware-inställningar** (VMware VM-installation)
**PowerCLI** | [PowerCLI version 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) bör installeras om Replikeringshanteraren körs på en virtuell VMware-dator.
**Typ av nätverkskort** | VMXNET3 (om installationen är en virtuell VMware-dator)
 | **Maskin varu inställningar**
Processorkärnor | 8
RAM | 16 GB
Antal diskar | Tre OS-disk, cache-disk för Server och lagrings enhet.
Ledigt disk utrymme (cache) | 600 GB
Ledigt disk utrymme (kvarhållning av disk) | 600 GB
**Program varu inställningar** |
Operativsystem | Windows Server 2016 eller Windows Server 2012 R2
Nationella inställningar för operativsystem | Engelska (en-us)
TLS | TLS 1,2 ska vara aktiverat.
.NET Framework | .NET Framework 4,6 eller senare bör installeras på datorn (med stark kryptering aktiverat.
MySQL | MySQL bör installeras på enheten.<br/> MySQL ska installeras. Du kan installera manuellt, eller så kan Site Recovery installera det under installationen av produkten.
Övriga appar | Du bör inte köra andra appar på replikerings enheten.
Windows Server-roller | Aktivera inte följande roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Grup principer | Aktivera inte dessa grup principer: <br> -Förhindra åtkomst till kommando tolken. <br> -Förhindra åtkomst till verktyg för redigering av registret. <br> – Förtroende logik för bifogade filer. <br> – Aktivera skript körning. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Ingen befintlig standard webbplats <br> – Ingen befintlig webbplats/program lyssnar på port 443 <br>-Aktivera [Anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -inställning
**Nätverks inställningar** |
IP-adresstyp | Statisk
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)
Typ av nätverkskort | VMXNET3

### <a name="replication-appliance-url-access"></a>URL-åtkomst till replikeringsfil

Replication-enheten behöver åtkomst till dessa URL: er.

**URL** | **Detaljer**
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

**Installera** | **Detaljer**
--- | ---
Ladda ned och installera manuellt | Hämta MySQL-programmet & Placera det i mappen C:\Temp\ASRSetup och installera manuellt.<br/> När du konfigurerar installationen av MySQL visas som redan installerad.
Hämta inte online | Placera programmet MySQL installations program i mappen C:\Temp\ASRSetup. När du installerar installationen och klickar för att ladda ned och installera MySQL använder installations programmet det installations program som du har lagt till.
Ladda ned från Azure Migrate | När du installerar installationen och tillfrågas om MySQL väljer du **Ladda ned och installera**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Agent-baserad migrering – krav för VMware VM

**Support** | **Detaljer**
--- | ---
**Dator arbets belastning** | Azure Migrate stöder migrering av arbets belastningar (t. ex. Active Directory, SQL Server osv.) som körs på en dator som stöds.
**Operativ system** | Du hittar den senaste informationen i [operativ systemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) för Site Recovery. Azure Migrate tillhandahåller identiskt stöd för virtuella dator operativ system.
**Linux-filsystem/gäst lagring** | Du hittar den senaste informationen i Linux-filsystemets [stöd](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) för Site Recovery. Azure Migrate har samma stöd för Linux-filsystem.
**Nätverk/lagring** | För den senaste informationen granskar du kraven för [nätverk](../site-recovery/vmware-physical-azure-support-matrix.md#network) och [lagring](../site-recovery/vmware-physical-azure-support-matrix.md#storage) för Site Recovery. Azure Migrate tillhandahåller identiska nätverks-/lagrings krav.
**Krav för Azure** | Du hittar den senaste informationen i [Azure-nätverket](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [lagrings](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)-och [beräknings](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) kraven för Site Recovery. Azure Migrate har identiska krav för VMware-migrering.
**Mobilitetstjänsten** | Mobilitets tjänst agenten måste vara installerad på varje virtuell dator som du vill migrera.
**Mål disk** | Virtuella datorer kan bara migreras till Managed disks (standard HHD, Premium SSD) i Azure.



## <a name="agent-based-migration-url-access-requirements"></a>Agent-baserad migrering-URL åtkomst krav

Mobilitets tjänsten som körs på virtuella VMware-datorer kräver Internet anslutning till Internet.

- När du distribuerar mobilitets tjänsten kontrollerar den anslutningen till URL: erna som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad brand vägg. proxy kan du ge åtkomst till dessa URL: er och se till att proxyservern matchar eventuella CNAME-poster som tas emot när URL: erna genomsöks.

**URL** | **Detaljer**  
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

**Anordningar** | **anslutning**
--- | ---
Virtuella datorer | Mobilitets tjänsten som körs på virtuella datorer kommunicerar med den lokala konfigurations servern på port HTTPS 443 inkommande, för hantering av replikering.<br/><br/> Virtuella datorer skickar replikeringsdata till processervern (körs på konfigurations serverdatorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
Replikeringsfil | Replikeringstjänsten dirigerar replikering med Azure över Port HTTPS 443 utgående.
Processerver | Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.<br/> Som standard körs processervern på replikerings enheten.

## <a name="azure-vm-requirements"></a>Krav för virtuell Azure-dator

Alla lokala virtuella datorer som replikeras till Azure måste uppfylla de krav för virtuella Azure-datorer som sammanfattas i den här tabellen. När Site Recovery kör en krav kontroll för replikering kommer kontrollen att Miss klar om några av kraven inte uppfylls.

**Komponent** | **Signaturkrav** | **Detaljer**
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
Anslut efter migreringen – Windows | Ansluta till virtuella Azure-datorer som kör Windows efter migrering:<br/> – Innan migrering aktiverar du RDP på den lokala virtuella datorn. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.<br/> För plats-till-plats-VPN-åtkomst aktiverar du RDP och tillåter RDP i **Windows-brandväggen** -> **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk. Dessutom kontrollerar du att operativ systemets SAN-princip är inställd på **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135). |
Anslut efter migreringen – Linux | Ansluta till virtuella Azure-datorer efter migrering med SSH:<br/> Innan migreringen går du till den lokala datorn, kontrollerar att tjänsten Secure Shell är inställt på Start och att brand Väggs reglerna tillåter en SSH-anslutning.<br/> Efter redundansväxlingen på den virtuella Azure-datorn tillåter du inkommande anslutningar till SSH-porten för reglerna för nätverks säkerhets grupper på den misslyckade virtuella datorn och för det Azure-undernät som den är ansluten till. Lägg också till en offentlig IP-adress för den virtuella datorn. |  


## <a name="next-steps"></a>Nästa steg

[Förbered för VMware](tutorial-prepare-vmware.md) -utvärdering och migrering.
