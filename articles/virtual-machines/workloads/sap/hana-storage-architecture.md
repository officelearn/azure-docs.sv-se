---
title: Lagringsarkitektur för SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Lagringsarkitektur för hur du distribuerar SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a12c454906d6c6ff702b7f635a91361bbe3994c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616894"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA-lagringsarkitektur (stora instanser)

Lagringslayouten för SAP HANA på Azure (stora instanser) konfigureras av SAP HANA på den klassiska distributionsmodellen enligt rekommenderade SAP-riktlinjer. Riktlinjerna dokumenteras i [SAP HANA lagringskrav](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) vitt papper.

DEN STORA HANA-instansen av klassen Typ I levereras med fyra gånger minnesvolymen som lagringsvolym. För klassen Typ II för STORA HANA-instansenheter är lagringen inte fyra gånger mer. Enheterna levereras med en volym som är avsedd för lagring av HANA-transaktionsloggsäkerheter. Mer information finns i [Installera och konfigurera SAP HANA (stora instanser) på Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se följande tabell när det gäller lagringsallokering. Tabellen visar den grova kapaciteten för de olika volymer som medföljer de olika HANA-stora instansenheterna.

| SKU för stora instanser för HANA | hana/data | hana/logg | hana/delad | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 (S72) | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m (S72m) | 3 328 GB | 768 GB |1 280 GB | 768 GB |
| S96 (S96) | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S192 (på andra sätt) | 4 608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m (S192m) | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S192xm (S192xm) |  11 520 GB |  1 536 GB |  1 792 GB |  1 536 GB |
| S224 (S224) |  4 224 GB |  512 GB |  1 024 GB |  512 GB |
| S224m (S224m) |  8 448 GB |  512 GB |  1 024 GB |  512 GB |
| S384 (på andra sätt) | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S384m (S384m) | 12 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xm (S384xm) | 16 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xxm (S384xxm) |  20 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576m (S576m) | 20 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576xm (S576xm) | 31 744 GB | 4 096 GB | 2 048 GB | 4 096 GB |
| S768m (S768m) | 28 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S768xm (S768xm) | 40 960 GB | 6 144 GB | 4 096 GB | 6 144 GB |
| S960m (S960m) | 36 000 GB | 4 100 GB | 2 050 GB | 4 100 GB |


Faktiska distribuerade volymer kan variera beroende på distribution och det verktyg som används för att visa volymstorlekarna.

Om du delar upp en SKU för stora hana-instanser kan några exempel på möjliga divisionsdelar se ut:

| Minnespartition i GB | hana/data | hana/logg | hana/delad | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1 792 GB | 640 GB | 1 024 GB | 640 GB |
| 1,536 | 3 328 GB | 768 GB | 1 280 GB | 768 GB |


Dessa storlekar är grova volymsiffror som kan variera något beroende på distribution och de verktyg som används för att titta på volymerna. Det finns också andra partitionsstorlekar, till exempel 2,5 TB. Dessa lagringsstorlekar beräknas med en formel som liknar den som användes för de tidigare partitionerna. Termen "partitioner" betyder inte att operativsystemet, minnet eller CPU-resurser är på något sätt partitionerade. Det anger lagringspartitioner för de olika HANA-instanser som du kanske vill distribuera på en enda HANA-enhet för stor instans. 

Du kan behöva mer lagringsutrymme. Du kan lägga till lagringsutrymme genom att köpa ytterligare lagringsutrymme i 1 TB-enheter. Den här extra lagringen kan läggas till som ytterligare volym. Det kan också användas för att utöka en eller flera av de befintliga volymerna. Det går inte att minska storleken på volymerna som ursprungligen distribuerades och oftast dokumenteras av de tidigare tabellerna. Det går inte heller att ändra namnen på volymerna eller montera namn. De lagringsvolymer som tidigare beskrivits är kopplade till HANA Large Instance-enheterna som NFS4-volymer.

Du kan använda ögonblicksbilder av lagring för säkerhetskopiering och återställning och katastrofåterställning. Mer information finns i [SAP HANA (Stora instanser) hög tillgänglighet och haveriberedskap på Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se [HLI-scenarier som stöds](hana-supported-scenario.md) för information om lagringslayout för ditt scenario.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Köra flera SAP HANA-instanser på en STOR HANA-instansenhet

Det är möjligt att vara värd för mer än en aktiv SAP HANA-instans på HANA Large Instance-enheter. För att tillhandahålla funktioner för ögonblicksbilder av lagring och haveriberedskap kräver en sådan konfiguration en volymuppsättning per instans. För närvarande kan HANA Stora instansenheter delas upp på följande sätt:

- **S72, S72m, S96, S144, S192**: I steg om 256 GB, med 256 GB den minsta startenheten. Olika steg som 256 GB och 512 GB kan kombineras till maximalt minne på enheten.
- **S144m och S192m**: I steg om 256 GB, med 512 GB den minsta enheten. Olika steg som 512 GB och 768 GB kan kombineras till maximalt minne på enheten.
- **Typ II-klass**: I steg om 512 GB, med den minsta startenheten på 2 TB. Olika steg som 512 GB, 1 TB och 1,5 TB kan kombineras till maximalt minne på enheten.

Få exempel på att köra flera SAP HANA-instanser kan se ut så här.

| SKU | Minnesstorlek | Lagringsstorlek | Storlekar med flera databaser |
| --- | --- | --- | --- |
| S72 (S72) | 768 GB | 3 TB | 1x768-GB HANA-instans<br /> eller 1x512 GB-instans + 1x256 GB-instans<br /> eller 3x256 GB-instanser | 
| S72m (S72m) | 1,5 TB | 6 TB | 3x512GB HANA-instanser<br />eller 1x512 GB-instans + 1x1 TB-instans<br />eller 6x256 GB-instanser<br />eller 1x1,5 TB-instans | 
| S192m (S192m) | 4 TB | 16 TB | 8x512 GB-instanser<br />eller 4x1 TB-instanser<br />eller 4x512 GB-instanser + 2x1 TB-instanser<br />eller 4x768 GB-instanser + 2x512 GB-instanser<br />eller 1x4-TB-instans |
| S384xm (S384xm) | 8 TB | 22 TB | 4x2-TB-instanser<br />eller 2x4 TB-instanser<br />eller 2x3 TB-instanser + 1x2 TB-instanser<br />eller 2x2,5 TB-instanser + 1x3 TB-instanser<br />eller 1x8-TB-instans |


Det finns andra varianter också. 

## <a name="encryption-of-data-at-rest"></a>Kryptering av data i vila
Lagringen som används för HANA Large Instance använder transparent kryptering för data när de lagras på diskarna sedan slutet av 2018. I tidigare distributioner kan du välja att få volymerna krypterade. Om du har bestämt dig för att inte ha det alternativet kan du begära att volymerna ska krypteras online. Övergången från icke-krypterade till krypterade volymer är transparent och kräver inte driftstopp. 

Med klass typ I av SKU:er är volymen som lun-startnivån lagras på krypterad. I Revision 3 HANA Large Instance-stämplar, med hjälp av klassen Typ II för SKU:er för STORA HANA-instans, måste du kryptera start-LUN med OS-metoder. I Revision 4 HANA Stora instans stämplar, med hjälp av typ II-enheter volymen boot LUN lagras och krypteras i vila som standard också. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Nödvändiga inställningar för större HANA-instanser på STORA HANA-instanser
Lagringen som används i STORA HANA-instanser har en filstorleksbegränsning. [Storleksbegränsningen är 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) per fil. Till skillnad från i filstorleksbegränsningar i FILSYSTEMEN EXT3 är HANA inte underförstått medveten om den lagringsbegränsning som tillämpas av HANA Large Instances-lagringen. Det innebär att HANA inte automatiskt skapar en ny datafil när filstorleksgränsen på 16 TB har uppnåtts. När HANA försöker utöka filen efter 16 TB kommer HANA att rapportera fel och indexservern kraschar i slutet.

> [!IMPORTANT]
> För att förhindra att HANA försöker utöka datafiler utöver 16 TB-filstorleksgränsen för HANA-lagring av stora instanser måste du ange följande parametrar i den global.ini-konfigurationsfilen för HANA
> 
> - datavolume_striping=sant
> - datavolume_striping_size_gb = 15000
> - Se även SAP note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Var medveten om [SAP-#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Nästa steg**
- Referera [scenarier som stöds för STORA HANA-instanser](hana-supported-scenario.md)