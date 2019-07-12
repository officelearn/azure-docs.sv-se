---
title: Azure Migrate-installation-arkitektur | Microsoft Docs
description: Översikt över Azure Migrate-installation
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c2c9ca3082aa9c2067a63f8d6304e8a229dac14a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811458"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-installation

Den här artikeln beskriver Azure Migrate-installationen. Du kan distribuera installationen när du använder Verktyg för Azure Migrate-utvärdering och migrering för att identifiera, utvärdera och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. 

[Azure Migrate](migrate-services-overview.md) ger en central knutpunkt för att spåra identifiering, bedömning och migrering av dina lokala appar och arbetsbelastningar och privata/offentliga virtuella datorer till Azure. Hubben innehåller Azure Migrate verktyg för bedömning och migrering, samt tredjepartsprogram oberoende programvaruleverantörer (ISV)-erbjudanden.



## <a name="appliance-overview"></a>Översikt över installation

Azure Migrate typer för installation och användning finns på följande sätt.

**Distribueras som** | **Används för** | **Detaljer**
--- | --- |  ---
Virtuell VMware-dator | VMware-VM-utvärdering med verktyget Azure Migrate-utvärdering.<br/><br/> Agentlös VMware VM-migrering med verktyget Azure Migrate-servermigrering | Hämta OVA-mallen och importera till vCenter Server för att skapa virtuell dator.
Hyper-V-dator | Hyper-V VM-utvärdering med verktyget Azure Migrate-utvärdering. | Hämta komprimerade virtuella Hårddisken och importera till Hyper-V för att skapa virtuell dator.

## <a name="appliance-access"></a>Installationen åtkomst

När du har konfigurerat installationen kan du fjärransluta till installationen VM via TCP-port 3389. Du kan även fjärransluta till management-webbapp för installation på port 44368 med URL: ``` https://<appliance-ip-or-name>:44368 ```.

## <a name="appliance-license"></a>Licens för installation
Enheten som levereras med en utvärderingslicens för Windows Server 2016 som är giltig i 180 dagar. Om utvärderingsperioden är nära förfallodatum, rekommenderar vi att du hämtar och distribuerar en ny installation eller att du aktiverar licensen VM-enhetens operativsystem.

## <a name="appliance-agents"></a>Agenter för installation
Installationen har dessa agenter som installerats.

**Agent** | **Detaljer**
--- | ---
Identifieringsagent | Samlar in konfigurationsdata från lokala virtuella datorer.
Utvärdering av agent | Den lokala miljön för att samla in prestandadata för VM-profiler.
Nätverkskort för migrering | Samordnar replikering av virtuella datorer och samordnar kommunikationen mellan virtuella datorer och Azure.
Migrering gateway | Skickar replikerade VM-data till Azure.


## <a name="appliance-deployment-requirements"></a>Krav på installation

