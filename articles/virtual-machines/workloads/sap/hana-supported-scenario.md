---
title: Stöd för scenarier SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Scenarier som stöds och deras arkitektur för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e8253238bf5edb5e0ea3f89fe67d6aa39f4a2d7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855463"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Scenarier som stöds för stora HANA-instanser
Det här dokumentet beskriver scenarierna som stöds med arkitektur information för HANA stora instanser (HLI).

>[!NOTE]
>Om ditt nödvändiga scenario inte nämns här kontaktar du Microsoft Service Management-teamet för att utvärdera dina krav.
Verifiera design med SAP eller din implementationspartner för tjänsten innan du fortsätter med etablering HLI-enheter.

## <a name="terms-and-definitions"></a>Termer och definitioner
Låt oss se de termer och definitioner som används i dokumentet.

- SID: System-ID för HANA-system.
- HLI: Stora Hana-instanser.
- DR: En plats för katastrofåterställning.
- Normal DR: En system-installation med en dedikerad resurs för DR ändamål som endast används.
- Flera syften DR: Ett system på DR-plats som konfigurerats för att använda icke-produktionsmiljö tillsammans med produktion-instans som konfigurerats för att använda på DR-händelse. 
- Enkel SID:  Ett system med en instans installerad.
- Multi-SID: Ett system med flera instanser som har konfigurerats. Kallas även en MCOS-miljö.


## <a name="overview"></a>Översikt
HANA stora instanser stöder olika arkitekturer för att utföra dina affärsbehov. I följande lista beskrivs scenarier och deras konfigurationsinformation. 

Den härledda arkitekturdesign är helt och hållet ur infrastruktur och du måste läsa SAP eller din implementering partners för HANA-distribution. Kontakta Microsoft-kontoteamet om du vill granska arkitekturen och få en lösning för dig om dina scenarier inte visas i listan.

**Dessa arkitekturer är helt kompatibel med TDI (skräddarsydda dataintegrering)-design och stöds av SAP.**

Det här dokumentet innehåller information om de båda komponenterna i varje arkitektur som stöds:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Varje server som etablerats är förkonfigurerad med uppsättningar med Ethernet-gränssnitt. Här följer information om ethernet-gränssnitt som konfigurerats på varje HLI-enhet.

- **A**: Det här gränssnittet används för/av klientåtkomst.
- **B**: Det här gränssnittet används för nod till nod-kommunikation. Det här gränssnittet är konfigurerat på alla servrar (oavsett vilken topologi som begärs) men bara användas för den 
- skalbara scenarier.
- **C**: Det här gränssnittet används för noden att storage-anslutning.
- **D**: Det här gränssnittet används för noden att ISCSI-enhetsanslutning för STONITH installation. Det här gränssnittet konfigureras bara när krävs HSR-inställningar.  

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod till nod |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | STONITH |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Nod till nod |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | STONITH |

Du kan använda de gränssnitt som baserat på topologin som konfigurerats på HLI-enhet. Till exempel ställs gränssnittet ”B” in för nod till nod-kommunikation, vilket är användbart när du har en skalbar-topologi som har konfigurerats. När det gäller nod skala upp konfigurationen används inte det här gränssnittet. Granska dina krävs scenarier (senare i det här dokumentet) om du vill ha mer information om hur gränssnittet. 

Om det behövs kan du definiera ytterligare NIC-kort på egen hand. Konfigurationen på de befintliga nätverkskort kan inte ändras.

>[!NOTE]
>Ytterligare gränssnitt fysiska gränssnitt eller partnerskap som kanske fortfarande. Du bör de ovan nämnda gränssnitt för ditt ärende som används, kan du ignorera resten / eller inte ska vara vägas mot.

Fördelningen för enheter med två IP-adresser tilldelade bör se ut:

- Ethernet ”A” bör ha en IP-adress som ligger utanför intervallet för Serverpoolen för IP-adress som du skickade till Microsoft. Den här IP-adressen användas för att underhålla i/etc/hosts av Operativsystemet.

- Ethernet ”C” bör ha en IP-adress som används för kommunikation till NFS. Därför kan dessa adresser gör **inte** måste underhållas i etc/hosts för att tillåta trafik för instans instans i klienten.

