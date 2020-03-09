---
title: Stöd för VMware-migrering i Azure Migrate
description: Läs mer om stöd för migrering av VMware VM i Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 9d8dc4dadc975a0fb69ea207f6062b72231460ef
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388968"
---
# <a name="support-matrix-for-vmware-migration"></a>Support mat ris för VMware-migrering

Den här artikeln sammanfattar support inställningar och begränsningar för migrering av virtuella VMware-datorer med [Azure Migrate: Server-migrering](migrate-services-overview.md#azure-migrate-server-migration-tool) . Om du vill ha information om hur du bedömer virtuella VMware-datorer för migrering till Azure läser du [matrisen för utvärderings support](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Migrations alternativ

Du kan migrera virtuella VMware-datorer på ett par olika sätt:

- Med agent lös migrering: migrera virtuella datorer utan att behöva installera något på dem. Du distribuerar [Azure Migrate](migrate-appliance.md) -installationen för migrering utan agent.
- Med agent-baserad migrering: installera en agent på den virtuella datorn för replikering. För en agent-baserad migrering måste du distribuera en [replikeringsprincip](migrate-replication-appliance.md).

Läs [den här artikeln](server-migrate-overview.md) för att ta reda på vilken metod du vill använda.

## <a name="migration-limitations"></a>Begränsningar för migrering

- Du kan välja upp till 10 virtuella datorer på en gång för replikering. Om du vill migrera fler datorer kan du replikera i grupper om 10.
- För VMware-agent lös migrering kan du köra upp till 100 replikeringar samtidigt.

## <a name="agentless-vmware-servers"></a>Agentbaserade – VMware-servrar

**VMware** | **Information**
--- | ---
**VMware vCenter Server** | Version 5,5, 6,0, 6,5 eller 6,7.
**VMware vSphere ESXI-värd** | Version 5,5, 6,0, 6,5 eller 6,7.
**vCenter Server behörigheter** | [Vid utan agent används migreringen](migrate-appliance.md). Installationen behöver följande behörigheter:<br/><br/> - **data lager. browse**: Tillåt bläddring av VM-loggfiler för att felsöka skapande och borttagning av ögonblicks bilder.<br/><br/> - **data lager. LowLevelFileOperations**: Tillåt åtgärder för att läsa/skriva/ta bort/byta/byta namn i listan över data lager för att felsöka skapande och borttagning av ögonblicks bilder<br/><br/> - **VirtualMachine. Configuration. DiskChangeTracking**: Tillåt aktivering eller inaktive ring av ändrings spårning av virtuella dator diskar för att hämta ändrade block med data mellan ögonblicks bilder.<br/><br/> - **VirtualMachine. Configuration. DiskLease**: Tillåt disk låne åtgärder för en virtuell dator för att läsa disken med hjälp av VMware vSphere Virtual Disk Development Kit (vddk).<br/><br/> - **VirtualMachine. Provisioning. AllowDiskAccess**: (specifikt för vSphere 6,0 och senare) Tillåt att en disk på en virtuell dator öppnas för slumpmässig Läs åtkomst på disken med hjälp av vddk.<br/><br/> - **VirtualMachine. Provisioning. AllowReadOnlyDiskAccess**: Tillåt att en disk öppnas på en virtuell dator för att läsa disken med hjälp av vddk.<br/><br/> - **VirtualMachine. Provisioning. AllowDiskRandomAccess**: Tillåt att en disk öppnas på en virtuell dator för att läsa disken med hjälp av vddk.<br/><br/> - **VirtualMachine. Provisioning. AllowVirtualMachineDownload**: tillåter Läs åtgärder på filer som är associerade med en virtuell dator, för att ladda ned loggarna och felsöka om det uppstår fel.<br/><br/> -* * VirtualMachine. SnapshotManagement. * * *: tillåter skapande och hantering av ögonblicks bilder av virtuella datorer för replikering.<br/><br/> - **virtuell dator. interaktion.** avstängning: Tillåt att den virtuella datorn stängs av under migreringen till Azure.



## <a name="agentless-vmware-vms"></a>Virtuella datorer utan agent

**Hjälp** | **Information**
--- | ---
**Operativ system som stöds** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem som stöds av Azure kan migreras med hjälp av en migrering utan agent.
**Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för följande operativ system:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> För andra operativ system måste du göra justeringar manuellt innan migreringen. Relevanta artiklar innehåller instruktioner om hur du gör detta.
**Linux-start** | Om/boot finns på en dedikerad partition bör den finnas på OS-disken och inte spridas över flera diskar.<br/> Om/Boot är en del av rot-partitionen (/) bör partitionen/-partitionen finnas på OS-disken och inte omfatta andra diskar.
**UEFI-start** | Virtuella datorer med UEFI-start stöds inte för migrering.
**Disk storlek** | 2 TB OS-disk; 4 TB för data diskar.
**Disk gränser** |  Upp till 60 diskar per virtuell dator.
**Krypterade diskar/volymer** | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
**Delat disk kluster** | Stöds inte.
**Oberoende diskar** | Stöds inte.
**RDM/passthrough-diskar** | Om de virtuella datorerna har RDM-eller passthrough-diskar replikeras inte dessa diskar till Azure.
**MSNFS** | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte.
**iSCSI-mål** | Virtuella datorer med iSCSI-mål stöds inte för migrering utan agent.
**Multipath i/o** | Stöds inte.
**Lagrings vMotion** | Stöds inte. Det går inte att använda replikering om en virtuell dator använder Storage vMotion.
**Grupperade nätverkskort** | Stöds inte.
**IPv6** | Stöds inte.
**Mål disk** | Virtuella datorer kan bara migreras till Managed disks (standard HDD, Premium SSD) i Azure.
**Samtidig replikering** | 100 virtuella datorer per vCenter Server. Om du har fler kan du migrera dem i batchar på 100.


## <a name="agentless-azure-migrate-appliance"></a>Agent utan agent Azure Migrate 
Vid migrering utan agent används Azure Migrate-installationen som distribueras på en virtuell VMware-dator.

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---vmware) för VMware.
- Lär dig mer om [webb adresser](migrate-appliance.md#url-access) som behövs för att få åtkomst till enheten.

## <a name="agentless-ports"></a>Agenter-portar

**Anordningar** | **Anslutningen**
--- | ---
Enhet | Utgående anslutningar på port 443 för att överföra replikerade data till Azure och för att kommunicera med Azure Migrate tjänster som dirigerar replikering och migrering.
vCenter-Server | Inkommande anslutningar på port 443 för att tillåta att enheten dirigerar replikering – skapa ögonblicks bilder, kopiera data, versions ögonblicks bilder
vSphere/EXSI-värd | Inkommande på TCP-port 902 för att enheten ska replikera data från ögonblicks bilder.


## <a name="agent-based-vmware-servers"></a>Agentbaserade VMware-servrar
I den här tabellen sammanfattas utvärderings support och begränsningar för VMware virtualization-servrar.

**Krav för VMware** | **Information**
--- | ---
**VMware vCenter Server** | Version 5,5, 6,0, 6,5 eller 6,7.
**VMware vSphere ESXI-värd** | Version 5,5, 6,0, 6,5 eller 6,7.
**vCenter Server behörigheter** | Ett skrivskyddat konto för vCenter Server.

## <a name="agent-based-vmware-vms"></a>Virtuella datorer med agent-baserade VMware

Tabellen sammanfattar VMware VM-stöd för virtuella VMware-datorer som du vill migrera med hjälp av en agent-baserad migrering.

**Hjälp** | **Information**
--- | ---
**Dator arbets belastning** | Azure Migrate stöder migrering av arbets belastningar (t. ex. Active Directory, SQL Server osv.) som körs på en dator som stöds.
**Operativ system** | Du hittar den senaste informationen i [operativ systemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) för Site Recovery. Azure Migrate tillhandahåller identiskt stöd för virtuella dator operativ system.
**Linux-filsystem/gäst lagring** | Du hittar den senaste informationen i [Linux-filsystemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) för Site Recovery. Azure Migrate har samma stöd för Linux-filsystem.
**Nätverk/lagring** | För den senaste informationen granskar du kraven för [nätverk](../site-recovery/vmware-physical-azure-support-matrix.md#network) och [lagring](../site-recovery/vmware-physical-azure-support-matrix.md#storage) för Site Recovery. Azure Migrate tillhandahåller identiska nätverks-/lagrings krav.
**Krav för Azure** | Du hittar den senaste informationen i [Azure-nätverket](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [lagrings](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)-och [beräknings](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) kraven för Site Recovery. Azure Migrate har identiska krav för VMware-migrering.
**Mobilitets tjänst** | Mobilitets tjänst agenten måste vara installerad på varje virtuell dator som du vill migrera.
**UEFI-start** | Den migrerade virtuella datorn i Azure kommer automatiskt att konverteras till en virtuell dator med BIOS-start.<br/><br/> OS-disken bör ha upp till fyra partitioner och volymer ska formateras med NTFS.
**Mål disk** | Virtuella datorer kan bara migreras till Managed disks (standard HDD, Premium SSD) i Azure.
**Disk storlek** | 2 TB OS-disk; 8 TB för data diskar.
**Disk gränser** |  Upp till 63 diskar per virtuell dator.
**Krypterade diskar/volymer** | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
**Delat disk kluster** | Stöds inte.
**Oberoende diskar** | Tillåtna.
**Genom strömnings diskar** | Tillåtna.
**MSNFS** | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte.
**iSCSI-mål** | Virtuella datorer med iSCSI-mål stöds inte för migrering utan agent.
**Multipath i/o** | Stöds inte.
**Lagrings vMotion** | Tillåtna
**Grupperade nätverkskort** | Stöds inte.
**IPv6** | Stöds inte.




## <a name="agent-based-replication-appliance"></a>Installation av agent-baserad replikering 

När du ställer in replikeringen med hjälp av den ägg-mall som finns i Azure Migrate Hub, kör enheten Windows Server 2016 och uppfyller support kraven. Om du konfigurerar replikeringen manuellt på en fysisk server kontrollerar du att den uppfyller kraven.

- Lär dig mer om [krav för replikering](migrate-replication-appliance.md#appliance-requirements) av VMware.
- MySQL måste vara installerat på enheten. Lär dig mer om [installations alternativ](migrate-replication-appliance.md#mysql-installation).
- Lär dig mer om [webb adresser](migrate-replication-appliance.md#url-access) och [portar](migrate-replication-appliance.md#port-access) som krävs för att få åtkomst till replikerings enheten.

## <a name="agent-based-ports"></a>Agenter-baserade portar

**Anordningar** | **Anslutningen**
--- | ---
VMs | Mobilitets tjänsten som körs på virtuella datorer kommunicerar med den lokala replikerings enheten (konfigurations servern) på port HTTPS 443 inkommande, för hantering av replikering.<br/><br/> Virtuella datorer skickar replikeringsdata till processervern (körs på konfigurations serverdatorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
Replikeringsfil | Replikeringstjänsten dirigerar replikering med Azure över Port HTTPS 443 utgående.
Processerver | Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.<br/> Som standard körs processervern på replikerings enheten.

## <a name="azure-vm-requirements"></a>Krav för virtuell Azure-dator

Alla lokala virtuella datorer som replikeras till Azure måste uppfylla de krav för virtuella Azure-datorer som sammanfattas i den här tabellen. När Site Recovery kör en krav kontroll för replikering kommer kontrollen att Miss klar om några av kraven inte uppfylls.

**Komponentfilerna** | **Signaturkrav** | **Information**
--- | --- | ---
Gäst operativ system | Verifierar att virtuella VMware-operativsystem stöds för migrering.<br/> Du kan migrera alla arbets belastningar som körs på ett operativ system som stöds. | Kontrollen Miss lyckas om den inte stöds.
Gäst operativ systemets arkitektur | 64-bitars. | Kontrollen Miss lyckas om den inte stöds.
Storlek på operativ system disk | Upp till 2 048 GB. | Kontrollen Miss lyckas om den inte stöds.
Antal operativ system diskar | 81.1 | Kontrollen Miss lyckas om den inte stöds.
Antal data diskar | 64 eller mindre. | Kontrollen Miss lyckas om den inte stöds.
Data disk storlek | Upp till 4 095 GB | Kontrollen Miss lyckas om den inte stöds.
Nätverkskort | Flera nätverkskort stöds. |
Delad virtuell hård disk | Stöds inte. | Kontrollen Miss lyckas om den inte stöds.
FC-disk | Stöds inte. | Kontrollen Miss lyckas om den inte stöds.
Kryptering | Stöds inte. | BitLocker måste inaktive ras innan du aktiverar replikering för en dator.
Namn på virtuell dator | Mellan 1 och 63 tecken.<br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Dator namnet måste börja och sluta med en bokstav eller en siffra. |  Uppdatera värdet i dator egenskaperna i Site Recovery.
Anslut efter migreringen – Windows | Ansluta till virtuella Azure-datorer som kör Windows efter migrering:<br/> -Innan migreringen aktiverar RDP på den lokala virtuella datorn. Se till att TCP-och UDP-regler läggs till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **tillåtna appar**för alla profiler.<br/> För plats-till-plats-VPN-åtkomst aktiverar du RDP och Tillåt RDP i **Windows-brandväggen** -> **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk. Dessutom kontrollerar du att operativ systemets SAN-princip är inställd på **OnlineAll**. [Läs mer](prepare-for-migration.md). |
Anslut efter migreringen – Linux | Ansluta till virtuella Azure-datorer efter migrering med SSH:<br/> Innan migreringen går du till den lokala datorn, kontrollerar att tjänsten Secure Shell är inställt på Start och att brand Väggs reglerna tillåter en SSH-anslutning.<br/> Efter redundansväxlingen på den virtuella Azure-datorn tillåter du inkommande anslutningar till SSH-porten för reglerna för nätverks säkerhets grupper på den misslyckade virtuella datorn och för det Azure-undernät som den är ansluten till. Lägg också till en offentlig IP-adress för den virtuella datorn. |  


## <a name="next-steps"></a>Nästa steg

[Välj](server-migrate-overview.md) ett alternativ för VMware-migrering.
