---
title: Azure VMware-lösningar (AVS)-AVS privat moln VMware-komponenter
description: Beskriver hur VMware-komponenter installeras i det privata AVS-molnet
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea6c22c3957f72a0a416ce7ae42c62ff5a0791a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016790"
---
# <a name="avs-private-cloud-vmware-components"></a>Molnets privata moln VMware-komponenter

Ett privat AVS-moln är en isolerad VMware-stack (ESXi-värdar, vCenter-, virtuellt San-och NSX) som hanteras av en vCenter-Server i en hanterings domän. Med AVS-tjänsten kan du distribuera VMware i Azure Bare Metal-infrastruktur på Azure-platser. AVS-privata moln är integrerade med resten av Azure-molnet. Ett privat AVS-moln har distribuerats med följande VMware stack-komponenter:

* **VMware ESXi-** Hypervisor på Azure-dedikerade noder
* **VMware vCenter –** Utrustning för central hantering av vSphere-miljön i AVS-molnet
* **VMware-virtuellt San –** Lösning för Hyper-konvergerad infrastruktur
* **VMware NSX Data Center –** Nätverksvirtualisering och säkerhets program vara  

## <a name="vmware-component-versions"></a>Versioner av VMware-komponenter

En VMware-stack i AVS-molnet distribueras med följande program varu version.

| Komponent | Version | Licensierad version |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Enterprise plus |
| vCenter | 6.7 U2 | vCenter Standard |
| vSAN | 6.7 | Företag |
| NSX Data Center | 2.4.1 | Advanced |

## <a name="esxi"></a>ESXi

VMware ESXi installeras på etablerade AVS-noder när du skapar ett privat AVS-moln. ESXi tillhandahåller hypervisorn för distribution av virtuella arbets belastnings datorer (VM). Noder tillhandahåller en aktiv infrastruktur (beräkning och lagring) på ditt moln privata moln. Noderna ingår i vSphere-klustret i det privata moln molnet. Varje nod har fyra fysiska nätverks gränssnitt som är anslutna till Underlay-nätverket. Två fysiska nätverks gränssnitt används för att skapa en **vSphere-distribuerad växel (VDS)** på vCenter och två används för att skapa en **NSX-hanterad virtuell DFS-växel (N-VDS)** . Nätverks gränssnitt har kon figurer ATS i aktivt-aktivt läge för hög tillgänglighet.

Läs mer på VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter Server-apparat

vCenter Server-installation (VCSA) tillhandahåller autentiserings-, hanterings-och Orchestration-funktioner för VMware-lösningar (AVS). VCSA med Embedded Platform Services Controller (PSC) distribueras när du skapar ditt AVS-privata moln. VCSA distribueras i vSphere-klustret som skapas när du distribuerar ditt AVS-privata moln. Varje AVS-privat moln har sin egen VCSA. Vid expansion av ett privat AVS-moln läggs noderna till i VCSA i det privata moln molnet.

### <a name="vcenter-single-sign-on"></a>enkel inloggning för vCenter

