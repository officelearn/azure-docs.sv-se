---
title: Scenarier som stöds SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Scenarier som stöds och deras arkitektur information för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f17e447f26ae4f7573941fc0c578a918ff45a145
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101220"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Scenarier som stöds för HANA-stora instanser
I det här dokumentet beskrivs de scenarier som stöds tillsammans med deras arkitektur information för HLI (HANA Large instances).

>[!NOTE]
>Om det scenario som krävs inte anges här kontaktar du Microsofts tjänst hanterings team för att utvärdera dina krav.
Innan du fortsätter med HLI enhets etablering, verifierar du designen med SAP eller din tjänst implementerings partner.

## <a name="terms-and-definitions"></a>Villkor och definitioner
Nu ska vi förstå de termer och definitioner som används i dokumentet.

- SID System identifierare för HANA-system.
- HLI: Hana-stora instanser.
- AR En katastrof återställnings plats.
- Normal DR: En systeminstallation med en dedikerad resurs för DR-syften används endast.
- Multianvändnings DR: Ett system på en DR-plats som kon figurer ATS för att använda en icke-produktions miljö tillsammans med den produktions instans som kon figurer ATS att använda till DR 
- Enskild SID:  Ett system med en instans installerad.
- Flera SID: Ett system med flera konfigurerade instanser. Kallas även för en MCOS-miljö.


## <a name="overview"></a>Översikt
De stora och HANA-instanserna har stöd för olika arkitekturer för att uppnå dina affärs behov. I följande lista beskrivs scenarierna och deras konfigurations information. 

Den härledda arkitektur designen är helt ur infrastruktur perspektivet och du måste kontakta SAP eller implementerings partner för HANA-distributionen. Om dina scenarier inte finns med i listan kan du kontakta Microsoft-konto-teamet för att granska arkitekturen och härleda en lösning.

**Dessa arkitekturer är helt kompatibla med den TDI-design (anpassad data integrering) som stöds av SAP.**

Det här dokumentet beskriver information om de två komponenterna i varje arkitektur som stöds:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Varje server som tillhandahålls är förkonfigurerad med uppsättningarna Ethernet-gränssnitt. Här följer information om de Ethernet-gränssnitt som kon figurer ATS på varje HLI-enhet.

- **S**: Det här gränssnittet används för/av-klient åtkomsten.
- **B**: Det här gränssnittet används för nod-till-nod-kommunikation. Det här gränssnittet är konfigurerat på alla servrar (oavsett vilken topologi som begärs), men används bara för 
- skalnings scenarier.
- **C**: Det här gränssnittet används för noden till lagrings anslutningen.
- **D**: Det här gränssnittet används för noden till ISCSI-hälsoenhets anslutning för STONITH-installation. Det här gränssnittet är bara konfigurerat när HSR-installationen begärs.  

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod till nod |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | STONITH |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Nod till nod |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | STONITH |

Du använder gränssnitten baserat på den topologi som kon figurer ATS på HLI-enheten. Gränssnittet "B" är till exempel konfigurerat för att nod-till-nod-kommunikation, vilket är användbart när en skalbar topologi har kon figurer ATS. Det här gränssnittet används inte för en skalbar konfiguration av en nod. Granska dina nödvändiga scenarier (senare i det här dokumentet) om du vill ha mer information om gränssnitts användningen. 

Om det behövs kan du definiera ytterligare NIC-kort på egen hand. Konfigurationen på befintliga nätverkskort kan dock inte ändras.

>[!NOTE]
>Du kan fortfarande hitta ytterligare gränssnitt som är fysiska gränssnitt eller kopplingar. Du bör tänka på ovanstående gränssnitt för ditt använda fall, rest kan ignoreras/eller inte vägas med.

Fördelningen av enheter med två tilldelade IP-adresser bör se ut så här:

- Ethernet "A" ska ha en tilldelad IP-adress som ligger utanför adress intervallet för serverns IP-adresspool som du skickade till Microsoft. Den här IP-adressen ska användas för att underhålla i/etc/hosts av operativ systemet.

