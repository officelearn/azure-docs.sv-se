---
title: Azure VMware-lösning av CloudSimple - CloudSimple underhåll och uppdateringar
description: Beskriver hur CloudSimple tjänsten för schemalagt underhåll och uppdateringar
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160252"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple underhåll och uppdateringar

Privat moln-miljön är lätt att ingen enskild felpunkt:

* ESXi-klustren har konfigurerats med hög tillgänglighet för vSphere. Klustren storlek om du vill ha minst en extra nod för återhämtning.
* Redundant primärlagring tillhandahålls av virtuellt SAN-nätverk, vilket kräver minst tre noder för att ge skydd mot ett enstaka fel. virtuellt SAN-nätverk kan konfigureras för att ge högre återhämtning för större kluster.
* vCenter och PSC NSX Manager virtuella datorer är konfigurerade med RAID 10-principen för lagring som skydd mot fel för lagring. De virtuella datorerna skyddas mot noden/nätverksfel av vSphere hög tillgänglighet.
* ESXi-värdar ha redundanta fläktar och nätverkskort.
* TOR och stamnät växlar konfigureras parvis hög tillgänglighet för att tillhandahålla återhämtning.

CloudSimple som ständigt övervakar följande virtuella datorer för drifttid och tillgänglighet och som ger tillgänglighets-SLA:

* ESXi-värdar
* vCenter
* PSC
* NSX Manager

CloudSimple övervakar även följande kontinuerligt för fel:

* Hårddiskar
* Fysiska NIC-portar
* Servrar
* Fans
* Power
* Växlar
* Switch-portar

Om en disk eller en nod misslyckas, läggs automatiskt en ny nod i berörda VMware-klustret att ta med dem till hälsotillståndet omedelbart.

CloudSimple säkerhetskopierar, underhåller och uppdaterar dessa VMware-element i privata moln:

* ESXi
* vCenter-plattformstjänster
* Kontrollenhet
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Säkerhetskopiera och återställa

CloudSimple säkerhetskopiering inkluderar:

* Inkrementella säkerhetskopieringar av vCenter och PSC DVS regler.
* Användning av vCenter interna API: er för säkerhetskopiering av komponenter på programnivå.
* Automatisk säkerhetskopiering innan en uppdatering eller uppgradering av hanteringsprogramvara för VMware.
* Datakryptering vid källan av vCenter innan dataöverföring via en krypterad kanal TLS1.2 till Azure. Data lagras i en Azure-blob där de replikeras över regioner.

Du kan begära en återställning genom att öppna en [supportförfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Underhåll

CloudSimple har flera typer av planerat underhåll.

### <a name="backendinternal-maintenance"></a>Serverdel/internt Underhåll

Det här underhållet omfattar vanligtvis konfigureras om fysiska tillgångar eller installera programuppdateringar. Det påverkar inte normal konsumtion av de resurser som betjänas. Med redundanta nätverkskort ska varje fysisk rack, påverkas normal nätverkstrafik och privata moln åtgärder inte. Du kanske märker en prestandapåverkan endast om din organisation förväntar sig att använda den fullständiga redundant bandbredden under intervallet för underhåll.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple portal Underhåll

Vissa begränsad service avbrottstid krävs när CloudSimple kontrollplanet eller infrastruktur uppdateras. Underhållsfrekvens kan för närvarande kan vara så ofta som en gång per månad. Frekvensen är förväntat att avstå från att över tid. CloudSimple ger meddelanden för underhåll i portalen och ser till att intervallet som är så korta som möjligt. Följande tjänster fortsätta att fungera utan påverkas under ett portal Underhåll intervall:

* VMware Hanteringsplanet och program
* vCenter-åtkomst
* Alla nätverk och lagring
* Alla Azure-trafik

### <a name="vmware-infrastructure-maintenance"></a>VMware infrastrukturunderhåll

Ibland är det nödvändigt att göra ändringar i konfigurationen av VMware-infrastruktur.  För närvarande kan dessa intervall kan inträffa var 1 – 2 månad, men frekvensen förväntas att avstå från att över tid. Den här typen av underhåll kan vanligtvis göras utan att avbryta normal konsumtion av CloudSimple-tjänster. Följande tjänster fortsätta att fungera utan någon inverkan under ett Underhåll intervall VMware:

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

[Säkerhetskopiera virtuella datorer arbetsbelastning med Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).