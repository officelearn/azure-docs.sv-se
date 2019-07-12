---
title: Azure Migrate-supportmatris för VMware-bedömning och migrering
description: Sammanfattar support inställningar och begränsningar för bedömning och migrering av virtuella VMware-datorer till Azure med tjänsten Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: raynew
ms.openlocfilehash: 567a6582e193208a7ff4aa37bafefe1dec4f4e8f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811588"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Supportmatris för VMware-bedömning och migrering

Du kan använda den [Azure Migrate-tjänsten](migrate-overview.md) att utvärdera och migrera datorer till Microsoft Azure-molnet. Den här artikeln sammanfattas support inställningar och begränsningar för att utvärdera och migrera lokala virtuella VMware-datorer.


## <a name="vmware-scenarios"></a>VMware-scenarier

Tabellen beskriver scenarier som stöds för virtuella VMware-datorer.

**Distribution** | **Detaljer** 
--- | --- 
**Utvärdera lokala virtuella VMware-datorer** | [Konfigurera](tutorial-prepare-vmware.md) din första utvärdering.<br/><br/> [Kör](scale-vmware-assessment.md) en storskalig utvärdering.
**Migrera virtuella VMware-datorer** | Du kan migrera med hjälp av migrering utan agenter, med vissa begränsningar eller använda en agentbaserad-migrering. [Läs mer](server-migrate-overview.md)


    


## <a name="azure-migrate-projects"></a>Azure Migrate-projekt

**Support** | **Detaljer**
--- | ---
Azure-behörigheter | Du behöver deltagare eller ägare behörigheter i prenumerationen för att skapa ett Azure Migrate-projekt.
VMware-begränsningar  | Utvärdera upp till 35 000 virtuella VMware-datorer i ett enda projekt.

Ett projekt kan innehålla både virtuella VMware-datorer och Hyper-V-datorer till en gräns för utvärdering.

## <a name="assessment-vmware-server-requirements"></a>Utvärdering av VMware-serverkraven

Den här tabellen sammanfattar stöd för utvärdering och begränsningar för VMware virtualiseringsservrar.

**Support** | **Detaljer**
--- | ---
**vCenter-server** | Virtuella VMware-datorer du vill utvärdera måste hanteras av en eller flera vCenter-servrar som kör 5.5, 6.0, 6.5 eller 6.7.

## <a name="assessment-vcenter-server-permissions"></a>Utvärdering av vCenter Server-behörigheter

För utvärdering behöver du ett skrivskyddat konto för vCenter-servern.

## <a name="assessment-appliance-requirements"></a>Krav för utvärdering av enhet

**Support** | **Detaljer**
--- | ---
**ESXi** | Den VM-installationen måste distribueras på en ESXi-värd som kör version 5.5 eller senare.
**Azure Migrate-projekt** | En apparat kan associeras med ett enda projekt.
**vCenter Server** | En apparat kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter-Server.<br/> En apparat kan ansluta till en vCenter-servern.


## <a name="assessment-url-access-requirements"></a>Webbadress till begränsad provmiljö åtkomstkrav

Azure Migrate-installationen måste ha en Internetanslutning till internet.

- När du distribuerar installationen gör Azure Migrate en anslutningskontroll URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad firewall.proxy Tillåt åtkomst till dessa URL: er, se till att proxyn löser eventuella CNAME-poster har tagits emot vid Leta upp de URL: er.

**URL** | **Detaljer**  
--- | --- |
*.portal.azure.com  | Gå till Azure Migrate i Azure-portalen.
*.windows.net | Logga in till din Azure-prenumeration.
*.microsoftonline.com | Skapa Active Directory-appar att kommunicera med tjänsten Azure Migrate.
management.azure.com | Skapa Active Directory-appar att kommunicera med tjänsten Azure Migrate.
dc.services.visualstudio.com | Ladda upp appen loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.
*.servicebus.windows.net | Kommunikation mellan installationen och tjänsten Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate-tjänstens URL.
*.blob.core.windows.net | Ladda upp data till storage-konton.


## <a name="assessment-port-requirements"></a>Utvärdering av portkraven

