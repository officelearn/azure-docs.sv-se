---
title: Felsöka med fel koder för Azure Stream Analytics
description: Felsök Azure Stream Analytics problem med interna felkoder.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9dc3d873ddfef9a729f583cd914ca4700d562ff3
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045237"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Interna fel koder för Azure Stream Analytics

Du kan använda aktivitets loggar och resurs loggar för att felsöka oväntade beteenden från ditt Azure Stream Analytics-jobb. Den här artikeln innehåller en beskrivning av varje intern felkod. Interna fel är allmänna fel som uppstår i Stream Analyticss plattformen när Stream Analytics inte kan särskilja om felet är ett internt tillgänglighets fel eller en bugg i systemet.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Orsak**: den batch-storlek som används för att skriva till Cosmos DB är för stor. 
* **Rekommendation**: försök igen med en mindre batchstorlek.

## <a name="next-steps"></a>Nästa steg

* [Felsöka indataanslutningar](stream-analytics-troubleshoot-input.md)
* [Felsöka Azure Stream Analytics utdata](stream-analytics-troubleshoot-output.md)
* [Felsöka Azure Stream Analytics frågor](stream-analytics-troubleshoot-query.md)
* [Felsöka Azure Stream Analytics med hjälp av resurs loggar](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics data fel](data-errors.md)