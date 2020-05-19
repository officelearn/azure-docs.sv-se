---
title: Felsöka med fel koder för Azure Stream Analytics
description: Felsök Azure Stream Analytics problem med interna felkoder.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c8c792d548fc953cb5d059f322ad1f3139c91562
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597739"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Interna fel koder för Azure Stream Analytics

Du kan använda aktivitets loggar och resurs loggar för att felsöka oväntade beteenden från ditt Azure Stream Analytics-jobb. Den här artikeln innehåller en beskrivning av varje intern felkod. Interna fel är allmänna fel som uppstår i Stream Analyticss plattformen när Stream Analytics inte kan särskilja om felet är ett internt tillgänglighets fel eller en bugg i systemet.

## <a name="kafkainvalidrequest"></a>KafkaInvalidRequest

* **Orsak**: begäran som skickades till Kafka-servern är ogiltig.

## <a name="kafkainputerror"></a>KafkaInputError

* **Orsak**: Kafka-indatamängden påträffade ett problem.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Orsak**: den batch-storlek som används för att skriva till Cosmos DB är för stor. 
* **Rekommendation**: försök igen med en mindre batchstorlek.

## <a name="next-steps"></a>Nästa steg

* [Felsöka indataanslutningar](stream-analytics-troubleshoot-input.md)
* [Felsöka Azure Stream Analytics utdata](stream-analytics-troubleshoot-output.md)
* [Felsöka Azure Stream Analytics frågor](stream-analytics-troubleshoot-query.md)
* [Felsöka Azure Stream Analytics med hjälp av resurs loggar](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics data fel](data-errors.md)