**enheten** | **anslutning**
--- | --- 
Installation | Inkommande anslutningar på TCP-port 3389 för att tillåta anslutningar till fjärrskrivbord till installationen.<br/> Inkommande anslutningar på port 44368 fjärråtkomst till installation av appen med URL: en: https://<appliance-ip-or-name>:44368 <br/>Utgående anslutningar på port 443 för att skicka metadata för identifiering och prestanda till Azure Migrate.
vCenter-server | Inkommande anslutningar på TCP-port 443 så att installationen att samla in och metadata för utvärderingar. <br/> Enheten som ansluter till vCenter på port 443 som standard. Om vCenter-servern lyssnar på en annan port ska ändra du porten när du konfigurerar identifiering.


## <a name="agentless-migration-vmware-server-requirements"></a>Agentlös migrering VMware-serverkraven

Den här tabellen sammanfattar stöd för utvärdering och begränsningar för VMware virtualiseringsservrar.

**Support** | **Detaljer**
--- | ---
**vCenter-server** | Virtuella VMware-datorer du migrerar med hjälp av en migrering utan agenter måste hanteras av en eller flera vCenter-servrar som kör 5.5, 6.0, 6.5 eller 6.7.

## <a name="agentless-migration-vcenter-server-permissions"></a>Agentlös migrering-vCenter Server-behörigheter

**Behörigheter** | **Detaljer**
--- | --- 
Datastore.Browse | Tillåt bläddring i VM-loggfiler för att felsöka ögonblicksbilder skapas eller tas bort.
Datastore.LowLevelFileOperations | Tillåt Läs/Skriv/delete/namnbytesåtgärderna i datalagret webbläsare, felsökning av ögonblicksbild skapas eller tas bort.
VirtualMachine.Configuration.DiskChangeTracking | Tillåt att aktivera eller inaktivera ändringsspårning för Virtuella diskar för att hämta ändrade block av data mellan ögonblicksbilder
VirtualMachine.Configuration.DiskLease | Tillåt diskåtgärder lån för en virtuell dator att läsa disken med VMware vSphere-virtuell Disk Development Kit (VDDK).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Tillåt att öppna en disk på en virtuell dator att läsa disken med hjälp av VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Tillåter Läs åtgärder på filer som hör till en virtuell dator kan du hämta loggarna och felsöker om fel uppstår. 
VirtualMachine.SnapshotManagement.* | Tillåt skapande och hantering av ögonblicksbilder av Virtuella för replikering. 
Virtuella Machine.Interaction.Power av | Tillåt att den virtuella datorn stängs av under migreringen till Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Agentlös migrering – VMware-kraven för virtuella datorer

**Support** | **Detaljer**
--- | ---
**Operativsystem som stöds** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operativsystem som stöds av Azure kan migreras med hjälp av migrering utan agenter.
**Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för följande operativsystem:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> För andra operativsystem som du behöver justera manuellt före migreringen. Artiklarna innehåller instruktioner om hur du gör detta.
**Linux-Start** | Om/Boot finns på en särskild partition, bör finnas på OS-disken och sprids inte över flera diskar.<br/> Om/Boot är en del av rotpartitionen (/), ska sedan '/' partitionen vara på OS-disken och inte omfatta andra diskar.
**UEFI-Startmetod** | Virtuella datorer med UEFI boot stöds inte för migrering.
**Krypterade diskar/volymer** | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
**RDM/genomströmningsdiskar** | Om virtuella datorer har RDM eller genomströmning diskar, replikeras inte dessa diskar till Azure.
**NFS** | NFS-volymer monterade volymer på de virtuella datorerna replikeras inte.
**Måldisken** | Virtuella datorer kan bara migreras till managed disks (standard HHD, premium SSD) i Azure.


## <a name="agentless-migration-appliance-requirements"></a>Krav för utan Agent migrering-installation


**Support** | **Detaljer**
--- | ---
**ESXi** | Den VM-installationen måste distribueras på en ESXi-värd som kör version 5.5 eller senare.
**Azure Migrate-projekt** | En apparat kan associeras med ett enda projekt.
**vCenter Server** | En apparat kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter-Server.<br/> En apparat kan ansluta till en vCenter-servern.
**VDDK** | Om du kör en migrering utan agenter med Azure Migrate servermigrering, måste VMware vSphere virtuell Disk Development Kit (VDDK) installeras på VM-installation.

