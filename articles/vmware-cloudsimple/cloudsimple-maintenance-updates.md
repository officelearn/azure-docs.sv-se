---
title: Azure VMware-lösningar (AVS) – AVS-underhåll och uppdateringar
description: Beskriver AVS-tjänsteprocessen för schemalagt underhåll och uppdateringar
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf5937183fc20579ecd21aca8543a0a78d4b9ff3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025035"
---
# <a name="avs-maintenance-and-updates"></a>AVS-underhåll och uppdateringar

AVS-miljön för privata moln är utformad för att inte ha någon enskild felpunkt.

* ESXi-kluster konfigureras med vSphere hög tillgänglighet (HA). Det finns minst en reserv nod för återhämtning i klustret.
* Redundant primär lagring tillhandahålls av virtuellt San, vilket kräver minst tre noder för att ge skydd mot ett enskilt haveri. Virtuellt SAN kan konfigureras för att ge högre återhämtning för större kluster.
* vCenter-, PSC-och NSX Manager-VM: ar konfigureras med RAID-10-lagring för att skydda mot lagrings problem. De virtuella datorerna skyddas mot nod-/nätverks problem av vSphere HA.
* ESXi-värdar har redundanta fläktar och nätverkskort.
* Växlarna TOR och rygg har kon figurer ATS i HA par för att ge återhämtning.

AVS övervakar kontinuerligt följande virtuella datorer för drift tid och tillgänglighet och ger tillgänglighets service avtal:

* ESXi-värdar
* vCenter
* PSC
* NSX Manager

AVS övervakar också följande kontinuerligt för problem:

* Hård diskar
* Fysiska NIC-portar
* Servrar
* Fläktar
* Power
* Parametrar
* Växla portar

Om en disk eller nod Miss lyckas läggs en ny nod automatiskt till i det berörda VMware-klustret för att få tillbaka tillståndet omedelbart.

AVS säkerhetskopierar, underhåller och uppdaterar dessa VMware-element i de privata AVS-molnen:

* ESXi
* vCenter Platform-tjänster
* Domänkontrollant
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Säkerhetskopiera och återställ

AVS-säkerhetskopiering innehåller:

* Stegvisa säkerhets kopieringar av vCenter-, PSC-och DVS-regler.
* vCenter inbyggda API: er för att säkerhetskopiera komponenter i program skiktet.
* Automatisk säkerhets kopiering före uppdatering eller uppgradering av VMware Management-programvaran.
* vCenter Data Encryption vid källan innan data överförs via en TLS 1.2-krypterad kanal till Azure. Data lagras i en Azure-Blob där den replikeras mellan regioner.

Du kan begära en återställning genom att öppna en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Underhåll

AVS gör flera typer av planerat underhåll.

### <a name="backendinternal-maintenance"></a>Server del/internt underhåll

Det här underhållet omfattar vanligt vis omkonfigurering av fysiska till gångar eller installation av program varu uppdateringar. Den påverkar inte normal förbrukning av de till gångar som servas. Med redundanta nätverkskort som går till varje fysiskt rack påverkas inte normala nätverks trafik-och moln aktiviteter i molnet. Du kanske upptäcker att en prestanda påverkas endast om organisationen förväntar sig att använda den fullständiga redundanta bandbredden under underhålls intervallet.

### <a name="avs-portal-maintenance"></a>Underhåll av AVS-Portal

Vissa begränsade avbrott i tjänsten krävs när AVS-kontroll planet eller infrastrukturen uppdateras. För närvarande kan underhålls intervall vara så frekventa som ofta en gång per månad. Frekvensen förväntas nekas över tid. AVS tillhandahåller ett meddelande om Portal underhåll och håller intervallet så kort som möjligt. Följande tjänster fortsätter att fungera utan påverkan under ett underhålls intervall för portalen:

* Hanterings plan och program för VMware
* vCenter-åtkomst
* Alla nätverk och lagring
* All Azure-trafik

### <a name="vmware-infrastructure-maintenance"></a>Underhåll av VMware-infrastruktur

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

[Säkerhetskopiera virtuella arbets belastnings datorer med Veeam](backup-workloads-veeam.md)
