---
title: Azure blockchain service-versioner, uppdatering och uppgradering
description: Översikt över de redovisnings versioner som stöds i Azure blockchain-tjänsten, inklusive principer för system korrigeringar och systemhanterade uppgraderingar och användar hanterade uppgraderingar.
services: azure-blockchain
keywords: Blockchain
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 40719f1c353b8961e14815cb61e7a862b47fc6f5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329122"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versioner som stöds av Azure blockchain service-redovisning

I Azure blockchain service används den Ethereum-baserade [kvorumkonfigurationen](https://www.goquorum.com/developers) för bearbetning av privata transaktioner i en grupp kända deltagare som identifieras som konsortier i Azure blockchain-tjänsten.

För närvarande stöder Azure blockchain-tjänsten [kvorumresursens version 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) och [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar

Versions hantering i kvorum görs via en större version, dels och korrigerings version. Om kvorumloggen till exempel är 2.0.1, kommer versions typen att kategoriseras på följande sätt:

|Major | Del  | 9\.0a  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure blockchain-tjänsten uppdaterar automatiskt korrigerings versioner av kvorum till befintliga aktiva medlemmar inom 30 dagar från det att de har gjorts tillgängliga från kvorum.

## <a name="availability-of-new-ledger-versions"></a>Tillgänglighet för nya redovisnings versioner

Azure blockchain-tjänsten tillhandahåller de senaste högre och lägre versionerna av kvorum-redovisningen inom 60 dagar som är tillgängliga från kvorumdisken. Det finns högst fyra mindre versioner av konsortier som du kan välja mellan när du konfigurerar en ny medlem och konsortiet. Det finns för närvarande inte stöd för att uppgradera från till en större eller mindre version. Om du till exempel kör version 2. x stöds inte en uppgradering till version 3. x för närvarande. Om du kör version 2,2 på samma sätt stöds inte en uppgradering till version 2,3 för närvarande.

## <a name="next-steps"></a>Nästa steg

[Gränser i Azure blockchain-tjänsten](limits.md)
