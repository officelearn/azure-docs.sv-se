---
title: Övervaka nyckelvalv med Azure Event Grid
description: Använda Azure Event Grid för att prenumerera på Key Vault-händelser
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: cc12cc9a4828404e960aee239bd388af5b1ea3b7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431909"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Övervaka nyckelvalv med Azure Event Grid (förhandsversion)

Key Vault-integrering med Event Grid förhandsgranskas för närvarande. Det gör att användare kan meddelas när statusen för en hemlighet som lagras i nyckelvalvet har ändrats. En statusändring definieras som en hemlighet som håller på att upphöra att gälla (inom 30 dagar efter förfallodatumet), en hemlighet som har upphört att gälla eller en hemlighet som har en ny version tillgänglig. Meddelanden för alla tre hemliga typer (nyckel, certifikat och hemlighet) stöds.

Program kan reagera på dessa händelser med hjälp av moderna serverlösa arkitekturer, utan att behöva komplicerad kod eller dyra och ineffektiva avsökningstjänster. Händelser trycks via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till händelsehanterare som Azure [Functions,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)eller till och med till din egen Webhook, och du betalar bara för det du använder. Information om priser finns i [Prissättning för Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Viktiga händelser och scheman för Nyckelvalv

Event grid använder [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) för att dirigera händelsemeddelanden till prenumeranter. Key Vault-händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Key Vault-händelse eftersom eventType-egenskapen börjar med "Microsoft.KeyVault".

Mer information finns i [händelseschemat för Key Vault](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Meddelandehändelser utlöses endast på nya versioner av hemligheter, nycklar och certifikat, och du måste först prenumerera på händelsen i nyckelvalvet för att få dessa meddelanden.
> 
> Du får endast meddelandehändelser på certifikat när certifikatet förnyas automatiskt enligt den princip du har angett för ditt certifikat.

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser

Program som hanterar Key Vault-händelser bör följa några rekommenderade metoder:

* Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare. Det är viktigt att inte anta att händelser kommer från en viss källa, men för att kontrollera ämnet för meddelandet för att säkerställa att det kommer från nyckelvalvet du förväntar dig.
* På samma sätt kontrollerar du att eventType är en som du är beredd att bearbeta och förutsätter inte att alla händelser du får kommer att vara de typer du förväntar dig.
* Ignorera fält som du inte förstår.  Den här metoden hjälper dig att hålla dig motståndskraftig mot nya funktioner som kan läggas till i framtiden.
* Använd prefixet "subject" och suffixmatchningar för att begränsa händelser till en viss händelse.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Key Vault](overview.md))
- [Översikt för Azure Event Grid](../../event-grid/overview.md)
- Så [här: Dirigera nyckelvalvhändelser till Automation Runbook (förhandsgranskning)](event-grid-tutorial.md).
- Så här får du [e-post när en nyckelvalvshemlighet ändras](event-grid-logicapps.md)
- [Azure Event Grid-händelseschema för Azure Key Vault (förhandsversion)](../../event-grid/event-schema-key-vault.md)
- [Översikt över Azure Automation](../../automation/index.yml)