Embedded Platform service Controller på VCSA är associerad med en **vCenter-domän för enkel inloggning**. Domän namnet är **AVS. local**. En standard användare **CloudOwner@AVS.com** skapas så att du kan komma åt vCenter. Du kan lägga till dina lokala/Azure Active Directory [Identity sources för vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>Virtuellt SAN-lagring

AVS-privata moln skapas med fullständigt konfigurerade virtuellt SAN-lagring för hela Flash, lokalt till klustret. Minst tre noder av samma SKU krävs för att skapa ett vSphere-kluster med virtuellt San data lager. Avduplicering och komprimering är aktiverat i virtuellt San-datalagret som standard. Två disk grupper skapas på varje nod i vSphere-klustret. Varje disk grupp innehåller en cache-disk och tre kapacitets diskar.

En standard lagrings princip för virtuellt San skapas i vSphere-klustret och tillämpas på virtuellt San-datalagret. Den här principen avgör hur VM-lagrings objekt ska tillhandahållas och allokeras i data lagret för att garantera den tjänst nivå som krävs. Lagrings principen definierar de **problem som ska tolereras (FTT)** och **metoden för feltolerans**. Du kan skapa nya lagrings principer och tillämpa dem på de virtuella datorerna. För att upprätthålla SLA måste den 25% lediga kapaciteten vara kvar på virtuellt San-datalagret. 

### <a name="default-vsan-storage-policy"></a>Standard lagrings princip för virtuellt San

I tabellen nedan visas standard parametrarna för virtuellt San-lagrings principer.

| Antal noder i vSphere-kluster | FTT | Metod för feltolerans |
|------------------------------------|-----|--------------------------|
| 3-och 4 noder | 1 | RAID 1 (spegling) – skapar 2 kopior |
| 5 till 16 noder | 2 | RAID 1 (spegling) – skapar 3 kopior |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center tillhandahåller nätverksvirtualisering, mikrosegmentering och nätverks säkerhets funktioner i ditt moln privata moln. Du kan konfigurera alla tjänster som stöds av NSX Data Center på ditt moln privata moln genom NSX. När du skapar ett moln privat moln installeras och konfigureras följande NSX-komponenter.

* NSXT Manager
* Transport zoner
* Profil för värd och Edge-överordnad länk
* Logisk växel för Edge-Transport, Ext1 och ext2
* IP-pool för ESXi transport Node
* Noden IP-pool för Edge-Transport
* Edge-noder
* DRS för Controller-och Edge-VM: ar
* Nivå 0-router
* Aktivera BGP på Tier0-routern

## <a name="vsphere-cluster"></a>vSphere-kluster

ESXi-värdar konfigureras som ett kluster för att säkerställa hög tillgänglighet för det privata AVS-molnet. När du skapar ett moln privat moln distribueras hanterings komponenter i vSphere i det första klustret. En resurspool skapas för hanterings komponenter och alla virtuella hanterings datorer distribueras i den här resurspoolen. Det första klustret kan inte tas bort för att minska det privata molnet i AVS-molnet. vSphere-kluster ger hög tillgänglighet för virtuella datorer som använder **VSPHERE ha**. Fel som tolereras baseras på antalet tillgängliga noder i klustret. Du kan använda formeln ```Number of nodes = 2N+1``` där ```N``` är antalet felaktiga försök att tolerera.

### <a name="vsphere-cluster-limits"></a>vSphere kluster gränser

| Resurs | Gräns |
|----------|-------|
| Minsta antal noder för att skapa ett moln privat moln (första vSphere-kluster) | 3 |
| Maximalt antal noder i ett vSphere-kluster i ett moln privat moln | 16 |
| Maximalt antal noder i ett privat AVS-moln | 64 |
| Maximalt antal vSphere-kluster i ett privat AVS-moln | 21 |
| Minsta antal noder i ett nytt vSphere-kluster | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Underhåll av VMware-infrastruktur

Ibland är det nödvändigt att göra ändringar i konfigurationen av VMware-infrastrukturen. För närvarande kan dessa intervall inträffa var 1-2 månad, men frekvensen förväntas avböjas över tid. Den här typen av underhåll kan vanligt vis göras utan att avbryta normal förbrukning av AVS-tjänsterna. Under ett underhålls intervall för VMware fortsätter följande tjänster att fungera utan påverkan:

* Hanterings plan och program för VMware
* vCenter-åtkomst
* Alla nätverk och lagring
* All Azure-trafik

## <a name="updates-and-upgrades"></a>Uppdateringar och uppgraderingar

AVS ansvarar för livs cykel hantering av VMware-programvara (ESXi, vCenter, PSC och NSX) i det molnbaserade privata molnet.

Program uppdateringar omfattar:

* **Korrigeringar**. Säkerhets korrigeringar eller fel korrigeringar som publicerats av VMware.
* **Uppdateringar**. Lägre versions ändring av en VMware stack-komponent.
* **Uppgraderingar**. Huvud versions ändring av en VMware stack-komponent.

AVS testar en kritisk säkerhets korrigering så snart den blir tillgänglig från VMware. Enligt SLA samlar AVS in säkerhets korrigeringen till molnets privata moln miljöer inom en vecka.

AVS ger kvartals Visa uppdateringar av VMware-programkomponenter. När en ny huvud version av VMware-programvaran är tillgänglig, fungerar AVS med kunder för att samordna en lämplig underhålls period för uppgradering. 

## <a name="next-steps"></a>Nästa steg

* [AVS-underhåll och uppdateringar](cloudsimple-maintenance-updates.md)
