---
title: Privata VMware-komponenter i molnet
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver hur VMware-komponenter installeras i privata moln
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279512"
---
# <a name="private-cloud-vmware-components"></a>Privata VMware-komponenter i molnet

Ett privat moln är en isolerad VMware-stack (ESXi-värdar, vCenter, vSAN och NSX) som hanteras av en vCenter-server i en hanteringsdomän.  Med CloudSimple-tjänsten kan du distribuera VMware internt på Azure bare metal-infrastruktur på Azure-platser.  Privata moln är integrerade med resten av Azure Cloud.  Ett privat moln distribueras med följande VMware-stackkomponenter:

* **VMware ESXi -** Hypervisor på Azure-dedikerade noder
* **VMware vCenter -** Apparat för centraliserad hantering av privata moln vSphere miljö
* **VMware vSAN -** Hyperkonvergerad infrastrukturlösning
* **VMware NSX datacenter -** Programvara för nätverksvirtualisering och säkerhet  

## <a name="vmware-component-versions"></a>VMware-komponentversioner

En privat Cloud VMware stack distribueras med följande programvaruversion.

| Komponent | Version | Licensierad version |
|-----------|---------|------------------|
| Esxi | 6.7U2 | Företag Plus |
| vCenter | 6.7U2 | vCenter-standard |
| vSAN (på ett sätt) | 6.7 | Enterprise |
| NSX-datacenter | 2.4.1 | Avancerat |

## <a name="esxi"></a>Esxi

VMware ESXi installeras på etablerade CloudSimple-noder när du skapar ett privat moln.  ESXi tillhandahåller hypervisorn för distribution av virtuella datorer för arbetsbelastning ( VIRTUELLA datorer).  Noder tillhandahåller hyperkonvergerad infrastruktur (beräkning och lagring) i ditt privata moln.  Noderna är en del av vSphere-klustret i det privata molnet.  Varje nod har fyra fysiska nätverksgränssnitt anslutna till underlagsnätverk.  Två fysiska nätverksgränssnitt används för att skapa en **vSphere Distributed Switch (VDS)** på vCenter och två används för att skapa en **NSX-hanterad virtuell distribuerad växel (N-VDS)**.  Nätverksgränssnitt konfigureras i aktivt aktivt läge för hög tillgänglighet.

Läs mer om VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter-serverinstallation

vCenter-serverinstallation (VCSA) tillhandahåller autentiserings-, hanterings- och orchestration-funktioner för VMware-lösning av CloudSimple. VCSA med inbyggd PSC (Platform Services Controller) distribueras när du skapar ditt privata moln.  VCSA distribueras i vSphere-klustret som skapas när du distribuerar ditt privata moln.  Varje privat moln har sin egen VCSA.  Expansion av ett privat moln lägger till noderna till VCSA i det privata molnet.

### <a name="vcenter-single-sign-on"></a>vCenter enkel inloggning