En bladet konfiguration med två IP-adresser tilldelade är inte lämplig för distribution fall av HANA System Replication eller HANA skala ut. Om du har två IP-adresser som är tilldelade endast och vill distribuera en sådan konfiguration, kontakta SAP HANA på Azure Service Management för att hämta en tredje IP-adress i en tredje tilldelat VLAN. För stora HANA-instansen enheter med tre IP-adresserna som tilldelats på tre NIC-portar, gäller följande användningsregler för:

- Ethernet ”A” bör ha en IP-adress som ligger utanför intervallet för Serverpoolen för IP-adress som du skickade till Microsoft. Den här IP-adressen skall därför inte användas för att upprätthålla i/etc/hosts av Operativsystemet.

- Ethernet ”B” ska användas uteslutande behålls i etc/hosts för kommunikation mellan olika instanser. Dessa adresser skulle också vara IP-adresser som måste fortsätta att fungera i skalbar HANA konfigurationer som IP-adresser HANA används för kommunikation mellan noder-konfigurationen.

- Ethernet ”C” bör ha en IP-adress som används för kommunikation till NFS-lagring. Den här typen av adresser bör därför inte bevaras i etc/hosts.

- Ethernet ”D” bör användas exklusivt för åtkomst STONITH enhetens pacemaker. Det här gränssnittet är obligatorisk när du konfigurerar HANA System Replication (HSR) och vill uppnå automatisk växling vid fel i operativsystemet med hjälp av en uppstår baserat-enhet.


### <a name="storage"></a>Storage
Lagring är förkonfigurerade baserat på topologin som begärdes. Volymstorlekar och monteringspunkt varierar beroende på hur många servrar, SKU: er och topologi som har konfigurerats. Granska dina krävs scenarier (senare i det här dokumentet) om du vill ha mer information. Om det krävs mer lagring måste köpa du den i ett TB steg.

>[!NOTE]
>Denmonteringspunkt/usr/sap/<SID> är en symbolisk länk till monteringspunkten/hana/delas.


## <a name="supported-scenarios"></a>Scenarier som stöds

Arkitekturdiagram används följande beteckningar för bilderna:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

I följande lista visas scenarierna som stöds:

1. Enskild nod med en SID
2. Nod MCOS
3. Enskild nod med DR (Normal)
4. Enskild nod med DR (Multipurpose)
5. HSR med STONITH
6. HSR med DR (Normal / Multipurpose) 
7. Värden automatisk redundans (1 + 1) 
8. Skala ut med vänteläge
9. Skala ut utan vänteläge
10. Skala ut med DR



## <a name="1-single-node-with-one-sid"></a>1. Enskild nod med en SID

Den här topologin har stöd för en nod i en skala upp med en SID.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Single-nod-med-en-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men inte används |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Konfigurerats men inte används |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | Konfigurerats men inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter är förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|/Hana/Shared/SID | HANA-installation | 
|/hana/data/SID/mnt00001 | Installera filer | 
|/hana/log/SID/mnt00001 | Installera loggfiler | 
|/Hana/logbackups/SID | Gör om loggar |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. Nod MCOS

Den här topologin har stöd för en nod i en skala in konfiguration med flera SID.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men inte används |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Konfigurerats men inte används |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | Konfigurerats men inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter är förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|/Hana/Shared/SID1 | HANA-installation för SID1 | 
|/hana/data/SID1/mnt00001 | Datafiler installerar för SID1 | 
|/hana/log/SID1/mnt00001 | Loggfiler installerar för SID1 | 
|/Hana/logbackups/SID1 | Gör om loggar för SID1 |
|/Hana/Shared/SID2 | HANA-installation för SID2 | 
|/hana/data/SID2/mnt00001 | Datafiler installerar för SID2 | 
|/hana/log/SID2/mnt00001 | Loggfiler installerar för SID2 | 
|/Hana/logbackups/SID2 | Gör om loggar för SID2 |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- Volymens storlek distribution bygger på databasens storlek i minnet. Finns det [översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) att ta reda på vilken databas storlekar i minnet stöds med multisid miljö.

## <a name="3-single-node-with-dr-normal"></a>3. Enskild nod med DR (Normal)
 
