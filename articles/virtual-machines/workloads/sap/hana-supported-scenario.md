---
title: Stöd för scenarier SAP HANA i Azure (stora instanser) | Microsoft Docs
description: Scenarier som stöds och information om arkitekturen för SAP HANA i Azure (stora instanser)
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
ms.date: 06/25/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b703f6d141005cf3cf29531a0586eebb61693a2
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754575"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Scenarier som stöds för HANA stora instanser
Det här dokumentet beskriver scenarierna som stöds med information arkitektur för HANA stora instanser (HLI).

>[!NOTE]
>Om ditt nödvändiga scenario inte beskrivs här, kontaktar du Microsoft Service Management-teamet om du vill utvärdera dina krav.
Innan du fortsätter med HLI enheten etablering, verifiera design med SAP eller partnern service implementering.

## <a name="terms-and-definitions"></a>Termer och definitioner
Vi förstår de termer och definitioner som används i dokumentet.

- SID: System identifierare för HANA system.
- HLI: Hana stora instanser.
- DR: En disaster recovery-plats.
- Normal DR: en systeminställningarna med en dedikerad resurs för DR ändamål som endast används.
- Multipurpose DR: Ett system för DR-plats som konfigurerats för att använda icke-produktionsmiljö tillsammans med produktions-instans som konfigurerats för att använda DR-händelse. 
- Enskild SID: Ett system med en instans installerad.
- Flera SID: Ett system med flera instanser som har konfigurerats. Kallas även en MCOS miljö.


## <a name="overview"></a>Översikt
HANA stora instanser stöd för olika arkitekturer för att uppnå dina affärsbehov. I följande lista beskrivs scenarier och deras konfigurationsinformation. 

Den härledda arkitekturdesign är rent ur infrastruktur och du måste se SAP eller din implementering partners för HANA-distributionen. Kontakta Microsoft-kontoteamet för att granska arkitekturen och härledd en lösning för dig om dina scenarier som saknas.

**Dessa är helt kompatibel med TDI (anpassad dataintegrering) design- och stöds av SAP.**

Det här dokumentet innehåller information om de två komponenterna i varje arkitektur som stöds:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Varje server som etablerats levereras förinställda med uppsättningar med ethernet. Här följer information om ethernet som konfigurerats på varje HLI-enheter.

- **En**: används för/genom att klientåtkomst.
- **B**: används för nod till nod-kommunikation. Detta är konfigurerad på alla servrar (oavsett topologin begärt) men används endast för skalbar scenarier.
- **C**: det här gränssnittet används för noden till lagringsanslutning.
- **D**: det här gränssnittet används för noden som iSCSI-enhetsanslutning för STONITH installationen. Det här gränssnittet konfigureras bara när inställningen HSR begärs.  

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod till nod |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | STONITH |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nod till nod |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | STONITH |

Du kan använda baserade topologi som konfigurerats på enheten som HLI gränssnitt. Till exempel ställs gränssnittet ”B” in för nod till nod-kommunikation, vilket är användbart när du har en skalbar topologi som konfigurerats. När det gäller nod skala upp konfiguration används inte det här gränssnittet. Granska dina krävs scenarier (senare i det här dokumentet) om du vill ha mer information om hur gränssnittet. 

Om det behövs kan du definiera ytterligare NIC-kort på egen hand. Konfigurationen på de befintliga nätverkskort kan inte ändras.

>[!NOTE]
>Du kan fortfarande vara ytterligare gränssnitt som är fysiska gränssnitt eller partnerskap. Du bör överväga att ovannämnda gränssnitten för ditt ärende som används, kan du ignorera rest / eller att inte modifieras med.


### <a name="storage"></a>Storage
Lagring förkonfigureras baserat på topologi som begärdes. Volymstorlekar och monteringspunkt variera beroende på hur många servrar, SKU: er och topologi som har konfigurerats. Granska dina krävs scenarier (senare i det här dokumentet) om du vill ha mer information. Om det behövs mer lagringsutrymme kan du köpa en TB steg.

>[!NOTE]
>Denmonteringspunkt/usr/sap/<SID> är en symbolisk länk till monteringspunkt/hana/delas.


## <a name="supported-scenarios"></a>Scenarier som stöds

Arkitektur-diagram används följande kommentarer för bilder:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

I följande lista visas scenarierna som stöds:

