---
title: Mildrande åtgärder – Microsofts verktyg för hotmodellering – Azure | Microsoft-dokument
description: Sidan Mildrande åtgärder för Microsoft Threat Modeling Tool som belyser möjliga lösningar på de mest utsatta genererade hoten.
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
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728032"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsofts begränsning av hotmodelleringsverktyg

Hotmodelleringsverktyget är en central del av Microsoft Security Development Lifecycle (SDL). Det gör det möjligt för programvaruarkitekter att identifiera och minska potentiella säkerhetsproblem tidigt, när de är relativt enkla och kostnadseffektiva att lösa. Som ett resultat minskar det kraftigt den totala kostnaden för utveckling. Dessutom har vi utformat verktyget med icke-säkerhetsexperter i åtanke, vilket gör hotmodellering enklare för alla utvecklare genom att ge tydlig vägledning om hur du skapar och analyserar hotmodeller.

Besök **[threat modeling tool](threat-modeling-tool.md)** för att komma igång idag!

## <a name="mitigation-categories"></a>Begränsningskategorier

Begränsningen av hotmodelleringsverktyget kategoriseras enligt säkerhetsramen för webbprogram, som består av följande:

| Kategori | Beskrivning |
| -------- | ----------- |
| **[Granskning och loggning](threat-modeling-tool-auditing-and-logging.md)** | Vem gjorde vad och när? Granskning och loggning avser hur ditt program registrerar säkerhetsrelaterade händelser |
| **[Autentisering](threat-modeling-tool-authentication.md)** | Vem är du? Autentisering är den process där en entitet bevisar identiteten på en annan entitet, vanligtvis genom autentiseringsuppgifter, till exempel ett användarnamn och lösenord |
| **[Tillstånd](threat-modeling-tool-authorization.md)** | Vad kan du göra? Auktorisering är hur ditt program tillhandahåller åtkomstkontroller för resurser och åtgärder |
| **[Kommunikationssäkerhet](threat-modeling-tool-communication-security.md)** | Vem pratar du med? Kommunikationssäkerhet säkerställer att all kommunikation som görs är så säker som möjligt |
| **[Konfigurationshantering](threat-modeling-tool-configuration-management.md)** | Vem kör din ansökan som? Vilka databaser ansluter den till? Hur administreras din ansökan? Hur är dessa inställningar säkrade? Konfigurationshantering refererar till hur ditt program hanterar dessa driftproblem |
| **[Kryptografi](threat-modeling-tool-cryptography.md)** | Hur håller du hemligheter (sekretess)? Hur manipulerar du dina data eller bibliotek (integritet)? Hur ger du frön för slumpmässiga värden som måste vara kryptografiskt starka? Kryptografi avser hur ditt program upprätthåller sekretess och integritet |
| **[Undantagshantering](threat-modeling-tool-exception-management.md)** | När ett metodanrop i ditt program misslyckas, vad gör ditt program? Hur mycket avslöjar du? Returnerar du vänlig felinformation till slutanvändare? Skickar du värdefull undantagsinformation tillbaka till den som ringer? Misslyckas din ansökan graciöst? |
| **[Validering av indata](threat-modeling-tool-input-validation.md)** | Hur vet du att ingången som din ansökan tar emot är giltig och säker? Med indatavalidering avses hur programmet filtrerar, skrubbar eller avvisar indata före ytterligare bearbetning. Överväg att begränsa indata genom startpunkter och koda utdata via utförselställen. Litar du på data från källor som databaser och filresurser? |
| **[Känsliga data](threat-modeling-tool-sensitive-data.md)** | Hur hanterar ditt program känsliga data? Känsliga data avser hur ditt program hanterar data som måste skyddas antingen i minnet, över nätverket eller i beständiga butiker |
| **[Hantering av sessionssessioner](threat-modeling-tool-session-management.md)** | Hur hanterar och skyddar programmet användarsessioner? En session refererar till en serie relaterade interaktioner mellan en användare och ditt webbprogram |

Detta hjälper dig att identifiera:

* Var är de vanligaste misstagen som
* Var är de mest genomförbara förbättringarna

Därför använder du dessa kategorier för att fokusera och prioritera ditt säkerhetsarbete, så att om du vet att de vanligaste säkerhetsproblemen uppstår i kategorierna indataverifiering, autentisering och auktorisering, kan du börja där. För mer information besök ** [denna patentlänk](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Nästa steg

Besök **[hot mot hot mot hot mot hot mot hot](threat-modeling-tool-threats.md)** om du vill veta mer om de hotkategorier som verktyget använder för att skapa möjliga designhot.