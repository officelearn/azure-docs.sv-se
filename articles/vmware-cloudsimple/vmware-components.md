---
title: Azure VMware-lösning av CloudSimple - komponenter för privata moln VMware
description: Beskriver hur VMware-komponenter är installerade på privat moln
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e6548a5a04e32b374a8a9c29d2ca5f89fd65c78
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160192"
---
# <a name="private-cloud-vmware-components"></a>VMware-komponenter för privata moln

Ett privat moln är en isolerad VMware-stacken (ESXi-värdar, vCenter, virtuellt SAN-nätverk och NSX) miljön som hanteras av en vCenter-server i en hanteringsdomän.  Tjänsten CloudSimple kan du distribuera VMware internt i Azure bare metal-infrastrukturen i Azure-platser.  Privata moln är integrerade med resten av Azure-molnet.  Ett privat moln distribueras med följande VMware-stackkomponenter:

* **VMware ESXi -** hypervisor-program på Azure dedikerade noder
* **VMware vCenter -** tillämpning för central hantering av privat molnmiljö för vSphere
* **VMware virtuellt SAN -** en hyperkonvergerad lösning
* **VMware NSX Datacenter -** virtualisering och säkerhetsprogram  

## <a name="vmware-component-versions"></a>Versioner av VMware-komponent

Ett privat moln VMware-stacken distribueras med följande programvaruversion.

| Komponent | Version | Licensierad version |
|-----------|---------|------------------|
| ESXi | 6.7U1 | Enterprise Plus |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.3 | Avancerat |

## <a name="esxi"></a>ESXi

VMware ESXi har installerats på den köpta CloudSimple noder när du skapar ett privat moln.  ESXi innehåller hypervisor-programmet för att distribuera arbetsbelastningen virtuella datorer (VM).  Noderna kommer hyper-Konvergerad infrastruktur (beräkning och lagring) i ditt privata moln.  Noderna ingår i vSphere-kluster på det privata molnet.  Varje nod har fyra fysiska nätverk gränssnitt som är anslutna till underlay nätverk.  Två fysiska nätverksgränssnitt som används för att skapa en **vSphere distribuerade växla (VDS)** på vCenter och två används för att skapa en **NSX-hanterade virtuella distribuerade växeln (N-VDS)**.  Nätverksgränssnitt har konfigurerats i aktivt-aktivt läge för hög tillgänglighet.

Lär dig mer om VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter server-enhet

vCenter server-enhet (VCSA) tillhandahåller funktioner för autentisering, hantering och dirigering för VMware-lösning genom CloudSimple. VCSA med inbäddade plattform tjänster Controller (PSC) distribueras när du skapar ditt privata moln.  VCSA distribueras på vSphere-kluster som skapas när du distribuerar ditt privata moln.  Varje privata moln har sin egen VCSA.  Utökningen av ett privat moln lägger till noder VCSA i det privata molnet.

### <a name="vcenter-single-sign-on"></a>vCenter enkel inloggning