Embedded Platform Services Controller på VCSA är associerad med en **vCenter Single Sign-On-domän**.  Domännamnet är **cloudsimple.local**.  En standardanvändare **CloudOwner@cloudsimple.com** skapas för att du ska kunna komma åt vCenter.  Du kan lägga till lokala/Azure-identitetskällor för active directory [för vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>vSAN-lagring

Privata moln skapas med fullt konfigurerad vSAN-lagring, lokal för klustret.  Minst tre noder av samma SKU krävs för att skapa ett vSphere-kluster med vSAN-datalager.  De-duplicering och komprimering är aktiverade i vSAN-datalagret som standard.  Två diskgrupper skapas på varje nod i vSphere-klustret. Varje diskgrupp innehåller en cachedisk och diskar med tre kapacitet.

En standardprincip för vSAN-lagring skapas i vSphere-klustret och tillämpas på vSAN-datalagret.  Den här principen avgör hur vm-lagringsobjekten etableras och allokeras i datalagret för att garantera den servicenivå som krävs.  Lagringsprincipen definierar **felen att tolerera (FTT)** och **feltoleransmetoden**.  Du kan skapa nya lagringsprinciper och tillämpa dem på de virtuella datorerna. För att upprätthålla SLA måste 25 % ledig kapacitet upprätthållas i vSAN-datalagret.  

### <a name="default-vsan-storage-policy"></a>Standardprincip för vSAN-lagring

Tabellen nedan visar standardparametrarna för vSAN-lagringsprincip.

| Antal noder i vSphere Cluster | Finanstransaktioner | Metod för feltolerans |
|------------------------------------|-----|--------------------------|
| 3 och 4 noder | 1 | RAID 1 (spegling) - skapar 2 kopior |
| 5 till 16 noder | 2 | RAID 1 (spegling) - skapar 3 kopior |

## <a name="nsx-data-center"></a>NSX-datacenter

NSX Data Center tillhandahåller nätverksvirtualisering, mikrosegmentering och nätverkssäkerhetsfunktioner i ditt privata moln.  Du kan konfigurera alla tjänster som stöds av NSX Data Center i ditt privata moln via NSX.  När du skapar ett privat moln installeras och konfigureras följande NSX-komponenter.

* NSXT-chef
* Transportzoner
* Värd- och Edge Uplink-profil
* Logisk växel för Edge Transport, Ext1 och Ext2
* IP-pool för ESXi Transport Nod
* IP-pool för edge transportnod
* Kantnoder
* DRS-regeln om tillhörighet för styrenhet och virtuella kanter
* Nivå 0-router
* Aktivera BGP på Tier0-router

## <a name="vsphere-cluster"></a>vSphere-kluster

ESXi-värdar konfigureras som ett kluster för att säkerställa hög tillgänglighet för det privata molnet.  När du skapar ett privat moln distribueras hanteringskomponenter i vSphere i det första klustret.  En resurspool skapas för hanteringskomponenter och alla virtuella hanteringsresurser distribueras i den här resurspoolen. Det första klustret kan inte tas bort för att krympa det privata molnet.  vSphere-klustret ger hög tillgänglighet för virtuella datorer med **vSphere HA**.  Fel att tolerera baseras på antalet tillgängliga noder i klustret.  Du kan använda ```Number of nodes = 2N+1``` ```N``` formeln där är antalet fel att tolerera.

### <a name="vsphere-cluster-limits"></a>vSphere klustergränser

| Resurs | Gräns |
|----------|-------|
| Minsta antal noder för att skapa ett privat moln (första vSphere-klustret) | 3 |
| Maximalt antal noder i ett vSphere-kluster i ett privat moln | 16 |
| Maximalt antal noder i ett privat moln | 64 |
| Maximalt antal vSphere-kluster i ett privat moln | 21 |
| Minsta antal noder i ett nytt vSphere-kluster | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware infrastruktur underhåll

Ibland är det nödvändigt att göra ändringar i konfigurationen av VMware-infrastrukturen. För närvarande kan dessa intervall uppstå var 1-2 månader, men frekvensen förväntas minska med tiden. Den här typen av underhåll kan vanligtvis göras utan att avbryta normal förbrukning av CloudSimple-tjänsterna. Under ett VMware-underhållsintervall fortsätter följande tjänster att fungera utan någon påverkan:

* VMware-hanteringsplan och applikationer
* vCenter-åtkomst
* Alla nätverk och lagring
* All Azure-trafik

## <a name="updates-and-upgrades"></a>Uppdateringar och uppgraderingar

CloudSimple ansvarar för livscykelhanteringen av VMware-programvara (ESXi, vCenter, PSC och NSX) i det privata molnet.

Programuppdateringar inkluderar:

* **Patchar**. Säkerhetskorrigeringar eller buggfixar som släppts av VMware.
* **Uppdateringar**. Ändring av en VMware-stackkomponent.
* **Uppgraderingar**. Huvudversionsändring av en VMware-stackkomponent.

CloudSimple testar en viktig säkerhetskorrigering så snart den blir tillgänglig från VMware. Per SLA distribuerar CloudSimple säkerhetskorrigeringen till privata molnmiljöer inom en vecka.

CloudSimple tillhandahåller kvartalsvisa underhållsuppdateringar till VMware-programvarukomponenter. När en ny huvudversion av VMware-programvaran är tillgänglig arbetar CloudSimple med kunder för att samordna ett lämpligt underhållsfönster för uppgradering.  

## <a name="next-steps"></a>Nästa steg

* [CloudSimple underhåll och uppdateringar](cloudsimple-maintenance-updates.md)
