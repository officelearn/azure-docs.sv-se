---
title: Azure Migrate-installation
description: Innehåller en översikt över Azure Migrate-installationen som används i Server utvärdering och migrering.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 6311f24a9c977b5f8b34384f0754f041a0c57ce7
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990750"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-installation

I den här artikeln beskrivs Azure Migrate-utrustningen. Du distribuerar installationen när du använder [Azure Migrate: Server utvärderings](migrate-services-overview.md#azure-migrate-server-assessment-tool) verktyg för att identifiera och utvärdera appar, infrastruktur och arbets belastningar för migrering till Microsoft Azure. Enheten används också när du migrerar virtuella VMware-datorer till Azure med hjälp av [Azure Migrate: Server utvärdering](migrate-services-overview.md#azure-migrate-server-migration-tool) med utan [agent migrering](server-migrate-overview.md).

## <a name="appliance-overview"></a>Apparat översikt

Azure Migrates apparaten används i följande scenarier.

**Scenario** | **Verktyg** | **Används för** 
--- | --- | ---
Virtuell VMware-dator | Azure Migrate: Server utvärdering<br/><br/> Azure Migrate: Server-migrering | Identifiera virtuella VMware-datorer<br/><br/> Identifiera maskin program och beroenden<br/><br/> Samla in metadata för datorns metadata och prestanda för utvärderingar.<br/><br/> Replikera virtuella VMware-datorer med migrering utan agent.
Virtuell Hyper-V-dator | Azure Migrate: Server utvärdering | Identifiera virtuella Hyper-V-datorer<br/><br/> Samla in metadata för datorns metadata och prestanda för utvärderingar.
Fysisk dator |  Azure Migrate: Server utvärdering |  Identifiera fysiska servrar<br/><br/> Samla in metadata för datorns metadata och prestanda för utvärderingar.

## <a name="appliance---vmware"></a>Utrustning – VMware 

**Krav** | **VMware** 
--- | ---
**Hämta format** | . CELLER 
**Nedladdnings länk** | https://aka.ms/migrate/appliance/vmware 
**Hämtnings storlek** | 11,2 GB
**Licensavtalet** | Mallen för hämtade installationer innehåller en utvärderings licens för Windows Server 2016, som är giltig i 180 dagar. Om utvärderings perioden ligger nära förfallo datum, rekommenderar vi att du laddar ned och distribuerar en ny installation, eller att du aktiverar operativ Systems licensen för den virtuella dator enheten.
**Distribution** | Du distribuerar installationen som en virtuell VMware-dator. Du behöver tillräckligt med resurser på vCenter Server för att allokera en virtuell dator med 32 GB RAM, 8 virtuella processorer, cirka 80 GB disk lagring och en extern virtuell växel.<br/> Enheten kräver Internet åtkomst, antingen direkt eller via en proxyserver.<br/> Enheten kan ansluta till en enda vCenter Server.
**Maskinvara** | Resurser på vCenter för att allokera en virtuell dator med 32 GB RAM 8 virtuella processorer, cirka 80 GB disk lagring och en extern virtuell växel. 
**Hash-värde** | MD5: c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256:4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**vCenter Server/värd** | Den virtuella datorn måste distribueras på en ESXi-värd som kör version 5,5 eller senare.<br/><br/> vCenter Server som kör 5,5, 6,0, 6,5 eller 6,7.
**Azure Migrate projekt** | En apparat kan associeras med ett enda projekt. <br/> Valfritt antal enheter kan associeras med ett enda projekt.<br/> 
**Identifiering** | En apparat kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter Server.<br/> En apparat kan ansluta till en enda vCenter Server.
**Utrustnings komponenter** | Hanterings app: webbapp i program för användarindata under distributionen.<br/> Identifierings agent: samlar in dator konfigurations data.<br/> Bedömnings agent: samla in prestanda data.<br/> DRA: dirigerar VM-replikering och koordinerar kommunikationen mellan datorer/Azure.<br/> Gateway: skickar replikerade data till Azure.<br/> Tjänsten automatisk uppdatering: uppdatera komponenter (körs var 24: e timme).
**VDDK (utan agent migrering)** | Om du kör en agent lös migrering med Azure Migrate Server-migrering, måste VMware vSphere VDDK installeras på den virtuella datorn.


## <a name="appliance---hyper-v"></a>Apparat-Hyper-V

**Krav** | **Hyper-V** 
--- | ---
**Hämta format** | Zippad mapp (med VHD)
**Nedladdnings länk** | https://aka.ms/migrate/appliance/hyperv 
**Hämtnings storlek** | 10 GB
**Licensavtalet** | Mallen för hämtade installationer innehåller en utvärderings licens för Windows Server 2016, som är giltig i 180 dagar. Om utvärderings perioden ligger nära förfallo datum, rekommenderar vi att du laddar ned och distribuerar en ny installation, eller att du aktiverar operativ Systems licensen för den virtuella dator enheten.
**Distribution av utrustning**   |  Du distribuerar installationen som en virtuell Hyper-V-dator.<br/> Den virtuella dator som tillhandahålls av Azure Migrate är Hyper-V VM version 5,0.<br/> Hyper-V-värden måste köra Windows Server 2012 R2 eller senare.<br/> Värden behöver tillräckligt med utrymme för att allokera 16 GB RAM-minne, 8 virtuella processorer, runt 80 GB lagrings utrymme och en extern växel för den virtuella datorns dator.<br/> Enheten behöver en statisk eller dynamisk IP-adress och Internet åtkomst.
**Maskinvara** | Resurser på Hyper-V-värden för att allokera 16 GB RAM-minne, 8 virtuella processorer, runt 80 GB lagrings utrymme och en extern växel för den virtuella datorns installation.
**Hash-värde** | MD5:29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256:37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Hyper-V-värd** | Kör Windows Server 2012 R2 eller senare.
**Azure Migrate projekt** | En apparat kan associeras med ett enda projekt. <br/> Valfritt antal enheter kan associeras med ett enda projekt.<br/> 
**Identifiering** | En apparat kan identifiera upp till 5000 virtuella VMware-datorer på en vCenter Server.<br/> En apparat kan ansluta till upp till 300 Hyper-V-värdar.
**Utrustnings komponenter** | Hanterings app: webbapp i program för användarindata under distributionen.<br/> Identifierings agent: samlar in dator konfigurations data.<br/> Bedömnings agent: samla in prestanda data.<br/>  Tjänsten automatisk uppdatering: uppdatera komponenter (körs var 24: e timme).


## <a name="appliance---physical"></a>Apparat-fysisk

**Krav** | **Mekanisk** 
--- | ---
**Hämta format** | Zippad mapp (med installations skript för PowerShell)
**Nedladdnings länk** | [Nedladdnings länk](https://go.microsoft.com/fwlink/?linkid=2105112)
**Hämtnings storlek** | 59,7 MB
**Maskinvara** | Dedikerad fysisk dator eller virtuell dator. Datorn som kör produkten behöver 16 GB RAM-minne, 8 virtuella processorer, cirka 80 GB lagrings utrymme och en extern växel.<br/> Enheten behöver en statisk eller dynamisk IP-adress och Internet åtkomst.
**Hash-värde** | MD5:1e92ede3e87c03bd148e56a708cdd33f<br/><br/> SHA256: a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c
**Programvara** | Installations datorn ska köra Windows Server 2016. 
**Distribution av utrustning**   |  Installations skriptet för installationen laddas ned från portalen (i en zippad mapp). <br/> Du packar upp mappen och kör PowerShell-skriptet (AzureMigrateInstaller. ps1).
**Identifiering** | En apparat kan identifiera upp till 250 fysiska servrar.
**Utrustnings komponenter** | Hanterings app: webbapp i program för användarindata under distributionen.<br/> Identifierings agent: samlar in dator konfigurations data.<br/> Bedömnings agent: samla in prestanda data.<br/>  Tjänsten automatisk uppdatering: uppdatera komponenter (körs var 24: e timme).


## <a name="url-access"></a>URL-åtkomst

Azure Migrate-utrustningen behöver anslutning till Internet.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad proxy för att ansluta till Internet kan du ge åtkomst till dessa URL: er och se till att proxyservern matchar eventuella CNAME-poster som tas emot när URL: erna genomsöks.

**URL** | **Detaljer**  
--- | --- |
*.portal.azure.com  | Navigera till Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Logga in på din Azure-prenumeration.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Skapa Active Directory appar för att kunna kommunicera med Azure Migrate.
management.azure.com | Skapa Active Directory appar för att kunna kommunicera med tjänsten Azure Migrate.
dc.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
*.vault.azure.net | Hantera hemligheter i Azure Key Vault.
aka.ms/* | Tillåt åtkomst till aka-länkar. Används för uppdateringar av Azure Migrates enheten.
download.microsoft.com/download | Tillåt hämtning från Microsoft Download.
*.servicebus.windows.net | **Används för migrering av VMware-agent**<br/><br/> Kommunikation mellan installations programmet och tjänsten Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | **Används för migrering av VMware-agent**<br/><br/> Anslut till Azure Migrate tjänst-URL: er.
*.blob.core.windows.net |  **Används för migrering av VMware-agent**<br/><br/>Ladda upp data till lagring.




## <a name="collected-data---vmware"></a>Insamlade data – VMware

### <a name="collected-performance-data-vmware"></a>Insamlade prestanda data – VMware

Här är de prestanda data för VMware VM som enheten samlar in och skickar till Azure.

**Data** | **Medelvärde** | **Utvärderings påverkan**
--- | --- | ---
CPU-användning | processor. Usage. genomsnitt | Rekommenderad storlek/kostnad för virtuell dator
Minnes användning | mem.usage.average | Rekommenderad storlek/kostnad för virtuell dator
Disk läsnings data flöde (MB per sekund) | virtualDisk. Read. Average | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Disk skrivnings data flöde (MB per sekund) | virtualDisk. Write. Average | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Disk läsnings åtgärder per sekund | virtualDisk. numberReadAveraged. Average | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Disk skrivnings åtgärder per sekund | virtualDisk.numberWriteAveraged.average  | Beräkning för disk storlek, lagrings kostnad, VM-storlek
NIC-läst data flöde (MB per sekund) | net.received.average | Beräkning för VM-storlek
NÄTVERKSKORT skriver data flöde (MB per sekund) | net.transmitted.average  |Beräkning för VM-storlek


### <a name="collected-metadata-vmware"></a>Insamlade metadata – VMware

> [!NOTE]
> Metadata som upptäckts av Azure Migrate-installationen används för att hjälpa dig att ge dina program rätt storlek när du migrerar dem till Azure, utför Azures analys av lämplighet, program beroende analys och kostnads planering. Microsoft använder inte dessa data i förhållande till någon granskning av licens efterlevnad.

Här är en fullständig lista över de virtuella VMware-metadata som enheten samlar in och skickar till Azure.

**Data** | **Medelvärde**
--- | --- 
**Dator information** | 
ID FÖR VIRTUELL DATOR | vm.Config.InstanceUuid 
VM-namn | vm.Config.Name
vCenter Server-ID | VMwareClient.Instance.Uuid
Beskrivning av virtuell dator | vm.Summary.Config.Annotation
Licens produkt namn | vm.Client.ServiceContent.About.LicenseProductName
Typ av operativ system | vm.SummaryConfig.GuestFullName
Start typ | vm.Config.Firmware
Antal kärnor | vm.Config.Hardware.NumCPU
Minne (MB) | datorn. Config. Hardware. MemoryMB
Antal diskar | datorn. Config. Hardware. Device. ToList (). FindAll (x = > är VirtualDisk). Count
Lista över disk storlekar | datorn. Config. Hardware. Device. ToList (). FindAll (x = > är VirtualDisk)
Lista med nätverkskort | datorn. Config. Hardware. Device. ToList (). FindAll (x = > är VirtualEthernet). Count
CPU-användning | processor. Usage. genomsnitt
Minnes användning |mem.usage.average
**Per disk information** | 
Disk nyckel värde | diskdefragmenter. Knapp
Dikunit-nummer | diskdefragmenter. UnitNumber
Nyckel värde för disk styrenhet | disk.ControllerKey.Value
Gigabyte etablerad | virtualDisk.DeviceInfo.Summary
Disk namn | Värde som genereras med disk. UnitNumber, disk. Nyckel, disk. ControllerKey. VAlue
Läs åtgärder per sekund | virtualDisk. numberReadAveraged. Average
Skriv åtgärder per sekund | virtualDisk.numberWriteAveraged.average
Läs data flöde (MB per sekund) | virtualDisk. Read. Average
Skriv data flöde (MB per sekund) | virtualDisk. Write. Average
**Per NIC-information** | 
Nätverkskortets namn | NIC. Knapp
MAC-adress | ((VirtualEthernetCard)nic).MacAddress
IPv4-adresser | vm.Guest.Net
IPv6-adresser | vm.Guest.Net
Läs data flöde (MB per sekund) | net.received.average
Skriv data flöde (MB per sekund) | net.transmitted.average
**Information om lager Sök väg** | 
Namn | container.GetType().Name
Typ av underordnat objekt | fönster. ChildType
Referens information | container.MoRef
Överordnad information | Container. parent
Information om mappar per virtuell dator | ((Mapp)-behållare). ChildEntity. Type
Data Center information per virtuell dator | ((Datacenter)container).VmFolder
Data Center information per värd-mapp | ((Data Center)-behållare). HostFolder
Kluster information per värd | ((ClusterComputeResource) container). Värd
Värd information per virtuell dator | ((HostSystem) container). DATORN

## <a name="collected-data---hyper-v"></a>Insamlade data – Hyper-V

### <a name="collected-performance-data-hyper-v"></a>Insamlade prestanda data – Hyper-V

> [!NOTE]
> Metadata som upptäckts av Azure Migrate-installationen används för att hjälpa dig att ge dina program rätt storlek när du migrerar dem till Azure, utför Azures analys av lämplighet, program beroende analys och kostnads planering. Microsoft använder inte dessa data i förhållande till någon granskning av licens efterlevnad.

Här är de prestanda data för virtuella Hyper-datorer som enheten samlar in och skickar till Azure.

**Prestanda räknar klass** | **Medelvärde** | **Utvärderings påverkan**
--- | --- | ---
Virtuell Hyper-V hypervisor-processor | % Kör tid för gäst | Rekommenderad storlek/kostnad för virtuell dator
VIRTUELL Hyper-V-dynamiskt minne | Aktuellt tryck (%)<br/> Fysiskt synligt fysiskt minne (MB) | Rekommenderad storlek/kostnad för virtuell dator
Virtuell lagrings enhet för Hyper-V | Lästa byte per sekund | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Virtuell lagrings enhet för Hyper-V | Skrivna byte/sekund | Beräkning för disk storlek, lagrings kostnad, VM-storlek
Hyper-V Virtual Network Adapter | Mottagna byte per sekund | Beräkning för VM-storlek
Hyper-V Virtual Network Adapter | Skickade byte per sekund | Beräkning för VM-storlek

- PROCESSOR användningen är summan av all användning, för alla virtuella processorer som är kopplade till en virtuell dator.
- Minnes användningen är (aktuellt tryck * fysiskt synligt minne i gäst)/100.
- Värdena för disk-och nätverks användning samlas in från de listade Hyper-V-prestandaräknare.

### <a name="collected-metadata-hyper-v"></a>Insamlade metadata – Hyper-V

Här är en fullständig lista över Hyper-V VM-metadata som enheten samlar in och skickar till Azure.

**Data** | **WMI-klass** | **Egenskap för WMI-klass**
--- | --- | ---
**Dator information** | 
Serie nummer för BIOS _ Msvm_BIOSElement | BIOSSerialNumber
VM-typ (gen 1 eller 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Visnings namn för virtuell dator | Msvm_VirtualSystemSettingData | ElementName
VM-version | Msvm_ProcessorSettingData | VirtualQuantity
Minne (byte) | Msvm_MemorySettingData | VirtualQuantity
Maximalt minne som kan utnyttjas av VM | Msvm_MemorySettingData | Gräns
Dynamiskt minne aktiverat | Msvm_MemorySettingData | DynamicMemoryEnabled
Operativ systemets namn/version/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems namn data
Energi status för virtuell dator | Msvm_ComputerSystem | EnabledState
**Per disk information** | 
Disk-ID | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ av virtuell hård disk | Msvm_VirtualHardDiskSettingData | Typ
Storlek på virtuell hård disk | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Överordnad virtuell hård disk | Msvm_VirtualHardDiskSettingData | ParentPath
**Per NIC-information** | 
IP-adresser (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | IP-adresser
DHCP aktiverat (syntetiska nätverkskort) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NÄTVERKSKORT-ID (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | Instans
MAC-adress för nätverkskort (syntetiska nätverkskort) | Msvm_SyntheticEthernetPortSettingData | Adress
NÄTVERKSKORT-ID (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting Data | Instans
MAC-ID för nätverkskort (äldre nätverkskort) | MsvmEmulatedEthernetPortSetting Data | Adress




## <a name="discovery-and-collection-process"></a>Process för identifiering och insamling

Enheten kommunicerar med vCenter-servrar och Hyper-V-värdar/-kluster med följande process.

1. **Starta identifiering**:
    - När du startar identifieringen på Hyper-V-enheten kommunicerar den med Hyper-V-värdarna på WinRM-portarna 5985 (HTTP) och 5986 (HTTPS).
    - När du startar identifiering på VMware-enheten kommunicerar den med vCenter-servern på TCP-port 443 som standard. OM vCenter-servern lyssnar på en annan port kan du konfigurera den i installations programmets webbapp.
2. **Samla in metadata och prestanda data**:
    - Enheten använder en Common Information Model-session (CIM) för att samla in virtuella Hyper-V-Datadata från Hyper-V-värden på portarna 5985 och 5986.
    - Enheten kommunicerar med port 443 som standard för att samla in virtuella VMware-Datadata från vCenter Server.
3. **Skicka data**: enheten skickar insamlade data till Azure Migrate Server utvärdering och migrering av Azure Migrate Server över SSL-port 443. Enheten kan ansluta till Azure via Internet, eller så kan du använda ExpressRoute med offentlig/Microsoft-peering.
    - För prestanda data samlar enheten in real tids användnings data.
        - Prestanda data samlas in var 20: e sekund för VMware och var 30: e sekund för Hyper-V för varje prestanda mått.
        - Insamlade data samlas in för att skapa en enda data punkt i 10 minuter.
        - Värdet för högsta användning väljs från alla data punkter med 20/30 sekunder och skickas till beräkningen av Azure för bedömning.
        - Baserat på percentilvärdet som anges i bedömnings egenskaperna (50/nittionde/95/99), sorteras 10-minuters punkterna i stigande ordning och lämpligt percentilvärdet används för att beräkna utvärderingen
    - Vid Server migrering börjar installations programmet samla in VM-data och replikerar dem till Azure.
4. **Utvärdera och migrera**: nu kan du skapa utvärderingar från de metadata som samlas in av installations programmet med hjälp av Azure Migrate Server bedömning. Dessutom kan du börja migrera virtuella VMware-datorer med hjälp av Azure Migrate Server-migrering för att dirigera agent lös VM-replikering.


![Arkitektur](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Installations program

Installationen uppgraderas eftersom Azure Migrate agenter som körs på enheten uppdateras.

- Detta sker automatiskt eftersom den automatiska uppdateringen är aktive rad som standard.
- Du kan ändra den här standardinställningen för att uppdatera agenterna manuellt.
- Om du vill inaktivera den automatiska uppdateringen går du till Registereditorn > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance och anger register nyckeln "AutoUpdate" till 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Konfigurera agent uppdateringar till manuell

För manuella uppdateringar ser du till att du uppdaterar alla agenter på installationen på samma gång, med knappen **Uppdatera** för varje föråldrad agent på enheten. Du kan när som helst växla uppdaterings inställningen tillbaka till automatiska uppdateringar.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](tutorial-assess-vmware.md#set-up-the-appliance-vm) du konfigurerar-installationen för VMware.
[Lär dig hur](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) du konfigurerar-enheten för Hyper-V.

