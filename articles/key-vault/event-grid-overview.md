---
title: Övervaka Key Vault med Azure Event Grid
description: Använda Azure Event Grid för att prenumerera på Key Vault händelser
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 1fa554e03188c4d8d6227a6d2c0a560c3080b0fe
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033503"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Övervaka Key Vault med Azure Event Grid (förhands granskning)

Key Vault integration med Event Grid är för närvarande en för hands version. Det gör det möjligt för användare att meddelas när statusen för en hemlighet som lagras i Key Vault har ändrats. En status ändring definieras som en hemlighet som håller på att gå ut (inom 30 dagar efter förfallo datum), en hemlighet som har upphört att gälla eller en hemlighet med en ny version som är tillgänglig. Aviseringar för alla tre hemliga typer (nyckel, certifikat och hemlighet) stöds.

Program kan reagera på dessa händelser med hjälp av moderna serverbaserade arkitekturer, utan behov av komplicerade kod eller dyra och ineffektiva avsöknings tjänster. Händelser överförs via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till händelse hanterare som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)eller till och med din egen webhook, och du betalar bara för det du använder. Information om priser finns i [Event Grid priser](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Key Vault händelser och scheman

Event Grid använder [händelse prenumerationer](../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Key Vault händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Key Vault-händelse eftersom egenskapen eventType börjar med "Microsoft. nyckel valv".

Mer information finns i [händelse schema för Key Vault](../event-grid/event-schema-key-vault.md).

> [!NOTE]
> Händelser aktive ras endast för hemliga versioner (alla tre typer) som skapats efter att prenumerationen har angetts.
>
> För befintliga hemligheter måste du generera nya versioner.

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser

Program som hanterar Key Vault händelser bör följa några rekommenderade metoder:

* Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelse hanterare. Det är viktigt att inte anta att händelser kommer från en viss källa, men för att kontrol lera ämnet i meddelandet för att se till att det kommer från det nyckel valv som du förväntar dig.
* På samma sätt kan du kontrol lera att eventType är att du är för beredd att bearbeta och inte förutsätter att alla händelser som du tar emot är de typer som du förväntar dig.
* Ignorera fält som du inte förstår.  Den här övningen hjälper dig att hålla dig flexibel till nya funktioner som kan läggas till i framtiden.
* Använd prefixet "subject" och suffixet matchar för att begränsa händelser till en viss händelse.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Key Vault](key-vault-overview.md)
- [Översikt över Azure Event Grid](../event-grid/overview.md)
- Gör [så här: dirigera Key Vault händelser till Automation Runbook (för hands version)](event-grid-tutorial.md).
- [Azure Event Grid händelse schema för Azure Key Vault (förhands granskning)](../event-grid/event-schema-key-vault.md)
- [Översikt över Azure Automation](../automation/index.yml)
