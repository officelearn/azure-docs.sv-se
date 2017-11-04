---
title: Hot - Microsoft Threat Modeling verktyget - Azure | Microsoft Docs
description: "Hot kategorisidan för Microsoft Threat Modeling verktyget som innehåller kategorier för alla exponeras genereras hot."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 704f9995828866d4d2e4969e3aa922ed1e23c4ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft Threat Modeling verktyget hot

Verktyget Modeling hot utgör kärnan i Microsoft Security Development Lifecycle (SDL). Det gör programvaruarkitekter att identifiera och åtgärda eventuella säkerhetsfrågor tidigt, när de är relativt enkel och kostnadseffektiv att lösa. Därför kan minskar det den totala kostnaden för utveckling. Dessutom utformat vi verktyget med ej säkerhet experter ihåg förenklar hotmodellering för alla utvecklare genom att ge klara riktlinjer för att skapa och analysera hot modeller.

> Finns det  **[hot Modeling verktyget](./azure-security-threat-modeling-tool.md)**  och börja redan idag!

Verktyget Modeling hot hjälper dig att besvara vissa frågor, till exempel som nedan:

* Hur kan en angripare ändra autentiseringsdata?
* Hur påverkas om en angripare kan läsa data för användarprofiler?
* Vad händer om åtkomst nekas till databasen?

## <a name="stride-model"></a>STRIDE modellen

Bättre hjälpen du formulerar dessa typer av pekar frågor, Microsoft använder STRIDE-modell, som kategoriserar olika typer av hot och förenklar de övergripande säkerheten konversationerna.

| Kategori | Beskrivning |
| -------- | ----------- |
| **Förfalskning** | Omfattar sätt att komma åt och sedan använda en annan användares autentiseringsinformation, till exempel användarnamn och lösenord |
| **Manipulation** | Innebär att ändringar av data. Exempel obehöriga ändringar som gjorts beständiga data som lagras i en databas och ändring av data när den förs vidare mellan två datorer över öppna nätverk, till exempel Internet |
| **Repudiation** | Som är kopplade till användare och neka utför en åtgärd utan andra parter med något sätt att bevisa annars – till exempel en användare utför en otillåten åtgärd i ett system som saknar möjlighet att spåra otillåtna åtgärder. Icke-Repudiation refererar till möjligheten för ett system för räknaren repudiation hot. Till exempel behöva en användare som köper en artikel registrera för objektet har tagits emot. Leverantören kan sedan använda signerade inleveransen som bevis på att användaren tog emot paketet |
| **Avslöjande av information** | Innebär att information till personer som inte ska ha åtkomst till den exponeras, till exempel möjligheten för användare att läsa en fil som de inte har beviljats åtkomst till eller en inkräktare förmåga att läsa data som överförs mellan två datorer |
| **Denial of Service** | Denial of service (DoS) attacker neka tjänsten till giltiga användare – till exempel genom att göra en webbserver tillfälligt otillgänglig eller inte kan användas. Du måste skydda dig mot vissa typer av DoS hot bara för att förbättra systemets tillgänglighet och tillförlitlighet |
| **Rättighetsökning** | En icke-privilegierade användare får privilegierad åtkomst och har därmed behörighet att kompromettera eller förstöra hela systemet. Höjning av privilegier hot inkludera dessa situationer där en angripare effektivt har genombryts försvar för alla system och blir en del av betrodda systemet, farliga situationer verkligen |

## <a name="next-steps"></a>Nästa steg

Gå vidare till  **[hot Modeling verktyget åtgärder](./azure-security-threat-modeling-tool-mitigations.md)**  att lära dig hur man kan minimera dessa hot med Azure.