1. Enskild nod med en SID
2. Enskild nod MCOS
3. Enskild nod med DR (Normal)
4. Enskild nod med DR (Multipurpose)
5. HSR med STONITH
6. HSR med DR (Normal / Multipurpose) 
7. Värden automatisk redundans (1 + 1) 
8. Skala ut med vänteläge
9. Skala ut utan vänteläge
10. Skala ut med Katastrofåterställning



## <a name="1-single-node-with-one-sid"></a>1. Enskild nod med en SID

Den här topologin stöder en nod i en skala in konfiguration med en SID.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Single-nod-med-1-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitten förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men som inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men som inte används |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurerats men som inte används |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurerats men som inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|/Hana/Shared/SID | HANA installation | 
|/Hana/data/SID/mnt00001 | Installera filer | 
|/Hana/log/SID/mnt00001 | Installera loggfiler | 
|/Hana/logbackups/SID | Gör om loggar |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. Enskild nod MCOS

Den här topologin stöder en nod i en skala in konfiguration med flera SID.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Single-nod-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitten förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men som inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men som inte används |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurerats men som inte används |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurerats men som inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|/Hana/Shared/SID1 | HANA installation för SID1 | 
|/Hana/data/SID1/mnt00001 | Datafiler installerar för SID1 | 
|/Hana/log/SID1/mnt00001 | Loggfiler installerar för SID1 | 
|/Hana/logbackups/SID1 | Gör om loggarna för SID1 |
|/Hana/Shared/SID2 | HANA installation för SID2 | 
|/Hana/data/SID2/mnt00001 | Datafiler installerar för SID2 | 
|/Hana/log/SID2/mnt00001 | Loggfiler installerar för SID2 | 
|/Hana/logbackups/SID2 | Gör om loggar för SID2 |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- Volymen storlek distribution baserat på databasens storlek i minnet. Finns det [översikt och arkitektur](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) avsnittet om du vill veta vilken databas storlekar i minnet stöds med multisid miljö.

## <a name="3-single-node-with-dr-normal"></a>3. Enskild nod med DR (Normal)
 
Den här topologin stöder en nod i en skala in konfiguration med en eller flera SID med storage-baserade-replikering för en primär SID DR-platsen. Endast enskild SID visas på den primära platsen i diagrammet, men multisid (MCOS) stöds också.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Enskild nod med dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitten förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men som inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men som inte används |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurerats men som inte används |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurerats men som inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|/Hana/Shared/SID | HANA installation av SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för SID | 
|/Hana/logbackups/SID | Gör om loggarna för SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Baseras volym storlek distribution av databasens storlek i minnet. Finns det [översikt och arkitektur](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) avsnittet om du vill veta vilken databas storlekar i minnet stöds med multisid miljö.
- Vid DR: volymer och monteringspunkter konfigureras (markerade som ”som krävs för installation av HANA”) för produktion HANA instans installation vid Katastrofåterställning HLI-enhet. 
- Vid DR: data, logbackups och klusterdelade volymer (markerade som ”Lagringsreplikering”) replikeras via ögonblicksbild från produktionsplatsen. Volymerna är monterade under failover-tid. Se [redundans för katastrofåterställning](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) för mer information.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Enskild nod med DR (Multipurpose)
 
Den här topologin stöder en nod i en skala in konfiguration med en eller flera SID med storage-baserade-replikering för en primär SID DR-platsen. Endast enskild SID visas på den primära platsen i diagrammet, men multisid (MCOS) stöds också. På DR-plats används HLI enhet för QA instansen när produktion körs från den primära platsen. QA-instans på DR-plats är vid tidpunkten för DR-redundanskluster (eller redundanstest) tas offline.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Single-nod-med-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitten förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men som inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men som inte används |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurerats men som inte används |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurerats men som inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/Hana/Shared/SID | HANA installerar för produktion SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för produktion SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för produktion SID | 
|/Hana/logbackups/SID | Gör om loggfiler för produktion SID |
|**På DR-plats**|
|/Hana/Shared/SID | HANA installerar för produktion SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för produktion SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för produktion SID | 
|/Hana/Shared/QA-SID | HANA installation för QA SID | 
|/Hana/data/QA-SID/mnt00001 | Datafiler installerar för QA SID | 
|/Hana/log/QA-SID/mnt00001 | Loggfiler installerar för QA SID |
|/Hana/logbackups/QA-SID | Gör om loggarna för QA SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Baseras volym storlek distribution av databasens storlek i minnet. Finns det [översikt och arkitektur](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) avsnittet om du vill veta vilken databas storlekar i minnet stöds med multisid miljö.
- Vid DR: volymer och monteringspunkter konfigureras (markerade som ”som krävs för installation av HANA”) för produktion HANA instans installation vid Katastrofåterställning HLI-enhet. 
- Vid DR: data, logbackups och klusterdelade volymer (markerade som ”Lagringsreplikering”) replikeras via ögonblicksbild från produktionsplatsen. Volymerna är monterade under failover-tid. Se [redundans för katastrofåterställning](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) för mer information. 
- Vid DR: data, logbackups, log, klusterdelade volymer för QA (markerade som ”QA instans-installation”) har konfigurerats för QA instans installationen.