## <a name="agentless-migration-url-access-requirements"></a>Krav för åtkomst av AEM-Webbadressen för migrering

Azure Migrate-installationen måste ha en Internetanslutning till internet.

- När du distribuerar installationen gör Azure Migrate en anslutningskontroll URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad firewall.proxy Tillåt åtkomst till dessa URL: er, se till att proxyn löser eventuella CNAME-poster har tagits emot vid Leta upp de URL: er.

**URL** | **Detaljer**  
--- | --- 
*.portal.azure.com | Gå till Azure Migrate i Azure-portalen.
*.windows.net | Logga in till din Azure-prenumeration.
*.microsoftonline.com | Skapa Active Directory-appar att kommunicera med tjänsten Azure Migrate.
management.azure.com | Skapa Active Directory-appar att kommunicera med tjänsten Azure Migrate.
dc.services.visualstudio.com | Ladda upp appen loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.
*.servicebus.windows.net | Kommunikation mellan installationen och tjänsten Azure Migrate.
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate-tjänstens URL.
*.blob.core.windows.net | Ladda upp data till storage-konton.


## <a name="agentless-migration-port-requirements"></a>Migrering – portkraven utan Agent

**enheten** | **anslutning**
--- | --- 
Installation | Utgående TCP-port 3389 att ladda upp replikerade data till Azure och för att kommunicera med Azure Migrate för replikering och migrering.
vCenter-server | Inkommande anslutningar på TCP-port 443 för att tillåta installation för att dirigera replikering – skapa ögonblicksbilder, kopieringsdata, släpp ögonblicksbilder
vSphere/EXSI-värden | Inkommande på TCP-port 902 att replikera data från ögonblicksbilder. 


## <a name="agent-based-migration-vmware-server-requirements"></a>Agentbaserad migrering – VMware-serverkraven

Den här tabellen sammanfattar stöd för utvärdering och begränsningar för VMware virtualiseringsservrar.

**Support** | **Detaljer**
--- | ---
**vCenter-servern/ESXI** | Virtuella VMware-datorer du migrera måste hanteras av en eller flera vCenter-servrar som kör 5.5, 6.0, 6.5 eller 6.7 eller körs på en ESXI-värd med vSphere version 5.5, 6.0, 6.5 eller 6.7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Agentbaserad migrering-vCenter Server-behörigheter