- Ethernet "C" ska ha en tilldelad IP-adress som används för kommunikation till NFS. De här adresserna behöver därför **inte** behållas i etc/-värdar för att tillåta instans till instans trafik i klienten.

För distributions fall av HANA-systemreplikering eller HANA-utskalning är en blad konfiguration med två tilldelade IP-adresser inte lämplig. Om du har två IP-adresser som endast tilldelats och vill distribuera en sådan konfiguration kontaktar du SAP HANA på Azure Service Management för att få en tredje IP-adress i ett tredje VLAN tilldelat. Följande användnings regler gäller för HANA-stora instans enheter som har tre IP-adresser tilldelade till tre NIC-portar:

- Ethernet "A" ska ha en tilldelad IP-adress som ligger utanför adress intervallet för serverns IP-adresspool som du skickade till Microsoft. Den här IP-adressen får därför inte användas för att underhålla i/etc/hosts av operativ systemet.

- Ethernet "B" bör uteslutande användas för att upprätthållas i etc/-värdar för kommunikation mellan olika instanser. De här adresserna är också de IP-adresser som måste upprätthållas vid skalbara HANA-konfigurationer som IP-adresser HANA används för konfiguration mellan noder.

- Ethernet "C" ska ha en tilldelad IP-adress som används för kommunikation till NFS-lagring. Den här typen av adresser bör därför inte behållas i etc/hosts.

- Ethernet "D" ska endast användas för åtkomst STONITH-enheten för pacemaker. Det här gränssnittet krävs när du konfigurerar HANA-systemreplikering (HSR) och vill uppnå automatisk redundans vid operativ systemet med hjälp av en SBD-baserad enhet.


### <a name="storage"></a>Storage
Lagringen är förkonfigurerad baserat på den begärda topologin. Volym storlekarna och monterings punkt varierar beroende på hur många servrar, SKU: er och topologi som har kon figurer ATS. Granska dina nödvändiga scenarier (senare i det här dokumentet) om du vill ha mer information. Om det krävs mer lagrings utrymme kan du köpa det i en TB-ökning.

>[!NOTE]
>Monterings punkt/usr/SAP/\<-sid > är en symbolisk länk till/Hana/Shared-monterings punkt.


## <a name="supported-scenarios"></a>Scenarier som stöds

I arkitektur diagrammen används följande format för grafiken:

![Förklaringar. PNG](media/hana-supported-scenario/Legends.PNG)

I följande lista visas de scenarier som stöds:

1. En nod med en SID
2. MCOS för enskild nod
3. Enkel nod med DR (normal)
4. Enkel nod med DR (flera syften)
5. HSR med STONITH
6. HSR med DR (normal/multisyften) 
7. Automatisk redundans för värd (1 + 1) 
8. Skala ut med vänte läge
9. Skala ut utan vänte läge
10. Skala ut med DR



## <a name="1-single-node-with-one-sid"></a>1. En nod med en SID

Den här topologin har stöd för en nod i en skalnings konfiguration med en SID.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Single-node-with-one-SID. png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande mountpoints är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID | HANA-installation | 
|/hana/data/SID/mnt00001 | Installera datafiler | 
|/hana/log/SID/mnt00001 | Installera loggfiler | 
|/hana/logbackups/SID | Gör om loggar |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. MCOS för enskild nod

Den här topologin har stöd för en nod i en skalbar konfiguration med flera sid: er.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande mountpoints är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID1 | HANA-installation för SID1 | 
|/hana/data/SID1/mnt00001 | Installera filer för SID1 | 
|/hana/log/SID1/mnt00001 | Logga filer som installeras för SID1 | 
|/hana/logbackups/SID1 | Gör om loggar för SID1 |
|/hana/shared/SID2 | HANA-installation för SID2 | 
|/hana/data/SID2/mnt00001 | Installera filer för SID2 | 
|/hana/log/SID2/mnt00001 | Logga filer som installeras för SID2 | 
|/hana/logbackups/SID2 | Gör om loggar för SID2 |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- Distribution av volym storlek baseras på databasens storlek i minnet. Avsnittet [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) innehåller information om vilka databas storlekar i minnet som stöds med multisid-miljön.

