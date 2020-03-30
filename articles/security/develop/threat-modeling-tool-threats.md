---
title: Hot - Microsoft Threat Modeling Tool - Azure | Microsoft-dokument
description: Sidan Hotkategori för Microsoft Threat Modeling Tool, som innehåller kategorier för alla exponerade genererade hot.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727834"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Hot mot Microsoft threat modeling tool

Hotmodelleringsverktyget är en central del av Microsoft Security Development Lifecycle (SDL). Det gör det möjligt för programvaruarkitekter att identifiera och minska potentiella säkerhetsproblem tidigt, när de är relativt enkla och kostnadseffektiva att lösa. Som ett resultat minskar det kraftigt den totala kostnaden för utveckling. Dessutom har vi utformat verktyget med icke-säkerhetsexperter i åtanke, vilket gör hotmodellering enklare för alla utvecklare genom att ge tydlig vägledning om hur du skapar och analyserar hotmodeller.

> Besök **[threat modeling tool](threat-modeling-tool.md)** för att komma igång idag!

Hot Modeling Tool hjälper dig att svara på vissa frågor, till exempel de nedan:

* Hur kan en angripare ändra autentiseringsdata?
* Vad är effekten om en angripare kan läsa användarprofildata?
* Vad händer om åtkomst nekas till användarprofildatabasen?

## <a name="stride-model"></a>STRIDE-modell

För att bättre hjälpa dig att formulera den här typen av spetsiga frågor använder Microsoft STRIDE-modellen, som kategoriserar olika typer av hot och förenklar de övergripande säkerhetskonversationerna.

| Kategori | Beskrivning |
| -------- | ----------- |
| **Förfalskning** | Innebär att olaglig åtkomst och sedan använda en annan användares autentiseringsinformation, till exempel användarnamn och lösenord |
| **Manipulering** | Innebär skadlig ändring av data. Exempel på detta är obehöriga ändringar som gjorts i beständiga data, till exempel de som finns i en databas, och ändring av data när den flödar mellan två datorer över ett öppet nätverk, till exempel Internet |
| **Avståndstagande** | Associerad med användare som förnekar att utföra en åtgärd utan att andra parter har något sätt att bevisa något annat, till exempel utför en användare en olaglig åtgärd i ett system som saknar möjlighet att spåra de förbjudna åtgärderna. Icke-avståndstagande hänvisar till förmågan hos ett system för att motverka avståndstagande hot. En användare som köper en artikel kan till exempel behöva signera för artikeln vid inleveransen. Leverantören kan sedan använda det signerade kvittot som bevis på att användaren har fått paketet |
| **Utlämnande av information** | Innebär exponering av information för personer som inte ska ha tillgång till den, till exempel användarnas möjlighet att läsa en fil som de inte har beviljats tillgång till, eller en inkräktares förmåga att läsa data under överföring mellan två datorer |
| **Överbelastning** | DoS-attacker (Denial of Service) nekar tjänsten till giltiga användare, till exempel genom att göra en webbserver tillfälligt otillgänglig eller oanvändbar. Du måste skydda dig mot vissa typer av DoS-hot bara för att förbättra systemets tillgänglighet och tillförlitlighet |
| **Behörighetshöjning** | En oprivilegierad användare får privilegierad åtkomst och har därmed tillräcklig tillgång till kompromisser eller förstör hela systemet. Höjd av privilegiehot inkluderar de situationer där en angripare effektivt har trängt in i alla systemförsvar och blivit en del av det betrodda systemet självt, en farlig situation faktiskt |

## <a name="next-steps"></a>Nästa steg

Fortsätt till **[hotmodelleringsverktygsbegränsningar](threat-modeling-tool-mitigations.md)** för att lära dig olika sätt att minska dessa hot med Azure.