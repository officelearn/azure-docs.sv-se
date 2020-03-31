---
title: Svarstid i Blob-lagring - Azure Storage
description: Förstå och mät svarstid för Blob-lagringsåtgärder och lär dig hur du utformar dina Blob-lagringsprogram för låg latens.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75749722"
---
# <a name="latency-in-blob-storage"></a>Latens i Blobblagring

Svarstid, ibland refererad som svarstid, är den tid som ett program måste vänta på att en begäran ska slutföras. Svarstid kan direkt påverka ett programs prestanda. Låg latens är ofta viktigt för scenarier med människor i loopen, till exempel att utföra kreditkortstransaktioner eller läsa in webbsidor. System som behöver bearbeta inkommande händelser med hög frekvens, till exempel telemetriloggning eller IoT-händelser, kräver också låg latens. I den här artikeln beskrivs hur du förstår och mäter svarstid för åtgärder på blockblobar och hur du utformar dina program för låg svarstid.

Azure Storage erbjuder två olika prestandaalternativ för blockblobar: premium och standard. Premium block blobbar erbjuder betydligt lägre och mer konsekvent svarstid än standard block blobbar via högpresterande SSD-diskar. Mer information finns i **Premium prestandablock blob storage** i [Azure Blob storage: hot, cool och archive access tiers](storage-blob-storage-tiers.md).

## <a name="about-azure-storage-latency"></a>Om Azure Storage-svarstid

Azure Storage-svarstid är relaterad till begäranden för Azure Storage-åtgärder. Begärandens priser kallas även indata-/utdataåtgärder per sekund (IOPS).

Om du vill beräkna begäranden bestämmer du först hur lång tid varje begäran tar att slutföra och beräknar sedan hur många begäranden som kan bearbetas per sekund. Anta till exempel att en begäran tar 50 millisekunder (ms) att slutföra. Ett program som använder en tråd med en enastående läs- eller skrivåtgärd bör uppnå 20 IOPS (1 sekund eller 1000 ms / 50 ms per begäran). Teoretiskt, om trådantalet fördubblas till två, bör programmet kunna uppnå 40 IOPS. Om de utestående asynkrona läs- eller skrivåtgärderna för varje tråd fördubblas till två, bör programmet kunna uppnå 80 IOPS.

I praktiken skalas inte alltid begärandehastigheter så linjärt, på grund av omkostnader i klienten från schemaläggning av aktiviteter, kontextväxling och så vidare. På tjänstsidan kan det finnas variationer i svarstid på grund av tryck på Azure Storage-systemet, skillnader i lagringsmedia som används, brus från andra arbetsbelastningar, underhållsuppgifter och andra faktorer. Slutligen kan nätverksanslutningen mellan klienten och servern påverka Azure Storage-svarstiden på grund av överbelastning, omdirigering eller andra avbrott.

Azure Storage-bandbredd, även kallad dataflöde, är relaterad till begäranden och kan beräknas genom att multiplicera begäranden (IOPS) med begäranstorleken. Om man till exempel antar 160 begäranden per sekund resulterar varje 256 KiB data i dataflöde på 40 960 KiB per sekund eller 40 MiB per sekund.

## <a name="latency-metrics-for-block-blobs"></a>Svarstidsmått för blockblobar

Azure Storage innehåller två svarstidsmått för blockblobar. Dessa mått kan visas i Azure-portalen:

- **Svarstiden (End-to-end)** mäter intervallet från när Azure Storage tar emot det första paketet med begäran tills Azure Storage tar emot en klientbekräftelse på det sista paketet med svaret.

- **Serverfördröjningen** mäter intervallet från när Azure Storage tar emot det sista paketet av begäran tills det första paketet med svaret returneras från Azure Storage.

Följande bild visar **svarstiden för E2E genomsnittlig framgång** och Server **latens** för genomsnittlig framgång för en exempelarbetsbelastning som anropar åtgärden: `Get Blob`

![Skärmbild som visar svarstidsmått för åtgärden Hämta blob](media/storage-blobs-latency/latency-metrics-get-blob.png)

Under normala förhållanden finns det litet mellanrum mellan svarstid från till på sluten tid och serverfördröjning, vilket är vad bilden visar för exempelarbetsbelastningen.

Om du granskar svarmetri från på sluten tid och server och upptäcker att svarstiden från slutna till slutna dagar är betydligt högre än serverns svarstid, undersöker och adresserar du källan till den ytterligare svarstiden.

Om svarstiden från slutna till slutna och servrar är liknande, men du behöver lägre svarstid, överväg sedan att migrera till premiumblockbloloblagring.

## <a name="factors-influencing-latency"></a>Faktorer som påverkar latens

Den viktigaste faktorn som påverkar svarstiden är operationsstorlek. Det tar längre tid att slutföra större åtgärder, på grund av mängden data som överförs över nätverket och bearbetas av Azure Storage.

Följande diagram visar den totala tiden för åtgärder av olika storlekar. För små mängder data används svarstidsintervallet huvudsakligen med att hantera begäran i stället för att överföra data. Svarstidsintervallet ökar bara något när driftstorleken ökar (markerad 1 i diagrammet nedan). När operationsstorleken ökar ytterligare, spenderas mer tid på att överföra data, så att det totala svarstidsintervallet delas mellan hantering av begäran och dataöverföring (markerad 2 i diagrammet nedan). Med större driftstorlekar spenderas svarstidsintervallet nästan uteslutande på att överföra data och begäranheten är till stor del obetydlig (markerad 3 i diagrammet nedan).

![Skärmbild som visar total drifttid efter operationsstorlek](media/storage-blobs-latency/operation-time-size-chart.png)

Klientkonfigurationsfaktorer som samtidighet och trådning påverkar också svarstiden. Övergripande dataflöde beror på hur många lagringsbegäranden som finns under flygning vid en viss tidpunkt och på hur ditt program hanterar gängning. Klientresurser, inklusive CPU, minne, lokal lagring och nätverksgränssnitt, kan också påverka svarstiden.

Bearbetning av Azure Storage-begäranden kräver klient-CPU och minnesresurser. Om klienten är under press på grund av en underpowered virtuell dator eller någon runaway process i systemet, det finns färre resurser tillgängliga för att bearbeta Azure Storage-begäranden. Alla konkurrens- eller brist på klientresurser leder till en ökning av svarstiden från på sluten tid utan en ökning av serverns svarstid, vilket ökar gapet mellan de två måtten.

Lika viktigt är nätverksgränssnittet och nätverksröret mellan klienten och Azure Storage. Fysiskt avstånd ensam kan vara en viktig faktor, till exempel om en klient-VM är i en annan Azure-region eller lokalt. Andra faktorer som nätverkshopp, Internet-routning och internettillstånd kan påverka den totala lagringsfördröjningen.

För att bedöma svarstiden upprättar du först baslinjemått för ditt scenario. Baslinjemått ger dig den förväntade svarstiden från på slut- och server i samband med programmiljön, beroende på din arbetsbelastningsprofil, programkonfigurationsinställningar, klientresurser, nätverksrör och andra faktorer. När du har baslinjemått kan du lättare identifiera onormala kontra normala förhållanden. Baslinjemått gör det också möjligt att observera effekterna av ändrade parametrar, till exempel programkonfiguration eller VM-storlekar.

## <a name="next-steps"></a>Nästa steg

- [Skalbarhets- och prestandamål för Blob-lagring](scalability-targets.md)
- [Checklista för prestanda och skalbarhet för Blob-lagring](storage-performance-checklist.md)