## <a name="3-single-node-with-dr-normal"></a>3. Enkel nod med DR (normal)
 
Den här topologin har stöd för en nod i en skalbar konfiguration med en eller flera sid: er med den lagrings replikeringen till Dr-platsen för ett primärt sid. I diagrammet visas bara enkla SID på den primära platsen, men multisid (MCOS) stöds också.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Single-Node-with-Dr. png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande mountpoints är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|/hana/shared/SID | HANA-installation för SID | 
|/hana/data/SID/mnt00001 | Installera data filer för SID | 
|/hana/log/SID/mnt00001 | Logga filer installera för SID | 
|/hana/logbackups/SID | Gör om loggar för SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- För MCOS: Distribution av volym storlek baseras på databasens storlek i minnet. Avsnittet [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) innehåller information om vilka databas storlekar i minnet som stöds med multisid-miljön.
- Vid DR: Volymerna och mountpoints konfigureras (markerade som "krävs för HANA-installation") för produktion HANA-instansen-installation på DR HLI-enheten. 
- Vid DR: Data, logbackups och delade volymer (markerade som "lagrings replikering") replikeras via ögonblicks bilder från produktions platsen. Dessa volymer monteras endast under redundansväxlingen. Mer information finns i dokumentet om [redundans för haveri beredskap](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) .
- Start volym för **SKU-typ I klass** replikeras till en Dr-nod.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Enkel nod med DR (flera syften)
 
Den här topologin har stöd för en nod i en skalbar konfiguration med en eller flera sid: er med den lagrings replikeringen till Dr-platsen för ett primärt sid. I diagrammet visas bara enkla SID på den primära platsen, men multisid (MCOS) stöds också. På DR-platsen används HLI-enheten för instansen frågor och svar medan produktions åtgärderna körs från den primära platsen. Vid tidpunkten för DR-redundans (eller failover-test) tas frågor om frågor och svar på DR-platsen ned.

### <a name="architecture-diagram"></a>Arkitekturdiagram  

