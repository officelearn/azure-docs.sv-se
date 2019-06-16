---
title: Azure Blockchain Service stöds transaktionsregister versioner, korrigering och uppgradering
description: Översikt över stöds huvudböcker-versioner i Azure Blockchain-tjänsten, inklusive principer system uppdatering och hanteras av datorn och användarhanterade uppgraderingar.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399108"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Azure Blockchain Service ledger-versioner som stöds

Azure Blockchain Service använder Ethereum-baserade [kvorum](https://www.goquorum.com/developers) transaktionsregister som är utformat för bearbetning av privata transaktioner inom en grupp kända deltagare, identifieras som ett konsortium i Azure Blockchain-tjänsten.

Azure Blockchain-tjänsten stöder för närvarande [kvorum version 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) och [Tessera transaktionshanteraren](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar

Versionshantering i kvorum görs via en högre eller lägre version och patch släpper. Till exempel om kvorum-versionen är 2.0.1, skulle Versionstypen kategoriseras enligt följande:

|Större | Mindre  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service uppdateras automatiskt patch-versioner av kvorum för befintliga medlemmar som körs inom 30 dagar från att bli tillgänglig från kvorum.

## <a name="availability-of-new-ledger-versions"></a>Tillgängligheten för den nya transaktionsregister versioner

Azure Blockchain Service tillhandahåller de senaste högre och den lägre versionerna av kvorum redovisning inom 60 dagar efter som det är tillgängligt från tillverkaren kvorum. Högst fyra mindre versioner tillhandahålls för konsortium att välja mellan när du etablerar en ny medlem och consortium. Uppgradering från till en större eller mindre-versionen stöds inte för närvarande.

## <a name="next-steps"></a>Nästa steg

[Begränsningar i Azure Blockchain-tjänst](limits.md)
