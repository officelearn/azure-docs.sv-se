---
title: Stöd för VMware-migrering i Azure Migrate
description: Läs mer om stöd för VMware VM-migrering i Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 9d8dc4dadc975a0fb69ea207f6062b72231460ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269515"
---
# <a name="support-matrix-for-vmware-migration"></a>Stödmatris för VMware-migrering

Den här artikeln sammanfattar supportinställningar och begränsningar för migrering av virtuella datorer med [Azure Migrate: Servermigrering](migrate-services-overview.md#azure-migrate-server-migration-tool) . Om du letar efter information om hur du bedömer virtuella virtuella datorer med VMware för migrering till Azure kan du läsa [utvärderingssupportmatrisen](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Migreringsalternativ

Du kan migrera virtuella virtuella datorer på ett par olika sätt:

- Med agentless migrering: Migrera virtuella datorer utan att behöva installera något på dem. Du distribuerar [Azure Migrate-enheten](migrate-appliance.md) för agentlös migrering.
- Med agentbaserad migrering: Installera en agent på den virtuella datorn för replikering. För agentbaserad migrering måste du distribuera en [replikeringsinstallation](migrate-replication-appliance.md).

Läs [den här artikeln](server-migrate-overview.md) för att ta reda på vilken metod du vill använda.

## <a name="migration-limitations"></a>Migreringsbegränsningar

- Du kan välja upp till 10 virtuella datorer samtidigt för replikering. Om du vill migrera fler datorer replikeras du i grupper om 10.
- För VMware agentless migrering kan du köra upp till 100 replikeringar samtidigt.

## <a name="agentless-vmware-servers"></a>Agentless-VMware servrar

**Vmware** | **Detaljer**
--- | ---
**VMware vCenter Server** | Version 5.5, 6.0, 6.5 eller 6.7.
**VMware vSphere ESXI värd** | Version 5.5, 6.0, 6.5 eller 6.7.
**vCenter-serverbehörigheter** | Agentless migration använder [Migrate Appliance](migrate-appliance.md). Apparaten behöver följande behörigheter:<br/><br/> - **Datastore.Browse:** Tillåt att du kan söka efter vm-loggfiler för att felsöka skapande och borttagning av ögonblicksbilder.<br/><br/> - **Datastore.LowLevelFileOperations**: Tillåt läs-/skriv-/borttagning/byt namn på åtgärder i datalagrets webbläsare för att felsöka skapande och borttagning av ögonblicksbilder.<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking**: Tillåt aktivera eller inaktivera ändringsspårning av VM-diskar, för att hämta ändrade datablock mellan ögonblicksbilder.<br/><br/> - **VirtualMachine.Configuration.DiskLease**: Tillåt disklåneåtgärder för en virtuell dator, för att läsa disken med VMware vSphere Virtual Disk Development Kit (VDDK).<br/><br/> - **VirtualMachine.Provisioning.AllowDiskAccess**: (särskilt för vSphere 6.0 och högre) Tillåt att öppna en disk på en virtuell dator för slumpmässig läsåtkomst på disken med HJÄLP av VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess**: Tillåt att öppna en disk på en virtuell dator, för att läsa disken med HJÄLP AV VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskRandomAccess**: Tillåt att öppna en disk på en virtuell dator, för att läsa disken med HJÄLP AV VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowVirtualMachineDownload**: Tillåter läsåtgärder på filer som är associerade med en virtuell dator, för att hämta loggarna och felsöka om fel inträffar.<br/><br/> - **VirtualMachine.SnapshotManagement.***: Tillåt skapande och hantering av ögonblicksbilder av virtuella datorer för replikering.<br/><br/> - **Virtual Machine.Interaction.Power Off**: Tillåt att den virtuella datorn stängs av under migreringen till Azure.



## <a name="agentless-vmware-vms"></a>Virtuella datorer med Agentless-VMware

**Support** | **Detaljer**
--- | ---
**Operativsystem som stöds** | [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) och [Linux-operativsystem](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) som stöds av Azure kan migreras med hjälp av agentless migrering.
**Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för följande operativsystem:<br/> - Red Hat Enterprise Linux 6,5 +, 7,0 +<br/> - CentOS 6,5+, 7,0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8<br/><br/> För andra operativsystem måste du göra justeringar manuellt före migreringen. De relevanta artiklarna innehåller instruktioner om hur du gör detta.
**Linux-start** | Om /boot finns på en dedikerad partition bör den finnas på OS-disken och inte spridas över flera diskar.<br/> Om /boot är en del av rotpartitionen (/) ska /-partitionen finnas på OS-disken och inte sträcka sig över andra diskar.
**UEFI-start** | Virtuella datorer med UEFI-start stöds inte för migrering.
**Diskstorlek** | 2 TB OS-disk; 4 TB för datadiskar.
**Diskgränser** |  Upp till 60 diskar per virtuell dator.
**Krypterade diskar/volymer** | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
**Delat diskkluster** | Stöds inte.
**Oberoende diskar** | Stöds inte.
**RDM/vidaregenomströmningsdiskar** | Om virtuella datorer har RDM- eller passthrough-diskar replikeras inte dessa diskar till Azure.
**NFS** | NFS-volymer monterade som volymer på de virtuella datorerna replikeras inte.
**iSCSI-mål** | Virtuella datorer med iSCSI-mål stöds inte för agentlös migrering.
**Multipath IO** | Stöds inte.
**Lagring vMotion** | Stöds inte. Replikering fungerar inte om en virtuell dator använder lagrings-vMotion.
**Nätverkskort i team** | Stöds inte.
**IPv6** | Stöds inte.
**Måldisk** | Virtuella datorer kan endast migreras till hanterade diskar (standard HDD, premium SSD) i Azure.
**Samtidig replikering** | 100 virtuella datorer per vCenter-server. Om du har fler migrerar du dem i grupper om 100.


## <a name="agentless-azure-migrate-appliance"></a>Agentless-Azure Migrera-apparat 
Agentless migrering använder Azure Migrate-enheten, som distribueras på en virtuell VMware-dator.

- Läs mer om [apparatkrav](migrate-appliance.md#appliance---vmware) för VMware.
- Läs mer om [webbadresser](migrate-appliance.md#url-access) som apparaten behöver komma åt.

## <a name="agentless-ports"></a>Agentless-portar

**Enhet** | **Anslutning**
--- | ---
Apparaten | Utgående anslutningar på port 443 för att överföra replikerade data till Azure och för att kommunicera med Azure Migrate-tjänster som dirigerar replikering och migrering.
vCenter Server | Inkommande anslutningar på port 443 så att installationen kan dirigera replikering - skapa ögonblicksbilder, kopiera data, släppa ögonblicksbilder
vSphere/EXSI-värd | Inkommande på TCP-port 902 för att installationen ska replikera data från ögonblicksbilder.


## <a name="agent-based-vmware-servers"></a>Agent-baserade VMware-servrar
Den här tabellen sammanfattar utvärderingsstöd och begränsningar för VMware-virtualiseringsservrar.

**Krav på VMware** | **Detaljer**
--- | ---
**VMware vCenter Server** | Version 5.5, 6.0, 6.5 eller 6.7.
**VMware vSphere ESXI värd** | Version 5.5, 6.0, 6.5 eller 6.7.
**vCenter-serverbehörigheter** | Ett skrivskyddat konto för vCenter Server.

## <a name="agent-based-vmware-vms"></a>Agent-baserade VMware virtuella datorer

Tabellen sammanfattar VMware VM-stöd för virtuella datorer med VMware som du vill migrera med hjälp av agentbaserad migrering.

**Support** | **Detaljer**
--- | ---
**Arbetsbelastning för datorer** | Azure Migrate stöder migrering av alla arbetsbelastningar (t.ex.
**Operativsystem** | Den senaste informationen finns i [operativsystemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) för webbplatsåterställning. Azure Migrate ger stöd för identiska virtuella operativsystem.
**Linux-filsystem/gästlagring** | Den senaste informationen finns i [stöd för Linux-filsystemet](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) för Site Recovery. Azure Migrate har identiska Linux-filsystem stöd.
**Nätverk/lagring** | Den senaste informationen finns i [nätverks-](../site-recovery/vmware-physical-azure-support-matrix.md#network) och [lagringsförutsättningarna](../site-recovery/vmware-physical-azure-support-matrix.md#storage) för webbplatsåterställning. Azure Migrate innehåller identiska nätverks-/lagringskrav.
**Krav för Azure** | Den senaste informationen finns i [Azure-nätverket,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [lagrings-](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)och [beräkningskraven](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) för site recovery. Azure Migrate har identiska krav för VMware-migrering.
**Mobilitetstjänst** | Mobilitetsserviceagenten måste installeras på varje virtuell dator som du vill migrera.
**UEFI-start** | Den migrerade virtuella datorn i Azure konverteras automatiskt till en VIRTUELL BIOS-start.<br/><br/> OS-disken ska ha upp till fyra partitioner och volymerna ska formateras med NTFS.
**Måldisk** | Virtuella datorer kan endast migreras till hanterade diskar (standard HDD, premium SSD) i Azure.
**Diskstorlek** | 2 TB OS-disk; 8 TB för datadiskar.
**Diskgränser** |  Upp till 63 diskar per virtuell dator.
**Krypterade diskar/volymer** | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering.
**Delat diskkluster** | Stöds inte.
**Oberoende diskar** | Stöds.
**Genomströmningsdiskar** | Stöds.
**NFS** | NFS-volymer monterade som volymer på de virtuella datorerna replikeras inte.
**iSCSI-mål** | Virtuella datorer med iSCSI-mål stöds inte för agentlös migrering.
**Multipath IO** | Stöds inte.
**Lagring vMotion** | Stöds
**Nätverkskort i team** | Stöds inte.
**IPv6** | Stöds inte.




## <a name="agent-based-replication-appliance"></a>Agentbaserad replikeringsapparat 

När du konfigurerar replikeringsverktyget med hjälp av OVA-mallen som finns i Azure Migrate-hubben kör installationen Windows Server 2016 och uppfyller supportkraven. Om du ställer in replikeringsverktyget manuellt på en fysisk server kontrollerar du att den uppfyller kraven.

- Läs mer om [krav på replikeringsinstallationer](migrate-replication-appliance.md#appliance-requirements) för VMware.
- MySQL måste installeras på apparaten. Läs mer om [installationsalternativ](migrate-replication-appliance.md#mysql-installation).
- Läs mer om [webbadresser](migrate-replication-appliance.md#url-access) och [portar](migrate-replication-appliance.md#port-access) som replikeringsverktyget behöver komma åt.

## <a name="agent-based-ports"></a>Agentbaserade portar

**Enhet** | **Anslutning**
--- | ---
Virtuella datorer | Mobilitetstjänsten som körs på virtuella datorer kommunicerar med den lokala replikeringsverktyget (konfigurationsservern) på inkommande port HTTPS 443 för replikeringshantering.<br/><br/> Virtuella datorer skickar replikeringsdata till processservern (som körs på konfigurationsservermaskinen) på inkommande port HTTPS 9443. Den här porten kan ändras.
Replikeringsverktyget | Replikeringsverktyget dirigerar replikering med Azure via port HTTPS 443 utgående.
Bearbeta server | Processservern tar emot replikeringsdata, optimerar och krypterar dem och skickar den till Azure-lagring via utgående port 443.<br/> Som standard körs processservern på replikeringsverktyget.

## <a name="azure-vm-requirements"></a>Virtuella Azure VMware-datorer

Alla lokala virtuella datorer som replikeras till Azure måste uppfylla azure vm-kraven som sammanfattas i den här tabellen. När Site Recovery kör en förutsättningskontroll för replikering misslyckas kontrollen om vissa av kraven inte uppfylls.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Gästoperativsystem | Verifierar vmware-vm-operativsystem som stöds för migrering.<br/> Du kan migrera alla arbetsbelastningar som körs på ett operativsystem som stöds. | Kontrollen misslyckas om den inte stöds.
Arkitektur för gästoperativsystem | 64-bitars. | Kontrollen misslyckas om den inte stöds.
Storlek på operativsystemdisk | Upp till 2 048 GB. | Kontrollen misslyckas om den inte stöds.
Antal operativsystemdiskar | 1 | Kontrollen misslyckas om den inte stöds.
Antal datadiskar | 64 eller mindre. | Kontrollen misslyckas om den inte stöds.
Storlek på datadisk | Upp till 4 095 GB | Kontrollen misslyckas om den inte stöds.
Nätverkskort | Flera kort stöds. |
Delad VHD | Stöds inte. | Kontrollen misslyckas om den inte stöds.
FC-disk | Stöds inte. | Kontrollen misslyckas om den inte stöds.
BitLocker | Stöds inte. | BitLocker måste inaktiveras innan du aktiverar replikering för en dator.
VM-namn | Från 1 till 63 tecken.<br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Maskinnamnet måste börja och sluta med en bokstav eller ett nummer. |  Uppdatera värdet i datoregenskaperna i Site Recovery.
Ansluta efter migrering-Windows | Så här ansluter du till virtuella Azure-datorer som kör Windows efter migreringen:<br/> - Innan migrering aktiverar RDP på den lokala virtuella datorn. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.<br/> För VPN-åtkomst på plats till plats aktiverar du RDP och tillåter RDP i **Windows-brandväggen** -> **Tillåtna appar och funktioner** för **domännätverk och privata** nätverk. Kontrollera dessutom att operativsystemets SAN-princip är inställd på **OnlineAll**. [Läs mer](prepare-for-migration.md). |
Anslut efter migrering-Linux | Så här ansluter du till virtuella Azure-datorer efter migreringen med SSH:<br/> Kontrollera att tjänsten Secure Shell är inställd på Start på den lokala datorn före migreringen och att brandväggsregler tillåter en SSH-anslutning.<br/> Efter redundans, på den virtuella Azure-datorn, tillåt inkommande anslutningar till SSH-porten för nätverkssäkerhetsgruppsreglerna på den misslyckade över den virtuella datorn och för Det Azure-undernät som det är anslutet till. Lägg dessutom till en offentlig IP-adress för den virtuella datorn. |  


## <a name="next-steps"></a>Nästa steg

[Välj](server-migrate-overview.md) ett VMware-migreringsalternativ.
