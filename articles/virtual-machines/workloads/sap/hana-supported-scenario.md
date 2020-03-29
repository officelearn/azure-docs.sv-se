---
title: Scenarier som stöds för SAP HANA på Azure (stora instanser)| Microsoft-dokument
description: Scenarier som stöds och deras arkitekturinformation för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617169"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Scenarier som stöds för STORA HANA-instanser
I den här artikeln beskrivs scenarier och arkitekturinformation som stöds för HLI (HLI).

>[!NOTE]
>Om det scenario som krävs inte nämns i den här artikeln kontaktar du Microsoft Service Management-teamet för att bedöma dina krav.
Innan du konfigurerar HLI-enheten validerar du designen med SAP eller din tjänstimplementeringspartner.

## <a name="terms-and-definitions"></a>Villkor och definitioner
Låt oss förstå de termer och definitioner som används i den här artikeln:

- **SID**: En systemidentifierare för HANA-systemet
- **HLI**: Hana stora instanser
- **DR**: Katastrofåterställning
- **Normal DR:** En systeminstallation med en dedikerad resurs endast för DR-ändamål
- **Multipurpose DR**: Ett DR-platssystem som är konfigurerat för att använda en icke-produktionsmiljö tillsammans med en produktionsinstans som är konfigurerad för en DR-händelse 
- **Single-SID**: Ett system med en instans installerad
- **Multi-SID**: Ett system med flera instanser konfigurerade; kallas även en MCOS-miljö
- **HSR**: SAP HANA-systemreplikering

## <a name="overview"></a>Översikt
Stora HANA-instanser har stöd för en mängd olika arkitekturer som hjälper dig att uppfylla dina affärsbehov. Följande avsnitt täcker arkitekturscenarier och deras konfigurationsinformation. 

Den härledda arkitekturdesignen är enbart ur ett infrastrukturperspektiv och du måste konsultera SAP eller dina implementeringspartner för HANA-distributionen. Om dina scenarier inte finns med i den här artikeln kontaktar du Microsoft-kontoteamet för att granska arkitekturen och härleda en lösning åt dig.

> [!NOTE]
> Dessa arkitekturer är helt kompatibla med designen för anpassad dataintegration (TDI) och stöds av SAP.

I den här artikeln beskrivs information om de två komponenterna i varje arkitektur som stöds:

- Ethernet
- Lagring

### <a name="ethernet"></a>Ethernet

Varje etablerad server är förkonfigurerad med uppsättningar Ethernet-gränssnitt. Ethernet-gränssnitten som konfigurerats på varje HLI-enhet kategoriseras i fyra typer:

- **A**: Används för eller av klientåtkomst.
- **B**: Används för nod-till-nodkommunikation. Det här gränssnittet är konfigurerat på alla servrar (oavsett vilken topologi som begärs) men används endast för skalningsscenarier.
- **C**: Används för anslutning mellan nod till lagring.
- **D**: Används för nod-till-iSCSI-enhetsanslutning för STONITH-installation. Det här gränssnittet konfigureras endast när en HSR-konfiguration begärs.  

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod-till-nod|
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Stonith |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Nod-till-nod|
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Stonith |

Du väljer gränssnittet baserat på topologin som är konfigurerad på HLI-enheten. Till exempel är gränssnittet "B" inställt för nod-till-nod kommunikation, vilket är användbart när du har en utskalning topologi konfigurerad. Det här gränssnittet används inte för ennod, uppskalningskonfigurationer. Mer information om gränssnittsanvändning finns i de scenarier som krävs (senare i den här artikeln). 

Om det behövs kan du definiera ytterligare NIC-kort på egen hand. Konfigurationerna för befintliga nätverkskort kan dock *inte* ändras.

>[!NOTE]
>Du kan hitta ytterligare gränssnitt som är fysiska gränssnitt eller bindning. Du bör bara överväga de tidigare nämnda gränssnitten för ditt användningsfall. Alla andra kan ignoreras.

Fördelningen för enheter med två tilldelade IP-adresser ska se ut så här:

- Ethernet "A" bör ha en tilldelad IP-adress som finns inom server-IP-pooladressintervallet som du har skickat till Microsoft. Denna IP-adress bör underhållas i katalogen */etc/hosts* i operativsystemet.

