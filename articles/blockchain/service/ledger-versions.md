---
title: Azure Blockchain Service redovisningsversioner, korrigering, & uppgradering
description: Översikt över de redovisningsversioner som stöds i Azure Blockchain Service, inklusive principer för systemkorrigering och systemhanterade och användarhanterade uppgraderingar.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325192"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Azure Blockchain Service-redovisningsversioner som stöds

Azure Blockchain Service använder den Ethereum-baserade [kvorumbegeboken](https://www.goquorum.com/developers) som utformats för bearbetning av privata transaktioner inom en grupp kända deltagare, identifierade som ett konsortium i Azure Blockchain Service.

Azure Blockchain-tjänsten stöder för närvarande [kvorumversionen 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) och [Tessera-transaktionshanteraren](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar

Versionshantering i kvorum sker genom en större, mindre och patch utgåvor. Om kvorumversionen till exempel är 2.0.1 kategoriseras versionstyp på följande sätt:

|Större | Mindre  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service uppdaterar automatiskt korrigeringsversioner av Quorum till befintliga medlemmar som körs inom 30 dagar efter att de gjorts tillgängliga från Quorum.

## <a name="availability-of-new-ledger-versions"></a>Tillgänglighet för nya redovisningsversioner

Azure Blockchain Service tillhandahåller de senaste huvud- och delversionerna av kvorumredovisningen inom 60 dagar efter att den har varit tillgänglig från kvorumtillverkaren. Högst fyra mindre utgåvor ges till konsortier att välja mellan när de etablerar en ny medlem och ett nytt konsortium. Uppgradering från till en större eller mindre version stöds för närvarande inte. Om du till exempel kör version 2.x stöds för närvarande inte en uppgradering till version 3.x. Om du kör version 2.2 stöds för närvarande inte en uppgradering till version 2.3.

## <a name="next-steps"></a>Nästa steg

[Begränsningar i Azure Blockchain-tjänsten](limits.md)
