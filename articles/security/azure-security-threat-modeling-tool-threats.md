---
title: Hot - Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Threat kategori-sidan för Microsoft Threat Modeling Tool, som innehåller kategorier för alla exponeras genereras hot.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: fd7c5fd929163dc7fcd22fbb045dee0fe3070394
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611561"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft Threat Modeling Tool hot

Threat Modeling Tool utgör kärnan av Microsoft Security Development Lifecycle (SDL). Det gör att programvaruarkitekter identifiera och minimera potentiella säkerhetsproblem tidigt, när de är relativt enkelt och kostnadseffektivt att lösa. Därför kan minskar det den totala kostnaden för utveckling. Dessutom kan vi utformade verktyget med icke-säkerhetsexperter i åtanke, vilket gör hotmodellering enklare för alla utvecklare genom att ange tydliga instruktioner om att skapa och analysera hotmodeller.

> Gå till den **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** och kom igång idag!

Threat Modeling Tool får du svar på vissa frågor, såsom nedan:

* Hur kan en angripare ändra autentiseringsdata?
* Hur påverkas om en angripare kan läsa data för användarprofiler?
* Vad händer om åtkomst nekas till databasen med användarprofiler?

## <a name="stride-model"></a>STRIDE modell

För bättre hjälp du formulera dessa typer av frågor som pekar, Microsoft använder STRIDE-modell som kategoriserar olika typer av hot och förenklar de övergripande säkerheten konversationerna.

| Category | Beskrivning |
| -------- | ----------- |
| **Spoofing** | Omfattar åtkomst till mjukvara och sedan använda en annan användares autentiseringsinformation, till exempel användarnamn och lösenord |
| **Tampering** | Innebär att ändringar av data. Exempel obehöriga ändringar som gjorts beständiga data som lagras i en databas och ändring av data när den förs vidare mellan två datorer öppna nätverk, till exempel Internet |
| **Repudiation** | Som är associerade med användare som neka en åtgärd utan andra parter som behöver något sätt att bevisa annars – till exempel en användare utför en otillåten åtgärd i ett system som saknar möjlighet att spåra de förbjudna åtgärderna. Non-Repudiation refererar till möjligheten för ett system för räknaren repudiation hot. När en användare köper en artikel kan till exempel behöva registrera för objektet vid mottagning. Leverantören kan sedan använda det signerade kvittot som bevis på att användaren tog emot paketet |
| **Avslöjande av information** | Omfattar exponering av information till personer som inte ska ha åtkomst till den, till exempel möjligheten för användare att läsa en fil som de inte har beviljats åtkomst till eller en inkräktare förmåga att läsa data i rörelse mellan två datorer |
| **Denial of Service** | Attacker denial of service (DoS) neka service till giltiga användare – till exempel genom att göra en server tillfälligt otillgänglig eller inte kan användas. Du måste skydda mot vissa typer av DoS-hot bara för att förbättra systemets tillgänglighet och tillförlitlighet |
| **Rättighetsökning** | En icke-privilegierade användare får privilegierad åtkomst och har därmed tillräcklig åtkomst till kompromettera eller förstöra hela systemet. Höjning av privilegier hot inkludera dessa situationer där en angripare effektivt har genombryts försvar för alla system och bli en del av betrodda själva systemet, farliga situationer verkligen |

## <a name="next-steps"></a>Nästa steg

Gå vidare till **[Threat Modeling Tool åtgärder](./azure-security-threat-modeling-tool-mitigations.md)** att lära dig hur du kan identifiera dessa hot med Azure.