- Ethernet "C" bör ha en tilldelad IP-adress som används för kommunikation till NFS. Den här adressen behöver *inte* underhållas i katalogen *etc/hosts* för att tillåta instans-till-instanstrafik inom klienten.

För UTSKALNING AV HANA-system eller UTSKALNINGSDISTRIBUTION FÖR HANA är en bladkonfiguration med två tilldelade IP-adresser inte lämplig. Om du bara har två tilldelade IP-adresser och vill distribuera en sådan konfiguration kontaktar du SAP HANA på Azure Service Management. De kan tilldela dig en tredje IP-adress i en tredje VLAN. För HANA Large Instances-enheter med tre tilldelade IP-adresser på tre nätverkskortsportar gäller följande användningsregler:

- Ethernet "A" bör ha en tilldelad IP-adress som ligger utanför server-IP-pooladressintervallet som du har skickat till Microsoft. Denna IP-adress bör inte underhållas i *katalogen etc/hosts* i operativsystemet.

- Ethernet "B" bör underhållas uteslutande i *etc / värdar* katalogen för kommunikation mellan de olika instanserna. Dessa är DE IP-adresser som ska underhållas i utskalning HANA-konfigurationer som IP-adresser som HANA använder för konfigurationen mellan noden.

- Ethernet "C" bör ha en tilldelad IP-adress som används för kommunikation till NFS-lagring. Denna typ av adress bör inte underhållas i katalogen *etc/hosts.*

- Ethernet "D" bör endast användas för åtkomst till STONITH-enheter för Pacemaker. Det här gränssnittet krävs när du konfigurerar HANA System Replication och vill uppnå automatisk redundans av operativsystemet med hjälp av en SBD-baserad enhet.


### <a name="storage"></a>Lagring
Lagringen är förkonfigurerad baserat på den begärda topologin. Volymstorlekarna och monteringspunkterna varierar beroende på antalet servrar, antalet SKU:er och den konfigurerade topologin. Mer information finns i de scenarier som krävs (senare i den här artikeln). Om du behöver mer lagringsutrymme kan du köpa det i steg om 1 TB.

>[!NOTE]
>Monteringspunkten /usr/sap/\<SID> är en symbolisk länk till /hana/shared mount point.


## <a name="supported-scenarios"></a>Scenarier som stöds

Arkitekturdiagrammen i nästa avsnitt använder följande noteringar:

