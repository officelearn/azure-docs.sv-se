---
title: CloudSimple underhåll och uppdateringar
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver CloudSimple-serviceprocessen för schemalagt underhåll och uppdateringar
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025035"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple underhåll och uppdateringar

Den privata molnmiljön är utformad för att inte ha någon enskild felpunkt.

* ESXi-kluster är konfigurerade med vSphere Hög tillgänglighet (HA). Kluster är dimensionerade för att ha minst en reservnod för återhämtning.
* Redundant primärlagring tillhandahålls av vSAN, som kräver minst tre noder för att ge skydd mot ett enda fel. vSAN kan konfigureras för att ge högre återhämtning för större kluster.
* virtuella datorer med vCenter, PSC och NSX Manager är konfigurerade med RAID-10-lagring för att skydda mot lagringsfel. De virtuella datorerna skyddas mot nod/nätverksfel av vSphere HA.
* ESXi-värdar har redundanta fans och nätverkskort.
* TOR- och ryggbrytare är konfigurerade i HA-par för att ge återhämtning.

CloudSimple övervakar kontinuerligt följande virtuella datorer för drifttid och tillgänglighet och tillhandahåller SLA-lösningar för tillgänglighet:

* ESXi värdar
* vCenter
* Psc
* NSX-chef

CloudSimple övervakar också följande kontinuerligt för fel:

* Hårddiskar
* Fysiska nätverkskortportar
* Servrar
* Fläktar
* Power
* Switchar
* Växla portar

Om en disk eller nod misslyckas läggs en ny nod automatiskt till i det berörda VMware-klustret för att omedelbart få den tillbaka till hälsotillståndet.

CloudSimple säkerhetskopierar, underhåller och uppdaterar dessa VMware-element i de privata molnen:

* Esxi
* vCenter-plattformstjänster
* Controller
* vSAN (på ett sätt)
* Nsx

## <a name="back-up-and-restore"></a>Säkerhetskopiera och återställa

CloudSimple backup innehåller:

* Nattliga inkrementella säkerhetskopior av regler för vCenter, PSC och DVS.
* vCenter-inbyggda API:er för att säkerhetskopiera komponenter i programlagret.
* Automatisk säkerhetskopiering före uppdatering eller uppgradering av VMware-hanteringsprogrammet.
* vCenter-datakryptering vid källan innan data överförs via en TLS1.2-krypterad kanal till Azure. Data lagras i en Azure-blob där den replikeras mellan regioner.

Du kan begära en återställning genom att öppna en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Underhåll

CloudSimple gör flera typer av planerat underhåll.

### <a name="backendinternal-maintenance"></a>Backend/internt underhåll

Det här underhållet innebär vanligtvis att konfigurera om fysiska tillgångar eller installera programkorrigeringar. Det påverkar inte normal konsumtion av de tillgångar som servas. Med redundanta nätverkskort som går till varje fysiskt rack påverkas inte normal nätverkstrafik och privata molnåtgärder. Du kanske märker en prestandapåverkan endast om din organisation förväntar sig att använda den fullständiga redundanta bandbredden under underhållsintervallet.

### <a name="cloudsimple-portal-maintenance"></a>Underhåll av CloudSimple-portal

Vissa begränsade driftstopp krävs när CloudSimple-kontrollplanet eller infrastrukturen uppdateras. För närvarande kan underhållsintervall vara lika frekventa som en gång per månad. Frekvensen förväntas minska med tiden. CloudSimple avisering för portalunderhåll och håller intervallet så kort som möjligt. Under ett portalunderhållsintervall fortsätter följande tjänster att fungera utan någon påverkan:

* VMware-hanteringsplan och applikationer
* vCenter-åtkomst
* Alla nätverk och lagring
* All Azure-trafik

### <a name="vmware-infrastructure-maintenance"></a>VMware infrastruktur underhåll

Ibland är det nödvändigt att göra ändringar i konfigurationen av VMware-infrastrukturen.  För närvarande kan dessa intervall uppstå var 1-2 månader, men frekvensen förväntas minska med tiden. Den här typen av underhåll kan vanligtvis göras utan att avbryta normal förbrukning av CloudSimple-tjänsterna. Under ett VMware-underhållsintervall fortsätter följande tjänster att fungera utan någon påverkan:

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

[Säkerhetskopiera virtuella virtuella arbetsbelastningar med Veeam](backup-workloads-veeam.md)
