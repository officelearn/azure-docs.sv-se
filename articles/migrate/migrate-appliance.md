---
title: Azure Migrate-installation
description: Ger en översikt över Azure Migrate-installationen som används vid serverutvärdering och migrering.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 1c21f06e674871aefde1ae952a459db16feeb717
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676342"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-installation

Den här artikeln sammanfattar kraven och supportkraven för Azure Migrate-installationen. 

## <a name="deployment-scenarios"></a>Distributionsscenarier

Azure Migrate-enheten används i följande scenarier.

**Scenario** | **Verktyg** | **Används för** 
--- | --- | ---
**VMware VM-utvärdering** | Azure Migrera:Server-utvärdering | Upptäck virtuella datorer med VMware<br/><br/> Upptäck datorappar och beroenden<br/><br/> Samla in datorns metadata och prestandametadata för utvärderingar.
**VMware VM-agentlös migrering** | Azure Migrera:Servermigrering | Upptäck virtuella datorer med VMware <br/><br/> Replikera virtuella virtuella datorer med agentlös migrering.
**Bedömning av virtuella datorer med hyper-V** | Azure Migrera:Server-utvärdering | Upptäck virtuella virtuella hyper-virtuella datorer<br/><br/> Samla in datorns metadata och prestandametadata för utvärderingar.
**Fysisk maskinbedömning** |  Azure Migrera:Server-utvärdering |  Upptäck fysiska servrar (eller virtuella datorer som du behandlar som fysiska servrar).<br/><br/> Samla in datorns metadata och prestandametadata för utvärderingar.

## <a name="deployment-methods"></a>Distributionsmetoder

Apparaten kan användas med ett par metoder:

- Apparaten kan distribueras med hjälp av en mall för virtuella datorer och virtuella video-datorer med hyper-v (OVA-mall för VMware eller VHD för Hyper-V).
- Om du inte vill använda en mall kan du distribuera apparaten för VMware eller Hyper-V med ett PowerShell-skript.
- I Azure Government bör du distribuera enheten med ett skript.
- För fysiska servrar distribuerar du alltid apparaten med ett skript.
- Nedladdningslänkar finns i tabellerna nedan.


## <a name="appliance---vmware"></a>Apparat - VMware 

I följande tabell sammanfattas Azure Migrate-installationskraven för VMware.

