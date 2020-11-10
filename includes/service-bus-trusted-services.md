---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb6f2281cc115904db6babcf31f1af387b254d48
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426576"
---
## <a name="trusted-microsoft-services"></a>Betrodda Microsoft-tjänster
När du aktiverar inställningen **Tillåt att betrodda Microsoft-tjänster kringgår den här brand Väggs** inställningen beviljas följande tjänster åtkomst till dina Service Bus-resurser.

| Betrodd tjänst | Användnings scenarier som stöds | 
| --------------- | ------------------------- | 
| Azure Event Grid | Tillåter Azure Event Grid att skicka händelser till köer eller ämnen i Service Bus namn området. Du måste också utföra följande steg: <ul><li>Aktivera systemtilldelad identitet för ett ämne eller en domän</li><li>Lägg till identiteten i rollen Azure Service Bus data avsändare i Service Bus-namnrymden</li><li>Konfigurera sedan händelse prenumerationen som använder en Service Bus kö eller ett ämne som en slut punkt för att använda den systemtilldelade identiteten.</li></ul> <p>Mer information finns i [händelse leverans med en hanterad identitet](../articles/event-grid/managed-service-identity.md)</p>|