## <a name="5-hsr-with-stonith"></a>5. HSR med STONITH
 
Den här topologin stöder två noder för konfigurationen av HANA System replikering (HSR). 

**Den här arkitekturen stöds bara för SUSE operativsystemet från och med nu.**


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![HSR med STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitten förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men som inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Används för STONITH |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurerats men som inte används |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | Används för STONITH |

### <a name="storage"></a>Storage
Följande monteringspunkter förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/Hana/Shared/SID | HANA installerar för produktion SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för produktion SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för produktion SID | 
|/Hana/logbackups/SID | Gör om loggfiler för produktion SID |
|**På den sekundära noden**|
|/Hana/Shared/SID | HANA installerar för sekundära SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för sekundära SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för sekundära SID | 
|/Hana/logbackups/SID | Gör om loggarna för sekundära SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Baseras volym storlek distribution av databasens storlek i minnet. Finns det [översikt och arkitektur](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) avsnittet om du vill veta vilken databas storlekar i minnet stöds med multisid miljö.
- STONITH: En uppstår har konfigurerats för STONITH-installationen. Men är en användning av STONITH valfritt.


## <a name="6-hsr-with-dr"></a>6. HSR med Katastrofåterställning
 
Den här topologin stöder två noder för konfigurationen av HANA System replikering (HSR). Både normala och multipurpose DR stöds. 

I diagrammet multipurpose scenariot illustreras då vid DR-plats, HLI enhet används för QA instansen när produktion körs från den primära platsen. QA-instans på DR-plats är vid tidpunkten för DR-redundanskluster (eller redundanstest) tas offline. 



### <a name="architecture-diagram"></a>Arkitekturdiagram  

![HSR med DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitten förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Konfigurerats men som inte används |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Används för STONITH |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurerats men som inte används |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | Används för STONITH |

### <a name="storage"></a>Storage
Följande monteringspunkter förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På den primära noden på den primära platsen**|
|/Hana/Shared/SID | HANA installerar för produktion SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för produktion SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för produktion SID | 
|/Hana/logbackups/SID | Gör om loggfiler för produktion SID |
|**På den sekundära noden på den primära platsen**|
|/Hana/Shared/SID | HANA installerar för sekundära SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för sekundära SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för sekundära SID | 
|/Hana/logbackups/SID | Gör om loggarna för sekundära SID |
|**På DR-plats**|
|/Hana/Shared/SID | HANA installerar för produktion SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för produktion SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för produktion SID | 
|/Hana/Shared/QA-SID | HANA installation för QA SID | 
|/Hana/data/QA-SID/mnt00001 | Datafiler installerar för QA SID | 
|/Hana/log/QA-SID/mnt00001 | Loggfiler installerar för QA SID |
|/Hana/logbackups/QA-SID | Gör om loggarna för QA SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- För MCOS: Baseras volym storlek distribution av databasens storlek i minnet. Finns det [översikt och arkitektur](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) avsnittet om du vill veta vilken databas storlekar i minnet stöds med multisid miljö.
- STONITH: En uppstår har konfigurerats för STONITH-installationen. Men är en användning av STONITH valfritt.
- Vid DR: **krävs två uppsättningar med lagringsvolymer** för primära och sekundära noden replikering.
- Vid DR: volymer och monteringspunkter konfigureras (markerade som ”som krävs för installation av HANA”) för produktion HANA instans installation vid Katastrofåterställning HLI-enhet. 
- Vid DR: data, logbackups och klusterdelade volymer (markerade som ”Lagringsreplikering”) replikeras via ögonblicksbild från produktionsplatsen. Volymerna är monterade under failover-tid. Se [redundans för katastrofåterställning](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) för mer information. 
- Vid DR: data, logbackups, log, klusterdelade volymer för QA (markerade som ”QA instans-installation”) har konfigurerats för QA instans installationen.


## <a name="7-host-auto-failover-11"></a>7. Värden automatisk redundans (1 + 1)
 
Den här topologin stöder två noder i en redundanskonfiguration för värden automatiskt. Det är en nod med master/worker-rollen och andra som en vänteläge. **SAP stöder det här scenariot endast för S/4 HANA.** Se Observera OSS ”[2408419 - SAP S/4HANA - stöd för flera noder](https://launchpad.support.sap.com/#/notes/2408419)” för mer information.



### <a name="architecture-diagram"></a>Arkitekturdiagram  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitten förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod för nodkommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men som inte används |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nod för nodkommunikation |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurerats men som inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På huvudservern och vänteläge noder**|
|/ hana/delade | HANA installerar för produktion SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för produktion SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för produktion SID | 
|/Hana/logbackups/SID | Gör om loggfiler för produktion SID |



### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
- I vänteläge: volymer och monteringspunkter konfigureras (markerade som ”som krävs för installation av HANA”) för HANA instans-installation på den väntande enheten.
 

## <a name="8-scale-out-with-standby"></a>8. Skala ut med vänteläge
 
Den här topologin stöder flera noder i en skalbar konfiguration. Det är en nod med rollen, en eller flera noder med worker-rollen och en eller flera noder i vänteläge. Men kan det finnas bara en huvudnod vid en viss tid.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitten förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod för nodkommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men som inte används |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nod för nodkommunikation |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurerats men som inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På noderna master, arbetare och vänteläge**|
|/ hana/delade | HANA installerar för produktion SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för produktion SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för produktion SID | 
|/Hana/logbackups/SID | Gör om loggfiler för produktion SID |


## <a name="9-scale-out-without-standby"></a>9. Skala ut utan vänteläge
 
Den här topologin stöder flera noder i en skalbar konfiguration. Det finns en nod med rollen och en eller läge noder med worker-rollen. Men kan det finnas bara en huvudnod vid en viss tid.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![scaleout nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitten förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod för nodkommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men som inte används |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nod för nodkommunikation |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurerats men som inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På master- och arbetsroller noder**|
|/ hana/delade | HANA installerar för produktion SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för produktion SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för produktion SID | 
|/Hana/logbackups/SID | Gör om loggfiler för produktion SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Skala ut med Katastrofåterställning
 
Den här topologin stöder flera noder i en skalbar med en Katastrofåterställning. Både normala och multipurpose DR stöds. Endast ett syfte DR visas i diagrammet. Du kan begära den här topologin med eller utan noden vänteläge.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![scaleout med dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Följande nätverksgränssnitten förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klienten HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nod för nodkommunikation |
| C | TYP I | eth1.tenant | eno2.tenant | Noden till lagring |
| D | TYP I | eth4.tenant | eno4.tenant | Konfigurerats men som inte används |
| A | TYP II | VLAN<tenantNo> | team0.tenant | Klienten HLI |
| B | TYP II | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nod för nodkommunikation |
| C | TYP II | VLAN < tenantNo + 1 > | team0.tenant + 1 | Noden till lagring |
| D | TYP II | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurerats men som inte används |

### <a name="storage"></a>Storage
Följande monteringspunkter förkonfigurerade:

| Monteringspunkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/ hana/delade | HANA installerar för produktion SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för produktion SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för produktion SID | 
|/Hana/logbackups/SID | Gör om loggfiler för produktion SID |
|**På noden Katastrofåterställning**|
|/ hana/delade | HANA installerar för produktion SID | 
|/Hana/data/SID/mnt00001 | Datafiler installerar för produktion SID | 
|/Hana/log/SID/mnt00001 | Loggfiler installerar för produktion SID | 


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/SAP/SID är en symbolisk länk till /hana/shared/SID.
-  Vid DR: volymer och monteringspunkter konfigureras (markerade som ”som krävs för installation av HANA”) för produktion HANA instans installation vid Katastrofåterställning HLI-enhet. 
- Vid DR: data, logbackups och klusterdelade volymer (markerade som ”Lagringsreplikering”) replikeras via ögonblicksbild från produktionsplatsen. Volymerna är monterade under failover-tid. Se [redundans för katastrofåterställning](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) för mer information. 


## <a name="next-steps"></a>Nästa steg
- Se [infrastruktur och anslutningen](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) för HLI
- Se [hög tillgänglighet och disaster recovery](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) för HLI