---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74279186"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definitioner för storlekstabellen

- Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1000^3 byte) med diskar som mäts i GiB (1024^3) kom ihåg att kapacitetssiffror som anges i GiB kan se mindre ut. Till exempel 1023 GiB = 1098,4 GB.
- Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
- Datadiskar kan köras i cachelagrat eller icke cachelagrat läge. För diskåtgärder med cachelagrade data anges cacheläget till **ReadOnly** eller **ReadWrite**.  För diskåtgärder med icke cachelagrade data anges cacheläget till **Inget**.
- Om du vill få bästa prestanda för dina virtuella datorer bör du begränsa antalet datadiskar till två diskar per vCPU.
- **Förväntad nätverksbandbredd** är den maximala aggregerade bandbredden som allokeras per vm-typ för alla nätverkskort för alla destinationer. Mer information finns i [Bandbredd för virtuell datornätverk](../articles/virtual-network/virtual-machine-network-throughput.md).

  Övre gränser är inte garanterade. Gränser ger vägledning för att välja rätt VM-typ för det avsedda programmet. Den faktiska nätverksprestandan beror på flera faktorer, bland annat överbelastning av nätverk, programbelastningar och nätverksinställningar. Information om hur du optimerar nätverksdataflödet finns i [Optimera nätverksdataflödet för virtuella Azure-datorer](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). För att uppnå den förväntade nätverksprestandan på Linux eller Windows kan du behöva välja en viss version eller optimera din virtuella dator. Mer information finns i [NTTTCP (Bandwidth/Throughput testing)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