Den här topologin har stöd för en nod i en skala in konfiguration med en eller flera SID med storage-baserade replikeringen till DR-plats för en primär SID. Endast enskild SID illustreras på den primära platsen i diagrammet, men multisid (MCOS) stöds också.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enskild nod med dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men inte används |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Konfigurerats men inte används |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | Konfigurerats men inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter är förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|/Hana/Shared/SID | HANA-installation av SID | 
|/hana/data/SID/mnt00001 | Datafiler installerar för SID | 
|/hana/log/SID/mnt00001 | Loggfiler installerar för SID | 
|/Hana/logbackups/SID | Gör om loggar för SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Volymens storlek distribution bygger på databasens storlek i minnet. Finns det [översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) att ta reda på vilken databas storlekar i minnet stöds med multisid miljö.
- Vid Katastrofåterställning: Volymer och monteringspunkter konfigureras (markerade som ”krävs för installation av HANA”) för produktion HANA-instansen installationen på DR HLI-enhet. 
- Vid Katastrofåterställning: De data, logbackups och klusterdelade volymer (markerade som ”Lagringsreplikering”) replikeras via ögonblicksbild från produktionsplatsen. Volymerna är monterade under failover-tid. Mer information finns i dokumentet [redundans för katastrofåterställning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) för mer information.
- Startvolym för **SKU-typ som jag klassen** replikeras till DR-nod.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Enskild nod med DR (Multipurpose)
 
Den här topologin har stöd för en nod i en skala in konfiguration med en eller flera SID med storage-baserade replikeringen till DR-plats för en primär SID. Endast enskild SID illustreras på den primära platsen i diagrammet, men multisid (MCOS) stöds också. På DR-plats används HLI enhet för QA instans när produktion körs från den primära platsen. QA-instans på DR-plats är vid tidpunkten för DR-redundans (eller redundanstest) tas offline.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Single-nod-med-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men inte används |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Konfigurerats men inte används |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | Konfigurerats men inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter är förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/Hana/Shared/SID | HANA installera för produktion SID | 
|/hana/data/SID/mnt00001 | Datafiler installera för produktion SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktion SID | 
|/Hana/logbackups/SID | Gör om loggar för produktion SID |
|**På DR-plats**|
|/Hana/Shared/SID | HANA installera för produktion SID | 
|/hana/data/SID/mnt00001 | Datafiler installera för produktion SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktion SID | 
|/hana/shared/QA-SID | HANA-installation för QA-SID | 
|/hana/data/QA-SID/mnt00001 | Datafiler installerar för QA SID | 
|/hana/log/QA-SID/mnt00001 | Loggfiler installerar för QA SID |
|/hana/logbackups/QA-SID | Gör om loggar för QA-SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Volymens storlek distribution bygger på databasens storlek i minnet. Finns det [översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) att ta reda på vilken databas storlekar i minnet stöds med multisid miljö.
- Vid Katastrofåterställning: Volymer och monteringspunkter konfigureras (markerade som ”krävs för installation av HANA”) för produktion HANA-instansen installationen på DR HLI-enhet. 
- Vid Katastrofåterställning: De data, logbackups och klusterdelade volymer (markerade som ”Lagringsreplikering”) replikeras via ögonblicksbild från produktionsplatsen. Volymerna är monterade under failover-tid. Mer information finns i dokumentet [redundans för katastrofåterställning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) för mer information. 
- Vid Katastrofåterställning: Data, logbackups, log, klusterdelade volymer för QA (markerade som ”QA instansinstallation”) är konfigurerade för installationen av QA-instans.
- Startvolym för **SKU-typ som jag klassen** replikeras till DR-nod.

## <a name="5-hsr-with-stonith"></a>5. HSR med STONITH
 
Den här topologin stöder två noder för konfigurationen av HANA System Replication (HSR). Den här konfigurationen stöds bara för en enda HANA-instanser på en nod. Innebär att, MCOS scenarier stöds inte.