Inbäddade plattform tjänster-styrenheten på VCSA är associerad med en **vCenter enkel inloggning på domän**.  Domännamnet är **cloudsimple.local**.  En standardanvändaren **CloudOwner@cloudsimple.com** har skapats för dig att få åtkomst till vCenter.  Du kan lägga till på lokal/Azure active directory [identitet källor för vCenter](https://docs.azure.cloudsimple.com/set-vcenter-identity/).

## <a name="vsan-storage"></a>virtuellt SAN-lagring

Privata moln skapas med fullständigt konfigurerad all-flash virtuellt SAN-lagring, lokala till klustret.  Minst tre noder i samma SKU krävs för att skapa ett vSphere-kluster med virtuella SAN-nätverket datalager.  Avduplicering och komprimering är aktiverat på virtuellt SAN-datalagret som standard.  Två diskgrupper skapas på varje nod i vSphere-kluster. Varje disk innehåller en disk i cachen och tre kapacitet diskar.

En standardprincip virtuellt SAN-lagring är skapat på vSphere-klustret och tillämpas på virtuellt SAN-datalager.  Den här principen bestämmer hur lagringsobjekt VM är etablerad och allokeras i databasen för att säkerställa nödvändig servicenivå.  Principen för lagring definierar den **i antal tolererade fel (FTT)** och **feltolerans metoden**.  Du kan skapa nya principer för lagring och koppla dem till de virtuella datorerna. Om du vill behålla SLA, måste 25% ledig kapacitet underhållas på virtuellt SAN-datalagret.  

### <a name="default-vsan-storage-policy"></a>Standardprincipen för virtuellt SAN-lagring

Tabellen nedan visar standard principparametrar för virtuellt SAN-lagring.

| Antalet noder i vSphere-kluster | FTT | Feltolerans metod |
|------------------------------------|-----|--------------------------|
| 3 och 4 noder | 1 | RAID 1 (spegling) - skapar 2 kopior |
| 5 till 16 noder | 2 | RAID 1 (spegling) - skapar 3 kopior |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Datacenter tillhandahåller nätverksvirtualisering, micro segmentering och säkerhetsfunktioner för nätverk i ditt privata moln.  Du kan konfigurera alla tjänster som stöds av NSX datacenter i ditt privata moln via NSX.  När du skapar ett privat moln följande NSX komponenter installeras och konfigureras.

* NSXT Manager
* Transport-zoner
* Värden och Edge överordnade profilen
* Logisk växel för Edge Transport, Ext1 och Ext2
* IP-adresspool för ESXi-Transport nod
* IP-adresspool för kantnoden för Transport
* Edge-noder
* DRS anti-affinity regeln för styrenhet och Edge virtuella datorer
* Nivå 0-Router
* Aktivera BGP på Tier0 routern

## <a name="vsphere-cluster"></a>vSphere-kluster

ESXi-värdar konfigureras som ett kluster för att garantera hög tillgänglighet för det privata molnet.  När du skapar ett privat moln kan distribueras komponenterna för vSphere på det första klustret.  En resurspool skapas för komponenterna och alla hantering av virtuella datorer distribueras i den här resurspoolen. Det första klustret kan inte tas bort för att minska det privata molnet.  vSphere-kluster ger hög tillgänglighet för virtuella datorer med hjälp av **vSphere HA**.  I antal tolererade fel baseras på antalet tillgängliga noder i klustret.  Du kan använda formeln ```Number of nodes = 2N+1``` där ```N``` är antalet fel ska kunna hanteras.

### <a name="vsphere-cluster-limits"></a>gränser för vSphere-kluster

| Resource | Gräns |
|----------|-------|
| Minsta antalet noder för att skapa ett privat moln (första vSphere-kluster) | 3 |
| Högsta antalet noder i en vSphere-kluster i ett privat moln | 16 |
| Högsta antalet noder i ett privat moln | 64 |
| Maximalt antal vSphere-kluster i ett privat moln | 21 |
| Minsta antalet noder i en ny vSphere kluster | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware infrastrukturunderhåll

Ibland är det nödvändigt att göra ändringar i konfigurationen av VMware-infrastruktur. För närvarande kan dessa intervall kan inträffa var 1 – 2 månad, men frekvensen förväntas att avstå från att över tid. Den här typen av underhåll kan vanligtvis göras utan att avbryta normal konsumtion av CloudSimple-tjänster. Följande tjänster fortsätta att fungera utan någon inverkan under ett Underhåll intervall VMware:

* VMware Hanteringsplanet och program
* vCenter-åtkomst
* Alla nätverk och lagring
* Alla Azure-trafik

## <a name="updates-and-upgrades"></a>Uppdateringar och uppgraderingar

CloudSimple ansvarar för livscykelhantering av VMware-programvara (ESXi, vCenter, PSC och NSX) i det privata molnet.

Programuppdateringar är:

* **Korrigeringar**. Säkerhetsuppdateringar eller felkorrigeringar som publicerats av VMware.
* **Uppdateringar**. Lägre version ändring av en VMware-stacken komponent.
* **Uppgraderingar**. Huvudversion ändring av en VMware-stacken komponent.

CloudSimple testar en viktig säkerhetskorrigering så snart det blir tillgängligt från VMware. Per serviceavtal samlar CloudSimple ut säkerhetskorrigeringen till privata molnmiljöer inom en vecka.

CloudSimple innehåller kvartalsvis underhållsuppdateringar av programkomponenter för VMware. När en ny huvudversion av VMware-programvara är tillgänglig fungerar CloudSimple med kunder för att samordna en lämplig underhållsperiod för uppgraderingen.  

## <a name="next-steps"></a>Nästa steg

* [CloudSimple underhåll och uppdateringar](cloudsimple-maintenance-updates.md)