**Behörigheter** | **Detaljer**
--- | --- 
Datastore.AllocateSpace | Tillåt tilldelning av diskutrymme på ett datalager för en virtuell dator, ögonblicksbilder, klona eller virtuell disk. 
Datastore.Browse | Tillåt bläddring i VM-loggfiler för att felsöka ögonblicksbilder skapas eller tas bort.
Datastore.LowLevelFileOperations | Tillåt läsa, skriva, ta bort och byta namn på åtgärder i datastore-webbläsaren om du vill felsöka ögonblicksbilder skapas eller tas bort.
Datastore.UpdateVirtualMachineFiles | Tillåt uppdatering sökvägar till VM-filer på ett datalager när databasen har resignatured.
Network.AssignNetwork | Tillåt att tilldela ett nätverk till en VM-resurs.
AssignVirtualMachineToResourcePool | Tillåt tilldelningen av en virtuell dator till en resurspool.
Resource.MigratePoweredOffVirtualMachine | Gör att migrera en avstängd VM till en annan resurspool eller en värddator.
Resource.MigratePoweredOnVirtualMachine | Tillåt migrering med vMotion för en storleken på virtuell dator till en annan resurspool eller en värddator.
Tasks.CreateTask | Tillåt ett tillägg att skapa en användardefinierad uppgift.
Tasks.UpdateTask | Tillåt ett tillägg att uppdatera en användardefinierad uppgift.
VirtualMachine.Configuration. | Tillåt att konfigurera alternativ för virtuella datorer och enheter.
Virtuella Machine.Interaction.AnswerQuestion | Tillåt att lösa problem med VM-tillståndsövergångar eller körningsfel.
Virtual Machine.Interaction.DeviceConnection | Tillåtet att ändra anslutningsläget för en virtuell dators matarledningar virtuella enheter.
Virtual Machine.Interaction.ConfigureCDMedia | Att konfigurera en virtuell DVD- eller CD-ROM-enhet.
Virtual Machine.Interaction.ConfigureFloppyMedia | Att konfigurera en virtuell diskett enhet.
Virtual Machine.Interaction.PowerOff | Kan den virtuella datorn stängs av under migreringen till Azure.
Virtual Machine.Interaction.PowerOn | Tillåt aktiverar en drivs av virtuell dator och återupptar en pausad virtuell dator.
Virtual Machine.Interaction.VMwareToolsInstall | Tillåt montera och demontera VMware-verktyg CD-installationsprogrammet som en CD-skivan för gästoperativsystemet.
VirtualMachine.Inventory.CreateNew | Tillåt skapandet av en virtuell dator och allokering för resurser som krävs.
VirtualMachine.Inventory.Register | Tillåt att lägga till en befintlig virtuell dator till en vCenter-Server eller värden inventering.
VirtualMachine.Inventory.Unregister | Tillåt att avregistrera en VMe från en vCenter-Server eller värden inventering.
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | Tillåt skrivåtgärder på filer som hör till en virtuell dator, inklusive vmx, diskar, loggar och nvram.
VirtualMachine.Provisioning.AllowVirtualMachineDownload | Tillåt läsåtgärder på filer som hör till en virtuell dator att ladda ner loggar för felsökning.
VirtualMachine.SnapshotManagement.RemoveSnapshot | Tillåt borttagning av en ögonblicksbild från historiken över ögonblicksbild.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Krav för installation agentbaserad migrering-replikering

Kraven för den [replikering installation](migrate-replication-appliance.md) används för agentbaserad migreringen av virtuella VMware-datorer och fysiska servrar med Azure Migrate servermigrering sammanfattas i tabellen.

> [!NOTE]
> När du konfigurerar replikering installationen med hjälp av OVA-mallen i Azure Migrate-hub enheten som kör Windows Server 2016 och uppfyller kraven för stöd. Om du ställer in replikering installationen manuellt på en fysisk server, se till att den överensstämmer med kraven.



**Komponent** | **Krav** 
--- | ---
 | **Inställningar för VMware** (VMware VM-enhet)
**PowerCLI** | [PowerCLI version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) ska installeras om replikering installationen körs på en VMware-VM.
**Typ av nätverkskort** | VMXNET3 (om installationen är en VMware-VM)
 | **Maskinvaruinställningar** 
Processorkärnor | 8 
RAM | 16 GB
Antal diskar | Tre: Den OS-disk, processerverns cachedisk och kvarhållningsenhet.
Ledigt diskutrymme (cache) | 600 GB
Ledigt diskutrymme (kvarhållningsdisken) | 600 GB
**Programinställningar** | 
Operativsystem | Windows Server 2016 eller Windows Server 2012 R2
Nationella inställningar för operativsystem | Engelska (en-us)
TLS | TLS 1.2 ska aktiveras.
.NET Framework | .NET framework 4.6 eller senare bör installeras på datorn (med stark kryptografi aktiverat.
MySQL | MySQL bör installeras på installationen.<br/> MySQL ska installeras. Du kan installera manuellt eller Site Recovery kan du installera det under distributionen av installationen. 
Andra appar | Du bör inte köra andra appar på replikering-installationen.
Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Grupprinciper | Aktivera inte dessa grupp-principer: <br> -Förhindra åtkomst till Kommandotolken. <br> -Förhindra åtkomst till registerredigeringsverktygen. <br> -Förtroende för bifogade filer. <br> -Aktivera körning av skript. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Ingen befintlig standardwebbplatsen <br> – Ingen befintlig webbplats/program lyssnar på port 443 <br>-Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställningen 
**Nätverksinställningar** | 
IP-adresstyp | Statisk 
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring) 
Typ av nätverkskort | VMXNET3 