![Single-Node-with-Dr-Multipurpose. png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande mountpoints är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära platsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för produktions-SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På DR-webbplatsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för produktions-SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktions-SID | 
|/hana/shared/QA-SID | HANA-installation för frågor och svar-SID | 
|/hana/data/QA-SID/mnt00001 | Filer som installeras för frågor och svar-SID | 
|/hana/log/QA-SID/mnt00001 | Loggfiler installeras för frågor och svar-SID |
|/hana/logbackups/QA-SID | Gör om loggar för säkerhets frågor och svar |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- För MCOS: Distribution av volym storlek baseras på databasens storlek i minnet. Avsnittet [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) innehåller information om vilka databas storlekar i minnet som stöds med multisid-miljön.
- Vid DR: Volymerna och mountpoints konfigureras (markerade som "krävs för HANA-installation") för produktion HANA-instansen-installation på DR HLI-enheten. 
- Vid DR: Data, logbackups och delade volymer (markerade som "lagrings replikering") replikeras via ögonblicks bilder från produktions platsen. Dessa volymer monteras endast under redundansväxlingen. Mer information finns i dokumentet om [redundans för haveri beredskap](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) . 
- Vid DR: Data, logbackups, logg, delade volymer för frågor och svar (märkta med instans installation av frågor och svar) har kon figurer ATS för instans installationen av frågor och svar.
- Start volym för **SKU-typ I klass** replikeras till en Dr-nod.

## <a name="5-hsr-with-stonith"></a>5. HSR med STONITH
 
Den här topologin har stöd för två noder för HSR-konfigurationen (HANA System Replication). Den här konfigurationen stöds bara för enstaka HANA-instanser på en nod. Innebär att MCOS-scenarier inte stöds.

**Från och med nu stöds den här arkitekturen bara för SUSE operativ system.**


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![HSR-with-STONITH. png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Används för STONITH |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | Används för STONITH |

### <a name="storage"></a>Storage
Följande mountpoints är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för produktions-SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På den sekundära noden**|
|/hana/shared/SID | HANA-installation för sekundär-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för sekundär SID | 
|/hana/log/SID/mnt00001 | Logga filer installera för sekundärt SID | 
|/hana/logbackups/SID | Gör om loggar för sekundärt SID |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- För MCOS: Distribution av volym storlek baseras på databasens storlek i minnet. Avsnittet [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) innehåller information om vilka databas storlekar i minnet som stöds med multisid-miljön.
- STONITH: En SBD har kon figurer ATS för installationen av STONITH. En användning av STONITH är dock valfri.


## <a name="6-hsr-with-dr"></a>6. HSR med DR
 
Den här topologin har stöd för två noder för HSR-konfigurationen (HANA System Replication). Både den normala och den vanliga DR-funktionen stöds. Dessa konfigurationer stöds endast för enstaka HANA-instanser på en nod. Innebär att MCOS-scenarier inte stöds med de här konfigurationerna.

I diagrammet illustreras scenariot för flera syften var på DR-platsen, HLI-enheten används för frågor mot frågor och svar medan produktions åtgärderna körs från den primära platsen. Vid tidpunkten för DR-redundans (eller failover-test) tas frågor om frågor och svar på DR-platsen ned. 



### <a name="architecture-diagram"></a>Arkitekturdiagram  

![HSR-with-DR. png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Konfigurerad men används inte |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Används för STONITH |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Konfigurerad men används inte |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | Används för STONITH |

### <a name="storage"></a>Storage
Följande mountpoints är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära noden på den primära platsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för produktions-SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På den sekundära noden på den primära platsen**|
|/hana/shared/SID | HANA-installation för sekundär-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för sekundär SID | 
|/hana/log/SID/mnt00001 | Logga filer installera för sekundärt SID | 
|/hana/logbackups/SID | Gör om loggar för sekundärt SID |
|**På DR-webbplatsen**|
|/hana/shared/SID | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för produktions-SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktions-SID | 
|/hana/shared/QA-SID | HANA-installation för frågor och svar-SID | 
|/hana/data/QA-SID/mnt00001 | Filer som installeras för frågor och svar-SID | 
|/hana/log/QA-SID/mnt00001 | Loggfiler installeras för frågor och svar-SID |
|/hana/logbackups/QA-SID | Gör om loggar för säkerhets frågor och svar |

### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- För MCOS: Distribution av volym storlek baseras på databasens storlek i minnet. Avsnittet [Översikt och arkitektur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) innehåller information om vilka databas storlekar i minnet som stöds med multisid-miljön.
- STONITH: En SBD har kon figurer ATS för installationen av STONITH. En användning av STONITH är dock valfri.
- Vid DR: Det **krävs två uppsättningar lagrings volymer** för replikering av primära och sekundära noder.
- Vid DR: Volymerna och mountpoints konfigureras (markerade som "krävs för HANA-installation") för produktion HANA-instansen-installation på DR HLI-enheten. 
- Vid DR: Data, logbackups och delade volymer (markerade som "lagrings replikering") replikeras via ögonblicks bilder från produktions platsen. Dessa volymer monteras endast under redundansväxlingen. Mer information finns i dokumentet om [redundans för haveri beredskap](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) . 
- Vid DR: Data, logbackups, logg, delade volymer för frågor och svar (märkta med instans installation av frågor och svar) har kon figurer ATS för instans installationen av frågor och svar.
- Start volym för **SKU-typ I klass** replikeras till en Dr-nod.


## <a name="7-host-auto-failover-11"></a>7. Automatisk redundans för värd (1 + 1)
 
Den här topologin har stöd för två noder i en konfiguration för automatisk redundans för värd. Det finns en nod med Master/Worker-rollen och annan som vänte läge. **SAP stöder bara det här scenariot för S/4 HANA.** Se OSS Obs ”[2408419 - SAP S/4HANA - stöd för flera noder](https://launchpad.support.sap.com/#/notes/2408419)” för mer information.



### <a name="architecture-diagram"></a>Arkitekturdiagram  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Nod-till-nod-kommunikation |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande mountpoints är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På huvud-och vänte noder**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för produktions-SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |



### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
- I vänte läge: Volymerna och mountpoints har kon figurer ATS (markerade som "krävs för HANA-installation") för den HANA-instans som installeras på enheten för vänte läge.
 

## <a name="8-scale-out-with-standby"></a>8. Skala ut med vänte läge
 
Den här topologin stöder flera noder i en skalbar konfiguration. Det finns en nod med en huvud roll, en eller flera noder med arbets rollen och en eller flera noder som vänte läge. Det kan dock bara finnas en huvudnod vid en viss tidpunkt.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Nod-till-nod-kommunikation |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande mountpoints är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På huvud-, arbets-och vänte noder**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för produktions-SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |


## <a name="9-scale-out-without-standby"></a>9. Skala ut utan vänte läge
 
Den här topologin stöder flera noder i en skalbar konfiguration. Det finns en nod med en huvud roll och en eller mode-noder med arbets rollen. Det kan dock bara finnas en huvudnod vid en viss tidpunkt.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Nod-till-nod-kommunikation |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande mountpoints är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På Master-och Worker-noderna**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för produktions-SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Skala ut med DR
 
Den här topologin stöder flera noder i en skalbarhet med en DR. Både normal och dubbel användnings DR stöds. I diagrammet visas endast det enda syftet med DR. Du kan begära den här topologin med eller utan noden vänte läge.


### <a name="architecture-diagram"></a>Arkitekturdiagram  

![scaleout-with-Dr. png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Följande nätverks gränssnitt är förkonfigurerade:

| LOGISKA NIC-GRÄNSSNITT | SKU-TYP | Namn med SUSE OS | Namn med RHEL OS | Användningsfall|
| --- | --- | --- | --- | --- |
| G | TYP I | ETH0. Tenant | eno1. Tenant | Klient till HLI |
| B | TYP I | ETH2. Tenant | eno3. Tenant | Nod-till-nod-kommunikation |
| C | TYP I | eth1. Tenant | eno2. Tenant | Nod till lagring |
| D | TYP I | eth4. Tenant | eno4. Tenant | Konfigurerad men används inte |
| G | TYP II | vlan\<tenantNo> | team0. Tenant | Klient till HLI |
| B | TYP II | vlan\<tenantNo+2> | team0. Tenant + 2 | Nod-till-nod-kommunikation |
| C | TYP II | VLAN\<-tenantNo + 1 > | team0. klient organisation + 1 | Nod till lagring |
| D | TYP II | VLAN\<-tenantNo + 3 > | team0. klient + 3 | Konfigurerad men används inte |

### <a name="storage"></a>Storage
Följande mountpoints är förkonfigurerade:

| Monterings punkt | Användningsfall | 
| --- | --- |
|**På den primära noden**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för produktions-SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktions-SID | 
|/hana/logbackups/SID | Gör om loggar för produktions-SID |
|**På DR-noden**|
|/hana/shared | HANA-installation för produktions-SID | 
|/hana/data/SID/mnt00001 | Filer som installeras för produktions-SID | 
|/hana/log/SID/mnt00001 | Loggfiler installera för produktions-SID | 


### <a name="key-considerations"></a>Viktiga överväganden
- /usr/sap/SID är en symbolisk länk till/hana/shared/SID.
-  Vid DR: Volymerna och mountpoints konfigureras (markerade som "krävs för HANA-installation") för produktion HANA-instansen-installation på DR HLI-enheten. 
- Vid DR: Data, logbackups och delade volymer (markerade som "lagrings replikering") replikeras via ögonblicks bilder från produktions platsen. Dessa volymer monteras endast under redundansväxlingen. Mer information finns i dokumentet om [redundans för haveri beredskap](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) . 
- Start volym för **SKU-typ I klass** replikeras till en Dr-nod.


## <a name="next-steps"></a>Nästa steg
- Se [infrastruktur och anslutning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) för HLI
- Se [hög tillgänglighet och haveri beredskap](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) för HLI
