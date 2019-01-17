---
title: Åtgärder – Microsoft Hotmodelleringsverktyg – Azure | Microsoft Docs
description: Åtgärder-sidan för den Microsoft Threat Modeling Tool markering möjliga lösningar på de mest exponerade genereras hot.
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
ms.openlocfilehash: 24aa49fd4ccccda372d2632ef4aee22bd5cb2bf6
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359144"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft Threat Modeling Tool åtgärder

Threat Modeling Tool utgör kärnan av Microsoft Security Development Lifecycle (SDL). Det gör att programvaruarkitekter identifiera och minimera potentiella säkerhetsproblem tidigt, när de är relativt enkelt och kostnadseffektivt att lösa. Därför kan minskar det den totala kostnaden för utveckling. Dessutom kan vi utformade verktyget med icke-säkerhetsexperter i åtanke, vilket gör hotmodellering enklare för alla utvecklare genom att ange tydliga instruktioner om att skapa och analysera hotmodeller.

Gå till den **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** och kom igång idag!

## <a name="mitigation-categories"></a>Minskning kategorier

Threat Modeling Tool åtgärder kategoriseras enligt Web Application Security tidsperiod, som består av följande:

| Kategori | Beskrivning |
| -------- | ----------- |
| **[Granskning och loggning](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Vem gjorde vad och när? Granskning och loggning refererar till hur ditt program registrerar säkerhetsrelaterade händelser |
| **[Autentisering](./azure-security-threat-modeling-tool-authentication.md)** | Vem är du? Autentisering är en process där en entitet bevisar identiteten för en annan entitet, vanligtvis via autentiseringsuppgifter, till exempel ett användarnamn och lösenord |
| **[Auktorisering](./azure-security-threat-modeling-tool-authorization.md)** | Vad kan du göra? Auktoriseringen är hur ditt program får åtkomstkontroller för resurser och åtgärder |
| **[KOMMUNIKATIONSSÄKERHET](./azure-security-threat-modeling-tool-communication-security.md)** | Vem som pratar med? KOMMUNIKATIONSSÄKERHET säkerställer all kommunikation som är klar är så skyddat som möjligt |
| **[Configuration Management](./azure-security-threat-modeling-tool-configuration-management.md)** | Som körs programmet som? Vilka databaser ansluter den till? Hur styrs ditt program? Hur skyddas de här inställningarna? Konfigurationshantering avser hur programmet hanterar dessa operativa problem |
| **[Kryptografi](./azure-security-threat-modeling-tool-cryptography.md)** | Hur behåller du hemligheter (sekretess)? Hur är det förvanskningstålig språkverktyg data eller bibliotek (integritet)? Hur tillhandahåller du frö för slumpmässiga värden som måste vara kryptografiskt starkt? Kryptografi avser hur programmet tillämpar sekretess och integritet |
| **[Hantering av undantag](./azure-security-threat-modeling-tool-exception-management.md)** | När ett metodanrop i ditt program inte vad gör ditt program? Hur mycket du avslöja? Du returnera ett information till slutanvändare? Du skickar värdefulla undantagsinformation tillbaka till anroparen? Misslyckas ditt program utan problem? |
| **[Verifiering av indata](./azure-security-threat-modeling-tool-input-validation.md)** | Hur vet du att de indata som programmet tar emot är giltig och säkra? Verifiering av indata syftar på hur ditt program filtrerar buskmarker eller avvisar indata innan ytterligare bearbetning. Överväg att begränsa indata via startpunkter och kodning utdata via avsluta punkter. Litar du data från källor som databaser och filresurser? |
| **[Känsliga Data](./azure-security-threat-modeling-tool-sensitive-data.md)** | Hur hanterar känsliga data i ditt program? Känsliga data avser hur programmet hanterar alla data som måste skyddas i minnet, över nätverket, eller i beständiga butiker |
| **[Sessionshantering](./azure-security-threat-modeling-tool-session-management.md)** | Hur gör ditt program hantera och skydda användarsessioner? En session som refererar till ett antal relaterade interaktioner mellan en användare och ditt webbprogram |

Detta hjälper dig att identifiera:

* Där är de vanligaste ibland blanda
* Var finns de mest användbara förbättringarna

Därför kan du med dessa kategorier att fokusera och prioritera ditt arbete med säkerhet, så att om du vet vanligaste säkerhetsproblem uppstå i indata-verifiering, autentisering och auktorisering kategorier, du kan starta det. Mer information finns  **[patent länken](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Nästa steg

Besök **[Threat Modeling Tool hot](./azure-security-threat-modeling-tool-threats.md)** mer information om hotet kategorierna verktyget använder för att generera möjliga design hot.