### <a name="replication-appliance-url-access"></a>Replikering installation URL-åtkomst

Replikering installationen behöver åtkomst till dessa URL: er.

**URL** | **Detaljer**
--- | ---
\*.backup.windowsazure.com | Används för överföring av replikerade data och samordning
\*.store.core.windows.net | Används för överföring av replikerade data och samordning
\*.blob.core.windows.net | Används för åtkomst till lagringskontot som lagrar replikerade data
\*.hypervrecoverymanager.windowsazure.com | Används för replikeringshantering och koordination
https:\//management.azure.com | Används för replikeringshantering och koordination 
*.services.visualstudio.com | Används för telemetri (det är valfritt)
time.nist.gov | Används för att kontrollera tidssynkronisering mellan system och global tid.
time.windows.com | Används för att kontrollera tidssynkronisering mellan system och global tid.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVF installationsprogrammet behöver åtkomst till dessa URL: er. De används för access control och Identitetshantering av Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Slutför nedladdningen av MySQL


#### <a name="mysql-installation-options"></a>MySQL-installationsalternativ

MySQL kan installeras på den installationen för replikering med någon av dessa metoder.

**Installera** | **Detaljer**
--- | ---
Hämta och installera manuellt | Ladda ned MySQL-program och placera den i mappen C:\Temp\ASRSetup och installera manuellt.<br/> När du ställer in den installationen MySQL visas som redan installerad. 
Hämta inte online | Placera MySQL installer-program i mappen C:\Temp\ASRSetup. När du installerar installationen och klicka för att ladda ned och installera MySQL, används det installationsprogram som du har lagt till. 
Ladda ned från Azure Migrate | När du installerar installationen och uppmanas att MySQL, Välj **ladda ned och installera**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Agentbaserad migrering – VMware-kraven för virtuella datorer