[![Tabell över arkitekturdiagram](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Här är scenarierna som stöds:

* Enkel nod med ett SID
* MCOS med en nod
* Enkel nod med DR (normal)
* Enkel nod med DR (multifunktion)
* HSR med STONITH
* HSR med DR (normal/mångsidig) 
* Automatisk redundans värd (1+1) 
* Skala ut med vänteläge
* Utskalning utan vänteläge
* Skala ut med DR

## <a name="single-node-with-one-sid"></a>Enkel nod med ett SID

Den här topologin stöder en nod i en skalningskonfiguration med ett SID.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enkel nod med ett SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerad men används inte |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurerad men används inte |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID | HANA-installation | 
|/hana/data/SID/mnt00001 | Installation av datafiler | 
|/hana/log/SID/mnt00001 | Installation av loggfiler | 
|/hana/logbackups/SID | Gör om loggar |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.

## <a name="single-node-mcos"></a>MCOS med en nod

Den här topologin stöder en nod i en uppskalningskonfiguration med flera SID.This topology supports one node in a scale-up configuration with multiple SIDs.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![MCOS med en nod](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerad men används inte |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurerad men används inte |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID1 | HANA installation för SID1 | 
|/hana/data/SID1/mnt00001 | Installation av datafiler för SID1 | 
|/hana/log/SID1/mnt00001 | Installation av loggfiler för SID1 | 
|/hana/logbackups/SID1 | Gör om loggar för SID1 |
|/hana/shared/SID2 | HANA installation för SID2 | 
|/hana/data/SID2/mnt00001 | Installation av datafiler för SID2 | 
|/hana/log/SID2/mnt00001 | Installation av loggfiler för SID2 | 
|/hana/logbackups/SID2 | Gör om loggar för SID2 |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- Volymstorleksfördelningen baseras på databasens storlek i minnet. Mer information om vilka databasstorlekar i minnet som stöds i en multi-SID-miljö finns i [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

## <a name="single-node-with-dr-using-storage-replication"></a>Enkel nod med DR med lagringsreplikering
 
Den här topologin stöder en nod i en uppskalningskonfiguration med en eller flera SID-enheter, med lagringsbaserad replikering till DR-platsen för ett primärt SID. I diagrammet visas endast ett en-SID-system på den primära platsen, men MCOS-system stöds också.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enkel nod med DR med lagringsreplikering](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerad men används inte |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurerad men används inte |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID | HANA-installation för SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för SID | 
|/hana/logbackups/SID | Gör om loggar för SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Volymstorleksfördelningen baseras på databasens storlek i minnet. Mer information om vilka databasstorlekar i minnet som stöds i en multi-SID-miljö finns i [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- På DR-platsen: Volymerna och monteringspunkterna är konfigurerade (markerade som "Krävs för HANA-installation") för produktions-HANA-instansinstallationen vid DR HLI-enheten. 
- På DR-platsen: Data, loggsäkerhetskopior och delade volymer (markerade som "Storage Replication") replikeras via ögonblicksbild från produktionsplatsen. Dessa volymer monteras endast under redundans. Mer information finns i [Felsäkerhetsövervikelse.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- Startvolymen för *SKU Type I-klass* replikeras till DR-noden.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Enkel nod med DR (multifunktion) med lagringsreplikering
 
Den här topologin stöder en nod i en uppskalningskonfiguration med en eller flera SID-enheter, med lagringsbaserad replikering till DR-platsen för ett primärt SID. I diagrammet visas endast ett en-SID-system på den primära platsen, men mcos-system (multi-SID) stöds också. På DR-platsen används HLI-enheten för QA-instansen medan produktionsoperationer körs från den primära platsen. Under DR-redundans (eller redundanstest) tas QA-instansen på DR-platsen ned.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enkel nod med DR (multifunktion) med lagringsreplikering](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerad men används inte |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurerad men används inte |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |
|**På DR-platsen**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/shared/QA-SID | HANA-installation för QA SID | 
|/hana/data/QA-SID/mnt00001 | Installation av datafiler för QA SID | 
|/hana/log/QA-SID/mnt00001 | Installation av loggfiler för QA SID |
|/hana/logbackups/QA-SID | Gör om loggar för QA SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Volymstorleksfördelningen baseras på databasens storlek i minnet. Mer information om vilka databasstorlekar i minnet som stöds i en multi-SID-miljö finns i [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- På DR-platsen: Volymerna och monteringspunkterna är konfigurerade (markerade som "Krävs för HANA-installation") för produktions-HANA-instansinstallationen vid DR HLI-enheten. 
- På DR-platsen: Data, loggsäkerhetskopior och delade volymer (markerade som "Storage Replication") replikeras via ögonblicksbild från produktionsplatsen. Dessa volymer monteras endast under redundans. Mer information finns i [Felsäkerhetsövervikelse.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 
- På DR-platsen: Data, loggsäkerhetskopior, loggsäkerhet och delade volymer för QA (markerade som "QA-instansinstallation") är konfigurerade för QA-instansinstallationen.
- Startvolymen för *SKU Type I-klass* replikeras till DR-noden.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR med STONITH för hög tillgänglighet
 
Den här topologin stöder två noder för HANA System Replication-konfigurationen. Den här konfigurationen stöds endast för enskilda HANA-instanser på en nod. Det innebär att MCOS-scenarier *inte* stöds.

> [!NOTE]
> Från och med december 2019 stöds den här arkitekturen endast för SUSE-operativsystemet.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![HSR med STONITH för hög tillgänglighet](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerad men används inte |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Används för STONITH |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurerad men används inte |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Används för STONITH |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |
|**På den sekundära noden**|
|/hana/shared/SID | HANA-installation för sekundärt SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för sekundärt SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för sekundärt SID | 
|/hana/logbackups/SID | Gör om loggar för sekundärt SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Volymstorleksfördelningen baseras på databasens storlek i minnet. Mer information om vilka databasstorlekar i minnet som stöds i en multi-SID-miljö finns i [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- STONITH: En SBD har konfigurerats för STONITH-installationen. Användningen av STONITH är dock frivillig.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Hög tillgänglighet med HSR och DR med lagringsreplikering
 
Den här topologin stöder två noder för HANA System Replication-konfigurationen. Både normala och multifunktionella DR stöds. Dessa konfigurationer stöds endast för enskilda HANA-instanser på en nod. Det innebär att MCOS-scenarier *inte* stöds med dessa konfigurationer.

I diagrammet visas ett multifunktionsscenario på DR-platsen, där HLI-enheten används för QA-instansen medan produktionsoperationer körs från den primära platsen. Under DR-redundans (eller redundanstest) tas QA-instansen på DR-platsen ned. 

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Hög tillgänglighet med HSR och DR med lagringsreplikering](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerad men används inte |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Används för STONITH |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurerad men används inte |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Används för STONITH |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På den primära noden på den primära platsen**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |
|**På den sekundära noden på den primära platsen**|
|/hana/shared/SID | HANA-installation för sekundärt SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för sekundärt SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för sekundärt SID | 
|/hana/logbackups/SID | Gör om loggar för sekundärt SID |
|**På DR-platsen**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/shared/QA-SID | HANA-installation för QA SID | 
|/hana/data/QA-SID/mnt00001 | Installation av datafiler för QA SID | 
|/hana/log/QA-SID/mnt00001 | Installation av loggfiler för QA SID |
|/hana/logbackups/QA-SID | Gör om loggar för QA SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Volymstorleksfördelningen baseras på databasens storlek i minnet. Mer information om vilka databasstorlekar i minnet som stöds i en multi-SID-miljö finns i [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- STONITH: En SBD har konfigurerats för STONITH-installationen. Användningen av STONITH är dock frivillig.
- På DR-platsen: *Två uppsättningar lagringsvolymer krävs* för primär och sekundär nodreplikering.
- På DR-platsen: Volymerna och monteringspunkterna är konfigurerade (markerade som "Krävs för HANA-installation") för produktions-HANA-instansinstallationen vid DR HLI-enheten. 
- På DR-platsen: Data, loggsäkerhetskopior och delade volymer (markerade som "Storage Replication") replikeras via ögonblicksbild från produktionsplatsen. Dessa volymer monteras endast under redundans. Mer information finns i [Felsäkerhetsövervikelse.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 
- På DR-platsen: Data, loggsäkerhetskopior, loggsäkerhet och delade volymer för QA (markerade som "QA-instansinstallation") är konfigurerade för QA-instansinstallationen.
- Startvolymen för *SKU Type I-klass* replikeras till DR-noden.


## <a name="host-auto-failover-11"></a>Automatisk redundans värd (1+1)
 
Den här topologin stöder två noder i en automatisk redundanskonfiguration för värd. Det finns en nod med en huvud-/arbetsroll och en annan som vänteläge. *SAP stöder det här scenariot endast för S/4 HANA.* Mer information finns i [OSS note 2408419 - SAP S/4HANA - Multi-Node Support](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Automatisk redundans värd (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Nod-till-nod-kommunikation |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På huvud- och standbynoderna**|
|/hana/delad | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |



### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- I vänteläge: Volymerna och monteringspunkterna är konfigurerade (markerade som "Krävs för HANA-installation") för HANA-instansinstallationen vid standby-enheten.
 

## <a name="scale-out-with-standby"></a>Skala ut med vänteläge
 
Den här topologin stöder flera noder i en skalningskonfiguration. Det finns en nod med en huvudroll, en eller flera noder med en arbetsroll och en eller flera noder som vänteläge. Det kan dock bara finnas en huvudnod vid en enda tidpunkt.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Skala ut med vänteläge](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Nod-till-nod-kommunikation |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På master-, arbetar- och standbynoderna**|
|/hana/delad | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |


## <a name="scale-out-without-standby"></a>Utskalning utan vänteläge
 
Den här topologin stöder flera noder i en skalningskonfiguration. Det finns en nod med en huvudroll och en eller flera noder med en arbetarroll. Det kan dock bara finnas en huvudnod vid en enda tidpunkt.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Utskalning utan vänteläge](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Nod-till-nod-kommunikation |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På master- och arbetarnoderna**|
|/hana/delad | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Skala ut med DR med lagringsreplikering
 
Den här topologin stöder flera noder i en utskalning med en DR. Både normala och multifunktionella DR stöds. I diagrammet visas endast det enda syftet DR. Du kan begära den här topologin med eller utan standby-noden.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Skala ut med DR med lagringsreplikering](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Nod-till-nod-kommunikation |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/hana/delad | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |
|**På DR-noden**|
|/hana/delad | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
-  På DR-platsen: Volymerna och monteringspunkterna är konfigurerade (markerade som "Krävs för HANA-installation") för produktions-HANA-instansinstallationen vid DR HLI-enheten. 
- På DR-platsen: Data, loggsäkerhetskopior och delade volymer (markerade som "Storage Replication") replikeras via ögonblicksbild från produktionsplatsen. Dessa volymer monteras endast under redundans. Mer information finns i [Felsäkerhetsövervikelse.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 
- Startvolymen för *SKU Type I-klass* replikeras till DR-noden.


## <a name="single-node-with-dr-using-hsr"></a>Enkel nod med DR med HSR
 
Den här topologin stöder en nod i en skalningskonfiguration med ett SID, med HANA-systemreplikering till DR-platsen för ett primärt SID. I diagrammet visas endast ett en-SID-system på den primära platsen, men mcos-system (multi-SID) stöds också.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enkel nod med DR med HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerad men används inte |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurerad men används inte |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade på båda HLI-enheterna (Primär och DR):

| Montera punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID | HANA-installation för SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för SID | 
|/hana/logbackups/SID | Gör om loggar för SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Volymstorleksfördelningen baseras på databasens storlek i minnet. Mer information om vilka databasstorlekar i minnet som stöds i en multi-SID-miljö finns i [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- Den primära noden synkroniseras med DR-noden med hjälp av HANA System Replication. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) används för att länka Ihop ExpressRoute-kretsarna för att skapa ett privat nätverk mellan dina regionala nätverk.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Enkel nod HSR till DR (kostnadsoptimerad) 
 
 Den här topologin stöder en nod i en skalningskonfiguration med ett SID, med HANA-systemreplikering till DR-platsen för ett primärt SID. I diagrammet visas endast ett en-SID-system på den primära platsen, men mcos-system (multi-SID) stöds också. På DR-platsen används en HLI-enhet för QA-instansen medan produktionsoperationer körs från den primära platsen. Under DR-redundans (eller redundanstest) tas QA-instansen på DR-platsen ned.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enkel nod HSR till DR (kostnadsoptimerad)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerad men används inte |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurerad men används inte |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |
|**På DR-platsen**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |
|/hana/shared/QA-SID | HANA-installation för QA SID | 
|/hana/data/QA-SID/mnt00001 | Installation av datafiler för QA SID | 
|/hana/log/QA-SID/mnt00001 | Installation av loggfiler för QA SID |
|/hana/logbackups/QA-SID | Gör om loggar för QA SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Volymstorleksfördelningen baseras på databasens storlek i minnet. Mer information om vilka databasstorlekar i minnet som stöds i en multi-SID-miljö finns i [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- På DR-platsen: Volymerna och monteringspunkterna är konfigurerade (markerade som "PROD Instance at DR site") för produktions-HANA-instansinstallationen vid DR HLI-enheten. 
- På DR-platsen: Data, loggsäkerhetskopior, loggsäkerhet och delade volymer för QA (markerade som "QA-instansinstallation") är konfigurerade för QA-instansinstallationen.
- Den primära noden synkroniseras med DR-noden med hjälp av HANA System Replication. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) används för att länka Ihop ExpressRoute-kretsarna för att skapa ett privat nätverk mellan dina regionala nätverk.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Hög tillgänglighet och haveriberedskap med HSR 
 
 Den här topologin stöder två noder för HANA System Replication-konfigurationen för de lokala regionernas hög tillgänglighet. För DR synkroniseras den tredje noden i DR-regionen med den primära platsen med HSR (async-läge). 

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Hög tillgänglighet och haveriberedskap med HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerad men används inte |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurerad men används inte |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |
|**På DR-platsen**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- På DR-platsen: Volymerna och monteringspunkterna är konfigurerade (markerade som "PROD DR-instans") för produktions-HANA-instansinstallationen vid DR HLI-enheten. 
- Den primära platsnoden synkroniseras med DR-noden med hjälp av HANA System Replication. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) används för att länka Ihop ExpressRoute-kretsarna för att skapa ett privat nätverk mellan dina regionala nätverk.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Hög tillgänglighet och haveriberedskap med HSR (kostnadsoptimerad)
 
 Den här topologin stöder två noder för HANA System Replication-konfigurationen för de lokala regionernas hög tillgänglighet. För DR synkroniseras den tredje noden i DR-regionen med den primära platsen med HSR (async-läge), medan en annan instans (till exempel QA) redan håller på att ta från DR-noden. 

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Hög tillgänglighet och haveriberedskap med HSR (kostnadsoptimerad)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerad men används inte |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurerad men används inte |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |
|**På DR-platsen**|
|/hana/shared/SID | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |
|/hana/shared/QA-SID | HANA-installation för QA SID | 
|/hana/data/QA-SID/mnt00001 | Installation av datafiler för QA SID | 
|/hana/log/QA-SID/mnt00001 | Installation av loggfiler för QA SID |
|/hana/logbackups/QA-SID | Gör om loggar för QA SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- På DR-platsen: Volymerna och monteringspunkterna är konfigurerade (markerade som "PROD DR-instans") för produktions-HANA-instansinstallationen vid DR HLI-enheten. 
- På DR-platsen: Data, loggsäkerhetskopior, loggsäkerhet och delade volymer för QA (markerade som "QA-instansinstallation") är konfigurerade för QA-instansinstallationen.
- Den primära platsnoden synkroniseras med DR-noden med hjälp av HANA System Replication. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) används för att länka Ihop ExpressRoute-kretsarna för att skapa ett privat nätverk mellan dina regionala nätverk.

## <a name="scale-out-with-dr-using-hsr"></a>Skala ut med DR med HSR
 
Den här topologin stöder flera noder i en utskalning med en DR. Du kan begära den här topologin med eller utan standby-noden. Den primära platsnoden synkroniseras med DR-platsnoden med hjälp av HANA System Replication (async-läge).


### <a name="architecture-diagram"></a>Arkitekturdiagram  

[![Skala ut med DR med HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| Logiskt nätverkskort | SKU-typ | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-till-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Nod till lagring |
| D | TYP I | eth4.hyresgäst | eno4.tenant | Konfigurerad men används inte |
| A | TYP II | vlan\<hyresgästEn> | team0.tenant | Klient-till-HLI/HSR |
| B | TYP II | vlan\<tenantNo+2> | team0.tenant+2 | Nod-till-nod-kommunikation |
| C | TYP II | vlan\<tenantNo+1> | team0.tenant+1 | Nod till lagring |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurerad men används inte |

### <a name="storage"></a>Lagring
Följande monteringspunkter är förkonfigurerade:

| Montera punkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/hana/delad | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |
|**På DR-noden**|
|/hana/delad | HANA installation för produktion SID | 
|/hana/data/SID/mnt00001 | Installation av datafiler för produktion SID | 
|/hana/log/SID/mnt00001 | Installation av loggfiler för produktion SID | 
|/hana/logbackups/SID | Gör om loggar för produktion SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till /hana/shared/SID.
- På DR-platsen: Volymerna och monteringspunkterna är konfigurerade för produktions-HANA-instansinstallationen vid DR HLI-enheten. 
- Den primära platsnoden synkroniseras med DR-noden med hjälp av HANA System Replication. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) används för att länka Ihop ExpressRoute-kretsarna för att skapa ett privat nätverk mellan dina regionala nätverk.


## <a name="next-steps"></a>Nästa steg
- [Infrastruktur och anslutning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) för STORA HANA-instanser
- [Hög tillgänglighet och haveriberedskap](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) för STORA HANA-instanser