**Från och med nu stöds den här arkitekturen endast för SUSE-operativsystem.**


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![HSR-with-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Används för STONITH |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Konfigurerats men inte används |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | Används för STONITH |

### <a name="storage"></a>Storage
Följande monteringspunkter är förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/Hana/Shared/SID | HANA installera för produktion SID | 
|/hana/data/SID/mnt00001 | Datafiler installera för produktion SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktion SID | 
|/Hana/logbackups/SID | Gör om loggar för produktion SID |
|**På den sekundära noden**|
|/Hana/Shared/SID | HANA installerar för sekundära SID | 
|/hana/data/SID/mnt00001 | Datafiler installerar för sekundära SID | 
|/hana/log/SID/mnt00001 | Loggfiler installerar för sekundära SID | 
|/Hana/logbackups/SID | Gör om loggar för sekundära SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Volymens storlek distribution bygger på databasens storlek i minnet. Finns det [översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) att ta reda på vilken databas storlekar i minnet stöds med multisid miljö.
- STONITH: En uppstår har konfigurerats för STONITH-installationen. Men är en användning av STONITH valfritt.


## <a name="6-hsr-with-dr"></a>6. HSR med DR
 
Den här topologin stöder två noder för konfigurationen av HANA System Replication (HSR). Både normal och multipurpose DR stöds. De här konfigurationerna har endast stöd för enda HANA-instanser på en nod. Innebär att MCOS scenarier inte stöds med de här konfigurationerna.

I diagrammet, multipurpose scenariot illustreras då vid DR-plats, HLI enhet används för QA instans när produktion körs från den primära platsen. QA-instans på DR-plats är vid tidpunkten för DR-redundans (eller redundanstest) tas offline. 



### <a name="architecture-diagram"></a>Arkitekturdiagram  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Används för STONITH |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Konfigurerats men inte används |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | Används för STONITH |

### <a name="storage"></a>Storage
Följande monteringspunkter är förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På den primära noden på den primära platsen**|
|/Hana/Shared/SID | HANA installera för produktion SID | 
|/hana/data/SID/mnt00001 | Datafiler installera för produktion SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktion SID | 
|/Hana/logbackups/SID | Gör om loggar för produktion SID |
|**På den sekundära noden på den primära platsen**|
|/Hana/Shared/SID | HANA installerar för sekundära SID | 
|/hana/data/SID/mnt00001 | Datafiler installerar för sekundära SID | 
|/hana/log/SID/mnt00001 | Loggfiler installerar för sekundära SID | 
|/Hana/logbackups/SID | Gör om loggar för sekundära SID |
|**På DR-plats**|
|/Hana/Shared/SID | HANA installera för produktion SID | 
|/hana/data/SID/mnt00001 | Datafiler installera för produktion SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktion SID | 
|/hana/shared/QA-SID | HANA-installation för QA-SID | 
|/hana/data/QA-SID/mnt00001 | Datafiler installerar för QA SID | 
|/hana/log/QA-SID/mnt00001 | Loggfiler installerar för QA SID |
|/hana/logbackups/QA-SID | Gör om loggar för QA-SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Volymens storlek distribution bygger på databasens storlek i minnet. Finns det [översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) att ta reda på vilken databas storlekar i minnet stöds med multisid miljö.
- STONITH: En uppstår har konfigurerats för STONITH-installationen. Men är en användning av STONITH valfritt.
- Vid Katastrofåterställning: **Två uppsättningar lagringsvolymer krävs** för primära och sekundära noden replikering.
- Vid Katastrofåterställning: Volymer och monteringspunkter konfigureras (markerade som ”krävs för installation av HANA”) för produktion HANA-instansen installationen på DR HLI-enhet. 
- Vid Katastrofåterställning: De data, logbackups och klusterdelade volymer (markerade som ”Lagringsreplikering”) replikeras via ögonblicksbild från produktionsplatsen. Volymerna är monterade under failover-tid. Mer information finns i dokumentet [redundans för katastrofåterställning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) för mer information. 
- Vid Katastrofåterställning: Data, logbackups, log, klusterdelade volymer för QA (markerade som ”QA instansinstallation”) är konfigurerade för installationen av QA-instans.
- Startvolym för **SKU-typ som jag klassen** replikeras till DR-nod.


## <a name="7-host-auto-failover-11"></a>7. Värden automatisk redundans (1 + 1)
 
Den här topologin stöder två noder i en redundanskonfiguration för värden automatiskt. Det finns en nod med master-/ arbetarroll och andra som en vänteläge. **SAP i det här scenariot har endast stöd för s/4 HANA.** Se OSS Obs ”[2408419 - SAP S/4HANA - stöd för flera noder](https://launchpad.support.sap.com/#/notes/2408419)” för mer information.



### <a name="architecture-diagram"></a>Arkitekturdiagram  

![sca](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod till nod-kommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men inte används |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Nod till nod-kommunikation |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | Konfigurerats men inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter är förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På noderna master och vänteläge**|
|/ hana/delade | HANA installera för produktion SID | 
|/hana/data/SID/mnt00001 | Datafiler installera för produktion SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktion SID | 
|/Hana/logbackups/SID | Gör om loggar för produktion SID |



### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- I vänteläge: Volymer och monteringspunkter konfigureras (markerade som ”krävs för installation av HANA”) för installationen av HANA-instansen på den vänteläge enheten.
 

## <a name="8-scale-out-with-standby"></a>8. Skala ut med vänteläge
 
Den här topologin har stöd för flera noder i en skalbar konfiguration. Det finns en nod med rollen som infrastrukturhanterare, en eller flera noder med worker-roll och en eller flera noder som vänteläge. Men kan det finnas bara en huvudnod vid en given tidpunkt tid.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod till nod-kommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men inte används |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Nod till nod-kommunikation |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | Konfigurerats men inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter är förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På noderna master, arbetsroller och vänteläge**|
|/ hana/delade | HANA installera för produktion SID | 
|/hana/data/SID/mnt00001 | Datafiler installera för produktion SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktion SID | 
|/Hana/logbackups/SID | Gör om loggar för produktion SID |


## <a name="9-scale-out-without-standby"></a>9. Skala ut utan vänteläge
 
Den här topologin har stöd för flera noder i en skalbar konfiguration. Det finns en nod med rollen som infrastrukturhanterare och en eller läge noder med worker-roll. Men kan det finnas bara en huvudnod vid en given tidpunkt tid.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod till nod-kommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men inte används |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Nod till nod-kommunikation |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | Konfigurerats men inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter är förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På huvud- och worker-noder**|
|/ hana/delade | HANA installera för produktion SID | 
|/hana/data/SID/mnt00001 | Datafiler installera för produktion SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktion SID | 
|/Hana/logbackups/SID | Gör om loggar för produktion SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Skala ut med DR
 
Den här topologin har stöd för flera noder i en skalbar med en DR. Med både normal och multipurpose DR stöds. I diagrammet visas endast enskilt syfte DR. Du kan begära den här topologin med eller utan noden vänteläge.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE-OS | Namn med RHEL-OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient till HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod till nod-kommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Noden lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men inte används |
| A | TYP II | vlan<tenantNo> | team0.tenant | Klient till HLI |
| B | TYP II | vlan<tenantNo+2> | team0.tenant+2 | Nod till nod-kommunikation |
| C | TYP II | vlan<tenantNo+1> | team0.tenant + 1 | Noden lagring |
| D | TYP II | vlan<tenantNo+3> | team0.tenant+3 | Konfigurerats men inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter är förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/ hana/delade | HANA installera för produktion SID | 
|/hana/data/SID/mnt00001 | Datafiler installera för produktion SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktion SID | 
|/Hana/logbackups/SID | Gör om loggar för produktion SID |
|**På noden DR**|
|/ hana/delade | HANA installera för produktion SID | 
|/hana/data/SID/mnt00001 | Datafiler installera för produktion SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktion SID | 


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
-  Vid Katastrofåterställning: Volymer och monteringspunkter konfigureras (markerade som ”krävs för installation av HANA”) för produktion HANA-instansen installationen på DR HLI-enhet. 
- Vid Katastrofåterställning: De data, logbackups och klusterdelade volymer (markerade som ”Lagringsreplikering”) replikeras via ögonblicksbild från produktionsplatsen. Volymerna är monterade under failover-tid. Mer information finns i dokumentet [redundans för katastrofåterställning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) för mer information. 
- Startvolym för **SKU-typ som jag klassen** replikeras till DR-nod.


## <a name="next-steps"></a>Nästa steg
- Se [infrastruktur och anslutningsmöjlighet](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) för HLI
- Se [hög tillgänglighet och katastrofåterställning recovery](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) för HLI