**Support** | **Detaljer**
--- | ---
**Datorns arbetsbelastning** | Azure Migrate stöder migrering av alla arbetsbelastningar (exempelvis Active Directory, SQLServer, osv) som körs på en dator som stöds.
**Operativsystem** | Den senaste informationen, granska de [stöd för värdoperativsystem](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) för Site Recovery. Azure Migrate har identiska stöd för VM-operativsystem.
**Linux-system/gäst fillagring** | Den senaste informationen, granska de [stöd för Linux filer system](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) för Site Recovery. Azure Migrate har identiska systemstöd för Linux-filer.
**Nätverkslagring /** | Den senaste informationen, granska de [nätverk](../site-recovery/vmware-physical-azure-support-matrix.md#network) och [storage](../site-recovery/vmware-physical-azure-support-matrix.md#storage) krav för Site Recovery. Azure Migrate innehåller identiska nätverkslagring krav.
**Krav för Azure** | Den senaste informationen, granska de [Azure-nätverk](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [storage](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage), och [compute](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) kraven för Site Recovery. Azure Migrate har identiska krav för VMware-migrering.
**Mobilitetstjänsten** | Mobility service-agenten måste installeras på varje virtuell dator som du vill migrera.
**Måldisken** | Virtuella datorer kan bara migreras till managed disks (standard HHD, premium SSD) i Azure.

   

## <a name="agent-based-migration-url-access-requirements"></a>Krav för agentbaserad-Webbadressen för migrering

Den mobilitetstjänstversion som körs på virtuella VMware-datorer måste ha en Internetanslutning till internet.

- När du distribuerar mobilitetstjänsten, sker en anslutningskontroll URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad firewall.proxy Tillåt åtkomst till dessa URL: er, se till att proxyn löser eventuella CNAME-poster har tagits emot vid Leta upp de URL: er.

**URL** | **Detaljer**  
--- | --- 
*.portal.azure.com | Gå till Azure Migrate i Azure-portalen.
*.windows.net | Logga in till din Azure-prenumeration.
*.microsoftonline.com | Skapa Active Directory-appar att kommunicera med tjänsten Azure Migrate. 
management.azure.com | Skapa Active Directory-appar att kommunicera med tjänsten Azure Migrate.
dc.services.visualstudio.com | Ladda upp appen loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.
*.servicebus.windows.net | Kommunikation mellan installationen och tjänsten Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate-tjänstens URL.
*.blob.core.windows.net | Ladda upp data till storage-konton.

## <a name="agent-based-migration-port-requirements"></a>Agentbaserad krav för migrering-port

**enheten** | **anslutning**
--- | --- 
Virtuella datorer | Mobilitetstjänsten på virtuella datorer kommunicerar med den lokala konfigurationsservern på port HTTPS 443 inkommande, för replikeringshantering.<br/><br/> Virtuella datorer skickar replikeringsdata till processervern (som körs på configuration server-datorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
Installation av replikering | Replikering installationen arrangerar replikeringen med Azure via port HTTPS 443 utgående.
Processervern | Processervern tar emot replikeringsdata, optimerar och krypterar dem och skickar dem till Azure storage över port 443 utgående.<br/> Som standard körs processervern på replikering-installationen.

## <a name="azure-vm-requirements"></a>Krav för Azure VM

Alla lokala virtuella datorer som replikeras till Azure måste uppfylla kraven för virtuella Azure-datorer som sammanfattas i den här tabellen. När Site Recovery kör en kravkontroll för replikering, misslyckas kontrollen om vissa av kraven inte uppfylls.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Gästoperativsystem | Kontrollera operativsystem som stöds för [virtuella VMware-datorer med hjälp av utan Agent replikering](#agentless-migration-vmware-vm-requirements), och för [virtuella VMware-datorer med hjälp av agentbaserad replikering](#agent-based-migration-vmware-vm-requirements).<br/> Du kan migrera alla arbetsbelastningar som körs på ett operativsystem som stöds. | Det går inte att kontrollera om stöds inte.
Gästen operativsystemets arkitektur | 64-bitars. | Det går inte att kontrollera om stöds inte.
Storleken på operativsystemdisken | Upp till 2 048 GB. | Det går inte att kontrollera om stöds inte.
Antal operativsystemdiskar | 1 | Det går inte att kontrollera om stöds inte.
Datadiskar | 64 eller mindre. | Det går inte att kontrollera om stöds inte.
Datadiskstorleken | Upp till 4095 GB | Det går inte att kontrollera om stöds inte.
Nätverkskort | Flera nätverkskort stöds. | 
Delad VHD | Stöds ej. | Det går inte att kontrollera om stöds inte.
FC-disk | Stöds ej. | Det går inte att kontrollera om stöds inte.
BitLocker | Stöds ej. | BitLocker måste inaktiveras innan du aktiverar replikering för en dator. 
VM-namn | Mellan 1 och 63 tecken.<br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Namnet på datorn måste börja och sluta med en bokstav eller siffra. |  Uppdatera värdet i egenskaperna för datorn i Site Recovery.
Ansluta till efter migrering – Windows | Ansluta till virtuella Azure-datorer som kör Windows efter migreringen:<br/> -Innan migreringen aktiverar du RDP på den lokala virtuella datorn. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.<br/> För plats-till-plats VPN-åtkomst, aktiverar du RDP och Tillåt RDP i **Windows-brandväggen** -> **tillåtna appar och funktioner** för **domän och privat** nätverk. Du kan också kontrollera att operativsystemets SAN-princip har angetts till **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135). | 
Ansluta till efter migrering – Linux | Ansluta till virtuella Azure-datorer efter migrering med hjälp av SSH:<br/> Kontrollera att Secure Shell-tjänsten är inställd på Start och att brandväggsreglerna tillåter en SSH-anslutning innan migreringen på den lokala datorn.<br/> Tillåta inkommande anslutningar till SSH-porten för reglerna för nätverkssäkerhetsgrupper på den redundansväxlade virtuella datorn och Azure-undernätet som den är ansluten på den virtuella Azure-datorn efter redundansväxlingen. Dessutom lägga till en offentlig IP-adress för den virtuella datorn. |  


## <a name="next-steps"></a>Nästa steg

[Förbereda för VMware](tutorial-prepare-vmware.md) bedömning och migrering.








