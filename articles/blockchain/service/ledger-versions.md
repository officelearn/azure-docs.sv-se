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
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027892"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Azure Blockchain Service ledger-versioner som stöds

Azure Blockchain Service använder Ethereum-baserade [kvorum](https://github.com/jpmorganchase/quorum/wiki) transaktionsregister som är utformat för bearbetning av privata transaktioner inom en grupp kända deltagare, identifieras som ett konsortium i Azure Blockchain-tjänsten.

Azure Blockchain-tjänsten stöder för närvarande [kvorum version 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) och [Tessera transaktionshanteraren](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar

Versionshantering i kvorum görs via en huvudversion, större punkt version och en mindre point-version. Till exempel om kvorum version 2.0.1, 2 är en huvudversion, 0 är en större punkt-version och 1 är mindre punkt versionen. Tjänsten korrigeringar automatiskt mindre punkt versioner av huvudboken. För närvarande stöds inte uppgradering större och större versioner.

## <a name="availability-of-new-ledger-versions"></a>Tillgängligheten för den nya transaktionsregister versioner

Azure Blockchain Service tillhandahåller de senaste versionerna av redovisningen inom 60 dagar efter som det är tillgängligt från tillverkaren huvudboken. Högst fyra större punkt versioner tillhandahålls för konsortium att välja mellan när du etablerar en ny medlem och consortium.

## <a name="next-steps"></a>Nästa steg

[Begränsningar i Azure Blockchain-tjänst](limits.md)
