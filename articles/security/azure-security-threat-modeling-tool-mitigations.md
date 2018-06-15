---
title: Åtgärder – Microsoft Threat modellering verktyget - Azure | Microsoft Docs
description: Åtgärder sidan för Microsoft Threat Modeling verktyget syntaxmarkering möjliga lösningar på det mest exponerade genereras hot.
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
ms.openlocfilehash: 07ef1fd3d81d795c9164741d22b5a689f86bd720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23867985"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft Threat Modeling verktyget åtgärder

Verktyget Modeling hot utgör kärnan i Microsoft Security Development Lifecycle (SDL). Det gör programvaruarkitekter att identifiera och åtgärda eventuella säkerhetsfrågor tidigt, när de är relativt enkel och kostnadseffektiv att lösa. Därför kan minskar det den totala kostnaden för utveckling. Dessutom utformat vi verktyget med ej säkerhet experter ihåg förenklar hotmodellering för alla utvecklare genom att ge klara riktlinjer för att skapa och analysera hot modeller.

Finns det  **[hot Modeling verktyget](./azure-security-threat-modeling-tool.md)**  och börja redan idag!

## <a name="mitigation-categories"></a>Minskning kategorier

Hot Modeling verktyget åtgärder kategoriseras enligt Web Application säkerhet ram, som består av följande:

| Kategori | Beskrivning |
| -------- | ----------- |
| **[Granskning och loggning](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Vem som gjorde vad och när? Granskning och loggning refererar till hur säkerhetshändelser poster i ditt program |
| **[Autentisering](./azure-security-threat-modeling-tool-authentication.md)** | Vem är du? Autentisering är den process där en entitet bevisar identiteten för en annan entitet vanligtvis via autentiseringsuppgifter, till exempel användarnamn och lösenord |
| **[Auktorisering](./azure-security-threat-modeling-tool-authorization.md)** | Vad kan du göra? Auktorisering är hur programmet ger åtkomstkontroll för resurser och åtgärder |
| **[KOMMUNIKATIONSSÄKERHET](./azure-security-threat-modeling-tool-communication-security.md)** | Som du talar till? KOMMUNIKATIONSSÄKERHET garanterar all kommunikation klar är så säkert som möjligt |
| **[Konfigurationshantering](./azure-security-threat-modeling-tool-configuration-management.md)** | Som körs programmet som? Vilka databaser ansluter den till? Hur styrs ditt program? Hur skyddas de här inställningarna? Konfigurationshantering refererar till hur programmet hanterar dessa operativa problem |
| **[Kryptografi](./azure-security-threat-modeling-tool-cryptography.md)** | Hur behåller du hemligheter (sekretess)? Hur ska du nyckellagring språkverktyg data eller bibliotek (integritet)? Hur tillhandahåller du frö för slumpmässiga värden som måste vara kryptografiskt starka? Kryptografi refererar till hur programmet tillämpar sekretess och integritet |
| **[Hantering av undantag](./azure-security-threat-modeling-tool-exception-management.md)** | När ett metodanrop i ditt program inte vad är ditt program? Hur mycket du avslöja? Du returnera eget felinformation till slutanvändare? Du skickar värdefulla undantagsinformation tillbaka till anroparen? Misslyckas programmet avslutas? |
| **[Verifiering av indata](./azure-security-threat-modeling-tool-input-validation.md)** | Hur vet du att ditt program tar emot indata är giltigt och säkert? Verifiering av indata syftar på hur programmet filtrerar buskmarker eller avvisar indata innan ytterligare bearbetning. Överväg att begränsa indata via startpunkter och kodning utdata via avsluta punkter. Litar du data från källor som databaser och filresurser? |
| **[Känsliga Data](./azure-security-threat-modeling-tool-sensitive-data.md)** | Hur hanterar programmet känsliga data? Känsliga data refererar till hur programmet hanterar alla data som måste skyddas i minnet, över nätverket, eller i beständiga lagrar |
| **[Sessionshantering](./azure-security-threat-modeling-tool-session-management.md)** | Hur ditt program hantera och skydda användarsessioner? En session som refererar till en uppsättning relaterade samverkan mellan användare och ditt webbprogram |

Det hjälper dig att identifiera:

* Där görs de vanligaste fel
* Var finns de mest tillämplig förbättringarna

Därför kan du använder dessa kategorier för fokus och prioritera ditt arbete för säkerhet, så att om du vet att de vanligaste säkerhetsproblem uppstå i inkommande verifiering, autentisering och auktorisering kategorier, kan du starta det. Mer information finns  **[patent länken](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Nästa steg

Besök  **[hot Modeling verktyget hot](./azure-security-threat-modeling-tool-threats.md)**  för mer information om hot kategorier verktyget används för att generera möjliga design hot.