---
title: Storage-arkitektur för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Storage-arkitektur att distribuera SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 614d6aef4a2b7be551574fd3c8e25e2a3e3c1c07
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028655"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Storage-arkitektur för SAP HANA (stora instanser)

Lagringslayout för SAP HANA på Azure (stora instanser) har konfigurerats av SAP HANA på den klassiska distributionsmodellen via SAP rekommenderade riktlinjer. Riktlinjerna finns dokumenterade i den [lagringskrav för SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) white paper om.

Den stora HANA-instansen av typen jag klassen har fyra gånger minne volymen lagringsvolym. Lagringen inte fyra gånger mer för Type II-klassen för stora HANA-instansen enheter. Enheterna levereras med en volym som är avsedd för att lagra HANA säkerhetskopieringar av transaktionsloggen. Mer information finns i [installera och konfigurera SAP HANA (stora instanser) på Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se tabellen nedan när det gäller lagringsallokering. I tabell visas ungefärlig kapacitet för olika volymer som ingår i de olika enheterna för stora HANA-instansen.

| Stora HANA-instansen SKU | Hana-data | Hana/log | Hana/delat | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1 280 GB | 768 GB |
| S192 | 4 608 GB | 1 024 GB | 1,536 GB | 1 024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12 000 GB | 2,050 GB | 2,050 GB | 2 040 GB |
| S384xm | 16 000 GB | 2,050 GB | 2,050 GB | 2 040 GB |
| S384xxm |  20 000 GB | 3 100 GB | 2,050 GB | 3 100 GB |
| S576m | 20 000 GB | 3 100 GB | 2,050 GB | 3 100 GB |
| S576xm | 31,744 GB | 4 096 GB | 2 048 GB | 4 096 GB |
| S768m | 28 000 GB | 3 100 GB | 2,050 GB | 3 100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4 096 GB | 6 144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


Faktiska distribuerade volymer kan variera beroende på distributionen och verktyg som används för att visa volymer större.

Om du dela upp en HANA SKU med stor instans kan några exempel på möjliga division delar se ut:

| Minnespartition i GB | Hana-data | Hana/log | Hana/delat | Hana/log/säkerhetskopiering |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1 024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1 280 GB | 768 GB |


Dessa storlekar är grov volym-värden som kan variera något beroende på distributionen och de verktyg som används för att titta på volymerna. Det finns även andra partitionsstorlekarna, till exempel 2,5 TB. Dessa lagringsstorlekar beräknas med en formel som liknar den som används för de föregående partitionerna. Termen ”partitioner” betyder att det operativsystem, minne eller CPU-resurser på något sätt partitioneras. Den anger partitioner för lagring för de olika HANA-instanser som du vill distribuera i en enskild enhet för stora HANA-instansen. 

Du kanske behöver mer lagringsutrymme. Du kan lägga till lagring genom att köpa ytterligare lagringsutrymme i enheter om 1 TB. Den här ytterligare lagringsutrymme kan läggas till som ytterligare volym. Det kan också användas till att utöka en eller flera av de befintliga volymerna. Det är inte möjligt att minska storleken på volymerna som ursprungligen distribueras och huvudsakligen dokumenterats i föregående tabell. Det är inte heller går att ändra namnen på volymerna eller montera namn. Lagringsvolymer som beskrivs ovan är kopplade till stora HANA-instansen-enheter som NFS4 volymer.

Du kan använda ögonblicksbilder av lagring för säkerhetskopiering, återställning och haveriberedskap återställningen. Mer information finns i [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se [HLI stöds scenarier](hana-supported-scenario.md) för layout av lagringsinformation för ditt scenario.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Köra flera SAP HANA-instanser på en enhet för stora HANA-instansen

Det är möjligt att vara värd för flera aktiva SAP HANA-instansen på stora HANA-instansen enheter. För att tillhandahålla funktioner för ögonblicksbilder av lagring och katastrofåterställning, kräver en sådan konfiguration en volym som angetts per instans. För närvarande kan stora HANA-instansen enheter delas upp på följande sätt:

- **S72, S72m, S144, S192**: I steg om 256 GB, 256 GB är den minsta från enheten. Olika steg, till exempel 256 GB och 512 GB kan kombineras till maximalt minne på enheten.
- **S144m och S192m**: I steg om 256 GB, 512 GB är den minsta enheten. Olika steg som 512 GB och 768 GB kan kombineras till maximalt minne på enheten.
- **Skriv II klass**: I steg om 512 GB med den minsta från enheten på 2 TB. Olika steg som 512 GB, 1 TB och 1,5 TB kan kombineras till maximalt minne på enheten.

Några exempel på att köra flera SAP HANA-instanser kan se ut så här.

| SKU | Minnesstorlek | Lagringsstorlek | Storlekar med flera databaser |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB HANA-instans<br /> eller 1 x 512 GB instans + 1 x 256 GB-instans<br /> eller 3 x 256 GB instanser | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA-instanser<br />eller 1 x 512 GB instans + 1 x 1 TB-instans<br />eller 6 x 256 GB instanser<br />eller 1x1.5 TB-instans | 
| S192m | 4 TB | 16 TB | 8 x 512 GB instanser<br />eller 4 x 1 TB-instanser<br />eller 4 x 512 GB instanser + 2 x 1 TB-instanser<br />eller 4 x 768 GB instanser + 2 x 512 GB instanser<br />eller 1 × 4 TB-instans |
| S384xm | 8 TB | 22 TB | 4 × 2 TB-instanser<br />eller 2 × 4 TB-instanser<br />eller 2 x 3 TB-instanser + 1 x 2 TB-instanser<br />eller 2x2.5 TB-instanser + 1 x 3 TB-instanser<br />eller 1 x 8 TB-instans |


Det finns även andra varianter. 

## <a name="encryption-of-data-at-rest"></a>Kryptering av vilande data
Lagring för stora HANA-instansen kan en transparent kryptering av data som lagras på diskarna. När en enhet för stora HANA-instansen har distribuerats kan aktivera du den här typen av kryptering. Du kan också ändra till krypterade volymer efter distributionen äger rum. Övergången från icke-krypterade till krypterade volymer är transparent och kräver driftstopp. 

Med typen jag klassen av SKU: er, Start LUN lagras på volymen är krypterad. Du måste kryptera Start LUN med OS-metoder för Type II-klassen för SKU: er för stora HANA-instansen. Mer information kontaktar du Microsoft Service Management-teamet.

**Nästa steg**
- Se [scenarier som stöds för stora HANA-instanser](hana-supported-scenario.md)