**Krav** | **Vmware** 
--- | ---
**Komponenter till apparaten** | Apparaten har följande komponenter:<br/><br/> - **Hanteringsapp:** Det här är en webbapp för användarinmatning under distribution av installationen. Används vid bedömning av datorer för migrering till Azure.<br/> - **Identifieringsagent:** Agenten samlar in maskinkonfigurationsdata. Används vid bedömning av datorer för migrering till Azure.<br/>- **Bedömningsagent:** Agenten samlar in prestandadata. Används vid bedömning av datorer för migrering till Azure.<br/>- **Automatisk uppdatering:** Uppdaterar apparatens komponenter (körs var 24:e timme).<br/>- **DRA-agent**: Dirigerar VM-replikering och samordnar kommunikationen mellan replikerade datorer och Azure. Används endast vid replikerande virtuella virtuella datorer med VMware till Azure med hjälp av agentless migrering.<br/>- **Gateway**: Skickar replikerade data till Azure. Används endast vid replikerande virtuella virtuella datorer med VMware till Azure med hjälp av agentless migrering.
**Distribution som stöds** | Distribuera som virtuell VMware-dator med OVA-mall.<br/><br/> Distribuera som en virtuell virtuell dator eller en fysisk dator med PowerShell-installationsskriptet.
**Projektstöd** |  En apparat kan associeras med ett enda projekt. <br/> Valfritt antal apparater kan associeras med ett enda projekt.<br/> 
**Begränsningar för identifiering** | En apparat kan upptäcka upp till 10 000 virtuella virtuella datorer på en vCenter-server.<br/> En apparat kan ansluta till en enda vCenter-server.
**OVA-mall** | Ladda ner från https://aka.ms/migrate/appliance/vmwareportalen eller från .<br/><br/> Nedladdningsstorleken är 11,2 GB.<br/><br/> Den nedladdade installationen mallen levereras med en Windows Server 2016 utvärderingslicens, som är giltig i 180 dagar. Om utvärderingsperioden är nära att löpa ut rekommenderar vi att du hämtar och distribuerar en ny installation, eller att du aktiverar operativsystemets licens för den virtuella datorn för den virtuella datorn för den virtuella datorn.
**PowerShell-skript** | Script [ladda ner](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Programvara/maskinvara** |  Installationen ska köras på datorn med Windows Server 2016, 32 GB RAM, 8 virtuella processorer, cirka 80 GB disklagring och en extern virtuell växel.<br/> Apparaten kräver tillgång till internet, antingen direkt eller via en proxy.<br/><br/> Om du kör installationen på en virtuell virtuell VMware-dator behöver du tillräckligt med resurser på vCenter-servern för att allokera en virtuell dator som uppfyller kraven.<br/><br/> Om du kör apparaten på en fysisk dator kontrollerar du att den kör Windows Server 2016 och uppfyller maskinvarukraven. 
**Krav på VMware** | Om du distribuerar enheten som en virtuell VMware-dator måste den distribueras på en ESXi-värd som kör version 5.5 eller senare.<br/><br/> vCenter Server som kör 5,5, 6,0, 6,5 eller 6,7.
**VDDK (agentless migrering)** | Om du distribuerar enheten som en virtuell VMware-dator och kör en agentlös migrering måste VMware vSphere VDDK installeras på den virtuella datorn för den virtuella datorn.
**Hash värde-OVA** | [Verifiera](tutorial-assess-vmware.md#verify-security) OVA-mallhh-värdena.
**Hash-värde-PowerShell-skript** | [Verifiera](deploy-appliance-script.md#verify-file-security) powershell-skripthh-värdena.




## <a name="appliance---hyper-v"></a>Apparat - Hyper-V

**Krav** | **Hyper-V** 
--- | ---
**Komponenter till apparaten** | Apparaten har följande komponenter:<br/><br/>- **Hanteringsapp:** Det här är en webbapp för användarinmatning under distribution av installationen. Används vid bedömning av datorer för migrering till Azure.<br/> - **Identifieringsagent:** Agenten samlar in maskinkonfigurationsdata. Används vid bedömning av datorer för migrering till Azure.<br/>- **Bedömningsagent:** Agenten samlar in prestandadata. Används vid bedömning av datorer för migrering till Azure.<br/>- **Automatisk uppdatering:** Uppdaterar apparatens komponenter (körs var 24:e timme).
**Distribution som stöds** | Distribuera som virtuell dator med Hyper-V med hjälp av en VHD-mall.<br/><br/> Distribuera som en virtuell hyper-V-dator eller en fysisk dator med hjälp av ett PowerShell-installationsskript.
**Projektstöd** |  En apparat kan associeras med ett enda projekt. <br/> Valfritt antal apparater kan associeras med ett enda projekt.<br/> 
**Begränsningar för identifiering** | En apparat kan upptäcka upp till 5000 virtuella virtuella hyper-virtuella datorer.<br/> En apparat kan ansluta till upp till 300 Hyper-V-värdar.
**VHD-mall** | Zippad mapp inklusive VHD. Ladda ner från https://aka.ms/migrate/appliance/hypervportalen eller från .<br/><br/> Nedladdningsstorleken är 10 GB.<br/><br/> Den nedladdade installationen mallen levereras med en Windows Server 2016 utvärderingslicens, som är giltig i 180 dagar. Om utvärderingsperioden är nära att löpa ut rekommenderar vi att du hämtar och distribuerar en ny installation, eller att du aktiverar operativsystemets licens för den virtuella datorn för den virtuella datorn för den virtuella datorn.
**PowerShell-skript** | Script [ladda ner](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Programvara/maskinvara***   |  Installationen ska köras på datorn med Windows Server 2016, 32 GB RAM, 8 virtuella processorer, cirka 80 GB disklagring och en extern virtuell växel.<br/> Apparaten behöver en statisk eller dynamisk IP-adress och kräver internetuppkoppling, antingen direkt eller via en proxy.<br/><br/> Om du kör installationen som en Virtuell Hyper-V behöver du tillräckligt med resurser på Hyper-V-värden för att allokera 16 GB RAM, 8 vCPUs, cirka 80 GB lagringsutrymme och en extern växel för den virtuella datorn för den virtuella datorn för den virtuella datorn.<br/><br/> Om du kör apparaten på en fysisk dator kontrollerar du att den kör Windows Server 2016 och uppfyller maskinvarukraven. 
**Hyper-V-krav** | Om du distribuerar enheten med VHD-mallen är den virtuella datorn för den virtuella enheten som tillhandahålls av Azure Migrate Hyper-V VM version 5.0.<br/><br/> Hyper-V-värden måste köra Windows Server 2012 R2 eller senare. 
**Hash-värde-VHD** | [Verifiera](tutorial-assess-hyper-v.md#verify-security) VHD-mallhh-värdena.
**Hash-värde-PowerShell-skript** | [Verifiera](deploy-appliance-script.md#verify-file-security) powershell-skripthh-värdena.


## <a name="appliance---physical"></a>Apparat - Fysisk

**Krav** | **Fysiska** 
--- | ---
**Komponenter till apparaten** | Apparaten har följande komponenter: <br/><br/> - **Hanteringsapp:** Det här är en webbapp för användarinmatning under distribution av installationen. Används vid bedömning av datorer för migrering till Azure.<br/> - **Identifieringsagent:** Agenten samlar in maskinkonfigurationsdata. Används vid bedömning av datorer för migrering till Azure.<br/>- **Bedömningsagent:** Agenten samlar in prestandadata. Används vid bedömning av datorer för migrering till Azure.<br/>- **Automatisk uppdatering:** Uppdaterar apparatens komponenter (körs var 24:e timme).
**Distribution som stöds** | Distribuera som en dedikerad fysisk dator, eller en virtuell dator, med hjälp av ett PowerShell-installationsskript. Skriptet är tillgängligt för nedladdning från portalen.
**Projektstöd** |  En apparat kan associeras med ett enda projekt. <br/> Valfritt antal apparater kan associeras med ett enda projekt.<br/> 
**Begränsningar för identifiering** | En apparat kan upptäcka upp till 250 fysiska servrar.
**PowerShell-skript** | Hämta skriptet (AzureMigrateInstaller.ps1) i en zippad mapp från portalen. [Läs mer](tutorial-assess-physical.md#set-up-the-appliance). Alternativt kan [du ladda ner direkt](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> Nedladdningsstorleken är 59,7 MB.
**Programvara/maskinvara** |  Installationen ska köras på datorn med Windows Server 2016, 32 GB RAM, 8 virtuella processorer, cirka 80 GB disklagring och en extern virtuell växel.<br/> Apparaten behöver en statisk eller dynamisk IP-adress och kräver internetuppkoppling, antingen direkt eller via en proxy.<br/><br/> Om du kör apparaten på en fysisk dator kontrollerar du att den kör Windows Server 2016 och uppfyller maskinvarukraven. 
**Hash-värde** | [Verifiera](deploy-appliance-script.md#verify-file-security) powershell-skripthh-värdena.

## <a name="url-access"></a>URL-åtkomst

Azure Migrate-enheten behöver anslutning till internet.

- När du distribuerar installationen gör Azure Migrate en anslutningskontroll till de webbadresser som krävs.
- Om du använder en URL-baserad proxy för att ansluta till internet måste du tillåta åtkomst till dessa webbadresser och se till att proxyn löser alla CNAME-poster som tas emot när webbadresserna slås upp.

### <a name="public-cloud-urls"></a>Offentliga molnadresser

**URL** | **Detaljer**  
--- | --- |
*.portal.azure.com  | Navigera till Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Logga in på din Azure-prenumeration.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Skapa Azure Active Directory (AD)-appar för att installationen ska kunna kommunicera med Azure Migrate.
management.azure.com | Skapa Azure AD-appar för att installationen ska kommunicera med Azure Migrate-tjänsten.
dc.services.visualstudio.com | Ladda upp apploggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.
aka.ms/* | Ge tillgång till aka länkar. Används för Azure Migrate-installationer.
download.microsoft.com/download | Tillåt nedladdningar från Microsoft-nedladdning.
*.servicebus.windows.net | Kommunikation mellan installationen och Azure Migrate-tjänsten.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Ansluta till Azure Migrera tjänstadresser.
*.hypervrecoverymanager.windowsazure.com | **Används för VMware agentless migration**<br/><br/> Ansluta till Azure Migrera tjänstadresser.
*.blob.core.windows.net |  **Används för VMware agentless migration**<br/><br/>Ladda upp data till lagring för migrering.

### <a name="government-cloud-urls"></a>Url:er för molnet från myndigheter

**URL** | **Detaljer**  
--- | --- |
*.portal.azure.us  | Navigera till Azure Portal.
graph.windows.net | Logga in på din Azure-prenumeration.
login.microsoftonline.us  | Skapa Azure Active Directory (AD)-appar för att installationen ska kunna kommunicera med Azure Migrate.
management.usgovcloudapi.net | Skapa Azure AD-appar för att installationen ska kommunicera med Azure Migrate-tjänsten.
dc.services.visualstudio.com | Ladda upp apploggar som används för intern övervakning.
*.vault.usgovcloudapi.net | Hantera hemligheter i Azure Key Vault.
aka.ms/* | Ge tillgång till aka länkar. Används för Azure Migrate-installationer.
download.microsoft.com/download | Tillåt nedladdningar från Microsoft-nedladdning.
*.servicebus.usgovcloudapi.net  | Kommunikation mellan installationen och Azure Migrate-tjänsten.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Ansluta till Azure Migrera tjänstadresser.
*.hypervrecoverymanager.windowsazure.us | **Används för VMware agentless migration**<br/><br/> Ansluta till Azure Migrera tjänstadresser.
*.blob.core.usgovcloudapi.net  |  **Används för VMware agentless migration**<br/><br/>Ladda upp data till lagring för migrering.
*.applicationinsights.us | Ladda upp apploggar som används för intern övervakning.





## <a name="collected-data---vmware"></a>Insamlade data - VMware

Installationen samlar in metadata, prestandadata och beroendeanalysdata (om agentlös [beroendeanalys](concepts-dependency-visualization.md) används).

### <a name="metadata"></a>Metadata

Metadata som identifieras av Azure Migrate-enheten hjälper dig att ta reda på om datorer och appar är redo för migrering till Azure, datorer och appar i rätt storlek, planerar kostnader och analyserar programberoenden. Microsoft använder inte dessa data i någon granskning av licensefterlevnad.

Här är den fullständiga listan över VMware VM-metadata som installationen samlar in och skickar till Azure.

**Data** | **Counter**
--- | --- 
**Maskininformation** | 
VM-ID | vm. Config.instanceUuid 
VM-namn | vm. Config.Name
vCenter Server-ID | VMwareClient.Instance.Uuid
Beskrivning av den virtuella datorn | vm. Sammanfattning.Config.Anteckning
Licensproduktnamn | vm. Client.serviceContent.About.LicenseProductName
Typ av operativsystem | vm. SammanfattningConfig.GuestFullName
Starttyp | vm. Config.Firmware (inbyggda)
Antal kärnor | vm. Config.Hardware.NumCPU
Minne (MB) | vm. Config.Hardware.MemoryMB
Antal diskar | vm. Config.hardware.device.toList(). FindAll(x => är VirtualDisk).antal
Lista över diskstorlek | vm. Config.hardware.device.toList(). FindAll(x => är VirtualDisk)
Lista över nätverkskort | vm. Config.hardware.device.toList(). FindAll(x => är VirtualEthernet).count
CPU-användning | cpu.usage.genomsnitt
Minnesanvändning |mem.usage.average
**Information om disk per disk** | 
Värde för disknyckel | Disk. Nyckel
Dikunit-nummer | Disk. Enhetsnummer
Nyckelvärde för diskstyrenhet | Disk. ControllerKey.Värde
Gigabyte som etablerats | virtualDisk.DeviceInfo.Sammanfattning
Disknamn | Värde som genereras med disk. UnitNumber, disk. Nyckel, disk. ControllerKey.VAlue
Läs åtgärder per sekund | virtualDisk.numberReadAveraged.average
Skrivåtgärder per sekund | virtualDisk.numberWriteAveraged.average
Läs dataflöde (MB per sekund) | virtualDisk.read.average
Skriva dataflöde (MB per sekund) | virtualDisk.write.average
**Information om nätverkskortet per nätverkskort** | 
Namn på nätverkskort | Nic. Nyckel
MAC-adress | ((VirtualEthernetCard)nic). MacAddress (på ett sätt)
IPv4-adresser | vm. Guest.Net
IPv6-adresser | vm. Guest.Net
Läs dataflöde (MB per sekund) | net.received.genomsnitt
Skriva dataflöde (MB per sekund) | net.transmitted.genomsnitt
**Information om lagersökväg** | 
Namn | Behållare. GetType(). Namn
Typ av underordnade objekt | Behållare. ChildType (barn)
Referensinformation | Behållare. MoRef (moref)
Överordnad information | Behållare.Överordnad
Mappinformation per virtuell dator | ((Mapp)behållare). Underordnade.
Datacenterinformation per virtuell dator | ((Datacenter)-behållaren). VmFolder (olikartade)
Datacenterinformation per värdmapp | ((Datacenter)-behållaren). HostFolder (olikartade)
Klusterinformation per värd | ((ClusterComputeResource)-behållaren). Värd
Värdinformation per virtuell dator | ((HostSystem)-behållaren). VM

### <a name="performance-data"></a>Prestandadata


Här är VMware VM-prestandadata som installationen samlar in och skickar till Azure.

**Data** | **Counter** | **Bedömningskonsekvens**
--- | --- | ---
CPU-användning | cpu.usage.genomsnitt | Rekommenderad VM-storlek/kostnad
Minnesanvändning | mem.usage.average | Rekommenderad VM-storlek/kostnad
Diskavläsningsdataflöde (MB per sekund) | virtualDisk.read.average | Beräkning för diskstorlek, lagringskostnad, VM-storlek
Diskskrivningar av dataflöde (MB per sekund) | virtualDisk.write.average | Beräkning för diskstorlek, lagringskostnad, VM-storlek
Diskläsningsåtgärder per sekund | virtualDisk.numberReadAveraged.average | Beräkning för diskstorlek, lagringskostnad, VM-storlek
Diskskrivningar åtgärder per sekund | virtualDisk.numberWriteAveraged.average  | Beräkning för diskstorlek, lagringskostnad, VM-storlek
NIC-läsflöde (MB per sekund) | net.received.genomsnitt | Beräkning för VM-storlek
Nätverkskortet skriver dataflöde (MB per sekund) | net.transmitted.genomsnitt  |Beräkning för VM-storlek

### <a name="app-dependencies-metadata"></a>Metadata för appberoenden

Agentless beroende analys samlar in anslutning och processdata.

#### <a name="connection-data"></a>Anslutningsdata

Här är anslutningsdata som enheten samlar in från varje virtuell dator som är aktiverad för agentlös beroendeanalys. Dessa data skickas till Azure.

**Data** | **Kommando som används** 
--- | --- 
Lokal port | Netstat
Lokal IP-adress | Netstat
Fjärrport | Netstat
Fjärr-IP-adress | Netstat
TCP-anslutningstillstånd | Netstat
Process-ID | Netstat
Nej. av aktiva anslutningar | Netstat

#### <a name="process-data"></a>Bearbeta data
Här är processdata som enheten samlar in från varje virtuell dator aktiverad för agentlös beroendeanalys. Dessa data skickas till Azure.

**Data** | **WMI-klass** | **Egenskapen WMI-klass**
--- | --- | ---
Processnamn | Win32_Process | Körbarpath
Processargument | Win32_Process | Kommandorad
Programnamn | Win32_Process | Parametern VersionInfo.ProductName för egenskapen ExecutablePath

#### <a name="linux-vm-data"></a>Linux VM-data

Här är anslutnings- och processdata som installationen samlar in från varje Virtuell Linux-dator som är aktiverad för agentlös beroendeanalys. Dessa data skickas till Azure.

**Data** | **Kommando som används** 
--- | ---
Lokal port | Netstat 
Lokal IP-adress | Netstat 
Fjärrport | Netstat 
Fjärr-IP-adress | Netstat 
TCP-anslutningstillstånd | Netstat 
Nej. av aktiva anslutningar | Netstat
Process-ID  | Netstat 
Processnamn | Ps
Processargument | Ps
Programnamn | dpkg eller rpm



## <a name="collected-data---hyper-v"></a>Insamlade data - Hyper-V

Installationen samlar in metadata, prestandadata och beroendeanalysdata (om agentlös [beroendeanalys](concepts-dependency-visualization.md) används).

### <a name="metadata"></a>Metadata
Metadata som identifieras av Azure Migrate-enheten hjälper dig att ta reda på om datorer och appar är redo för migrering till Azure, datorer och appar i rätt storlek, planerar kostnader och analyserar programberoenden. Microsoft använder inte dessa data i någon granskning av licensefterlevnad.

Här är den fullständiga listan över Hyper-V VM-metadata som installationen samlar in och skickar till Azure.

**Data* | **WMI-KLASS** | **EGENSKAPEN WMI-KLASS**
--- | --- | ---
**Maskininformation** | 
Serienummer för BIOS _ Msvm_BIOSElement | BIOSSerialAntal
VM-typ (Gen 1 eller 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Vm-visningsnamn | Msvm_VirtualSystemSettingData | ElementName
VM-version | Msvm_ProcessorSettingData | Virtuell kvantitet
Minne (byte) | Msvm_MemorySettingData | Virtuell kvantitet
Maximalt minne som kan förbrukas av vm | Msvm_MemorySettingData | Gräns
Dynamiskt minne aktiverat | Msvm_MemorySettingData | DynamicMemoryEnabled
Operativsystemnamn/version/FQDN | Msvm_KvpExchangeComponent | GästIntrinsicExchangeItems namndata
Vm-strömstatus | Msvm_ComputerSystem | EnabledState
**Information om disk per disk** | 
Diskidentifierare | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Virtuell hårddisktyp | Msvm_VirtualHardDiskSettingData | Typ
Virtuell hårddiskstorlek | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Överordnad virtuell hårddisk | Msvm_VirtualHardDiskSettingData | ÖverordnadPath
**Information om nätverkskortet per nätverkskort** | 
IP-adresser (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP-aktiverade (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-ID (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC-adress (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | Adress
NIC-ID (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting Data | InstanceID
NIC MAC-ID (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting Data | Adress

### <a name="performance-data"></a>Prestandadata

Här är hyper-VM-prestandadata som installationen samlar in och skickar till Azure.

**Klass för prestandaräknare** | **Counter** | **Bedömningskonsekvens**
--- | --- | ---
Hyper-V Hypervisor virtuell processor | % körtid för gäster | Rekommenderad VM-storlek/kostnad
Hyper-V virtuell dator med dynamiskt minne | Nuvarande tryck (%)<br/> Gäst synligt fysiskt minne (MB) | Rekommenderad VM-storlek/kostnad
Hyper-V virtuell lagringsenhet | Läs byte/sekund | Beräkning för diskstorlek, lagringskostnad, VM-storlek
Hyper-V virtuell lagringsenhet | Skriv byte/sekund | Beräkning för diskstorlek, lagringskostnad, VM-storlek
Virtuellt nätverkskort hyper-V | Mottagna byte/sekund | Beräkning för VM-storlek
Virtuellt nätverkskort hyper-V | Skickade byte/sekund | Beräkning för VM-storlek

- CPU-användning är summan av all användning, för alla virtuella processorer som är anslutna till en virtuell dator.
- Minnesanvändning är (Aktuellt tryck * Gäst synligt fysiskt minne) / 100.
- Disk- och nätverksanvändningsvärden samlas in från de listade hyper-V-prestandaräknarna.

## <a name="appliance-upgrades"></a>Uppgraderingar av apparater

Installationen uppgraderas när Azure Migrate-agenter som körs på installationen uppdateras. Detta sker automatiskt eftersom automatisk uppdatering är aktiverad på apparaten som standard. Du kan ändra den här standardinställningen för att uppdatera agenterna manuellt.

- **Inaktivera automatisk uppdatering:** Du inaktiverar automatisk uppdatering i registret genom att ställa in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate"-tangenten till 0 (DWORD). Om du bestämmer dig för att använda manuella uppdateringar är det viktigt att uppdatera alla agenter på apparaten samtidigt med hjälp av **uppdateringsknappen** för varje föråldrad agent på apparaten.
- **Uppdatera manuellt:** För manuella uppdateringar, se till att du uppdaterar alla agenter på apparaten med hjälp av **uppdateringsknappen** för varje föråldrad agent på apparaten. Du kan när som helst växla tillbaka uppdateringsinställningen till automatiska uppdateringar.

![Uppdatera apparaten automatiskt](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>Nästa steg

- [Läs om hur du](how-to-set-up-appliance-vmware.md) ställer in apparaten för VMware.
- [Läs om hur du](how-to-set-up-appliance-hyper-v.md) ställer in apparaten för Hyper-V.
- [Läs om hur du](how-to-set-up-appliance-physical.md) konfigurerar installationen av apparaten för fysiska servrar.