- [Granska](migrate-support-matrix-vmware.md#assessment-appliance-requirements) distributionskraven för en VMware-installationen och de webbadresser som enheten som behöver åtkomst till.
- [Granska](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) distributionskraven för en Hyper-V-installationen och de webbadresser som enheten som behöver åtkomst till.


## <a name="collected-performance-data-vmware"></a>Insamlade prestanda data – VMware

Här är de VMware VM-prestandadata som installationen samlar in och skickar till Azure.

**Data** | **Räknaren** | **Utvärdering av påverkan**
--- | --- | ---
CPU-användning | cpu.usage.average | Rekommenderade VM-storlek/kostnad
Minnesanvändning | mem.usage.average | Rekommenderade VM-storlek/kostnad
Disk-lästa dataflöde (MB per sekund) | virtualDisk.read.average | Beräkning för Diskstorlek, kostnaden för lagring, VM-storlek
Dataflöde per skrivning till disk (MB per sekund) | virtualDisk.write.average | Beräkning för Diskstorlek, kostnaden för lagring, VM-storlek
Disk-lästa-åtgärder per sekund | virtualDisk.numberReadAveraged.average | Beräkning för Diskstorlek, kostnaden för lagring, VM-storlek
Disk skrivåtgärder per sekund | virtualDisk.numberWriteAveraged.average  | Beräkning för Diskstorlek, kostnaden för lagring, VM-storlek
Läsningsdataflöde som NIC (MB per sekund) | net.received.average | Beräkning för VM-storlek
NIC genomströmning för skrivning (MB per sekund) | net.transmitted.average  |Beräkning för VM-storlek


## <a name="collected-metadata-vmware"></a>Insamlade metadata – VMware

Här är en fullständig lista över VMware VM-metadata som installationen samlar in och skickar till Azure.

**Data** | **Räknaren**
--- | --- 
**Information om dator** | 
ID FÖR VIRTUELL DATOR | vm.Config.InstanceUuid 
VM-namn | vm.Config.Name
vCenter Server-ID | VMwareClient.Instance.Uuid
Beskrivning av virtuell dator | vm.Summary.Config.Annotation
Licens produktnamn | vm.Client.ServiceContent.About.LicenseProductName
Typ av operativsystem | vm.SummaryConfig.GuestFullName
Starttyp | vm.Config.Firmware
Antal kärnor | vm.Config.Hardware.NumCPU
Minne (MB) | vm.Config.Hardware.MemoryMB
Antal diskar | den virtuella datorn. Config.Hardware.Device.ToList(). FindAll(x => is VirtualDisk).count
Disklista storlek | den virtuella datorn. Config.Hardware.Device.ToList(). FindAll (x = > är VirtualDisk)
Listan över nätverkskort | den virtuella datorn. Config.Hardware.Device.ToList(). FindAll(x => is VirtualEthernet).count
CPU-användning | cpu.usage.average
Minnesanvändning |mem.usage.average
**Per diskinformation** | 
Värdet för disk-nyckeln | disk. Nyckel
Dikunit tal | disk.UnitNumber
Nyckelvärdet för disk-domänkontrollant | disk.ControllerKey.Value
Gigabyte etablerats | virtualDisk.DeviceInfo.Summary
Disknamn | Värde som genereras med hjälp av disk. UnitNumber, disk. Nyckel, disk. ControllerKey.VAlue
Läsåtgärder per sekund | virtualDisk.numberReadAveraged.average
Skrivåtgärder per sekund | virtualDisk.numberWriteAveraged.average
Läs dataflöde (MB per sekund) | virtualDisk.read.average
Skriva dataflöde (MB per sekund) | virtualDisk.write.average
**Per NIC-information** | 
Nätverkskortets namn | nic.Key
MAC-adress | ((VirtualEthernetCard)nic).MacAddress
IPv4-adresser | vm.Guest.Net
IPv6-adresser | vm.Guest.Net
Läs dataflöde (MB per sekund) | net.received.average
Skriva dataflöde (MB per sekund) | net.transmitted.average
**Inventeringsinformation för sökvägen** | 
Namn | container.GetType().Name
Typ av underordnade objekt | behållaren. ChildType
Referensinformation | container.MoRef
Information om överordnade | Container.Parent
Mappdetaljer per virtuell dator | ((Mapp) behållare). ChildEntity.Type
Datacenter information per virtuell dator | ((Datacenter)container).VmFolder
Datacenter information per värd mapp | ((Datacenter)container).HostFolder
Information om kluster per värd | ((ClusterComputeResource)container).Host
Information om värden per virtuell dator | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Insamlade prestanda data Hyper-V

Här är de VMware VM-prestandadata som installationen samlar in och skickar till Azure.

**Prestandaräknaren-klass** | **Räknaren** | **Utvärdering av påverkan**
--- | --- | ---
Virtuell Hyper-V Hypervisor-Processor | % Körningstid för gäst | Rekommenderade VM-storlek/kostnad
Dynamiskt minne för Hyper-V virtuell dator | Aktuell belastning (%)<br/> Gästen synliga fysiskt minne (MB) | Rekommenderade VM-storlek/kostnad
Hyper-V virtuell lagringsenhet | Läs byte per sekund | Beräkning för Diskstorlek, kostnaden för lagring, VM-storlek
Hyper-V virtuell lagringsenhet | Skriva byte per sekund | Beräkning för Diskstorlek, kostnaden för lagring, VM-storlek
Hyper-V-virtuellt nätverkskort | Byte som tagits emot per sekund | Beräkning för VM-storlek
Hyper-V-virtuellt nätverkskort | Byte som skickats per sekund | Beräkning för VM-storlek

- CPU-användning är summan av all användning för alla virtuella processorer som är ansluten till en virtuell dator.
- Minnesanvändningen är (aktuell minnesbelastning * gäst synliga fysiskt minne) / 100.
- Disk- och användning ska samlas in från de angivna Hyper-V-prestandaräknarna.

## <a name="collected-metadata-hyper-v"></a>Insamlade metadata Hyper-V

Här är en fullständig lista över Hyper-V VM-metadata som installationen samlar in och skickar till Azure.

**Data** | **WMI-klass** | **Egenskap för WMI-klass**
--- | --- | ---
**Information om dator** | 
Serial number of BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Typ av virtuell dator (Gen 1 eller 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Visningsnamn för virtuell dator | Msvm_VirtualSystemSettingData | ElementName
VM-version | Msvm_ProcessorSettingData | VirtualQuantity
Minne (byte) | Msvm_MemorySettingData | VirtualQuantity
Högsta mängd minne som kan användas av en virtuell dator | Msvm_MemorySettingData | Gräns
Dynamiskt minne aktiverat | Msvm_MemorySettingData | DynamicMemoryEnabled
Operativsystemet namn/version/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsciExchangeItems namndata
Strömstatusen | Msvm_ComputerSystem | EnabledState
**Per diskinformation** | 
Diskens identifierare | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ av virtuell hårddisk | Msvm_VirtualHardDiskSettingData | type
Storlek på virtuell hårddisk | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Överordnad virtuell hårddisk | Msvm_VirtualHardDiskSettingData | ParentPath
**Per NIC-information** | 
IP-adresser (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | IP-adresser
DHCP aktiverat (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-ID (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC-MAC-adress (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | Adress
NIC-ID (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting Data | InstanceID
NIC-ID för MAC (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting Data | Adress




## <a name="discovery-and-collection-process"></a>Processen för identifiering och samling

Enheten som kommunicerar med vCenter-servrar och Hyper-V-värdar/kluster på följande sätt.


1. **Starta identifieringen**:
    - När du startar identifieringen på Hyper-V-installationen, som den kommunicerar med Hyper-V-värdar i WinRM-port 5985 (HTTP) och 5986 (HTTPS).
    - När du startar en identifiering på VMware-installationen, som den kommunicerar med vCenter-server på TCP-port 443 som standard. Om vCenter-servern lyssnar på en annan port ska konfigurera du det i installationen webbapp.
2. **Samla in metadata-och prestandadata**:
    - Installationen använder en Common Information Model (CIM)-session för att samla in data för Hyper-V-dator från Hyper-V-värden på port 5985 och 5986.
    - Enheten som kommunicerar med port 443 som standard att samla in data för VMware VM från vCenter-servern.
3. **Skicka data**: Enheten som skickar insamlade data till Azure Migrate Server-utvärdering och Azure Migrate servermigrering via SSL-port 443.
    - Installationen samlar in användningsdata i realtid för prestandadata.
        - Prestandadata samlas in var 20: e sekund för VMware och med 30 sekunders mellanrum för Hyper-V, för varje prestandamått.
        - Insamlade data lyfts upp för att skapa en enskild datapunkt tio minuter.
        - Värdet för högsta användning är valt från alla 20/30 andra datapunkter och skickas till Azure för utvärdering av beräkning.
        - Baserat på percentilvärdet som angetts i egenskaperna för utvärdering (50/90/95: e/99th), de tio minuter punkterna är sorterade i stigande ordning och lämpliga percentilvärdet som används för att beräkna utvärderingen
    - Installationen för migrering, börjar samla in data för virtuell dator och replikerar den till Azure.
4. **Utvärdera och migrera**: Du kan nu skapa utvärderingar från metadata som samlas in av installationen med hjälp av Azure Migrate Server-utvärdering. Dessutom kan du också börja migrera virtuella VMware-datorer med hjälp av Azure Migrate servermigrering för att dirigera replikering av virtuella datorer utan Agent.


![Arkitektur](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Installationen uppgraderingar

Installationen uppgraderas eftersom Azure Migrate-agenter som körs på enheten som har uppdaterats.

- Detta sker automatiskt eftersom inställningarna för automatisk uppdatering är aktiverad på enheten som standard.
- Du kan ändra den här standardinställningen för att uppdatera agenterna manuellt.
- Ange registernyckeln Appliance automatisk uppdatering i HKLM\SOFTWAREMicrosoft\Azure om du vill inaktivera automatiska uppdateringar.

### <a name="set-agent-updates-to-manual"></a>Ange agent uppdateringar till manuell

Manuella uppdateringar, se till att du uppdaterar alla agenter i installationen på samma gång, med hjälp av den **uppdatera** knappen för varje agent som är inaktuella i installationen. Du kan växla inställningen av uppdatering tillbaka till automatiska uppdateringar när som helst.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](tutorial-assess-vmware.md#set-up-the-appliance-vm) att ställa in installationen för VMware.
[Lär dig hur](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) att ställa in installationen för Hyper-V.

