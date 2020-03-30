---
title: Användarflödesversioner i Azure Active Directory B2C | Microsoft-dokument
description: Lär dig mer om de versioner av användarflöden som är tillgängliga i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40d21d3390396e0cb7e44d4e19598f9b0b691087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185632"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Användarflödesversioner i Azure Active Directory B2C

Användarflöden i Azure Active Directory B2C (Azure AD B2C) hjälper dig att ställa in gemensamma [principer](user-flow-overview.md) som beskriver kundidentitetsupplevelser fullt ut. Dessa upplevelser omfattar registrering, inloggning, återställning av lösenord eller profilredigering. I Azure AD B2C kan du välja från en samling av både rekommenderade användarflöden och förhandsgranska användarflöden.

Nya användarflöden läggs till som nya versioner. När användarflödena blir stabila rekommenderas de för användning. Användarflöden markeras som **Rekommenderade** om de har testats noggrant. Användarflöden kommer att betraktas som förhandsgranskning tills de markeras som rekommenderade. Använd ett rekommenderat användarflöde för alla produktionsprogram, men välj från andra versioner för att testa nya funktioner när det blir tillgängligt. Du bör inte använda äldre versioner av rekommenderade användarflöden.

>[!IMPORTANT]
> Om inte ett användarflöde identifieras som **rekommenderat**anses det vara i *förhandsgranskning*. Du bör bara använda rekommenderade användarflöden för dina produktionsprogram.

## <a name="v1"></a>V1

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Lösenordsåterställning | Ja | Gör det möjligt för en användare att välja ett nytt lösenord när han har verifierat sin e-post. Med det här användarflödet kan du konfigurera: <ul><li>[Multifaktorautentisering](custom-policy-multi-factor-authentication.md)</li><li>Inställningar för tokenkompatibilitet</li><li>[Krav på lösenordskomplexitet](user-flow-password-complexity.md)</li></ul> |
| Redigering av profiler | Ja | Gör det möjligt för en användare att konfigurera sina användarattribut. Med det här användarflödet kan du konfigurera: <ul><li>[Token livstid](tokens-overview.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li></ul> |
| Logga in med ROPC | Inga | Gör det möjligt för en användare med ett lokalt konto att logga in direkt i inbyggda program (ingen webbläsare krävs). Med det här användarflödet kan du konfigurera: <ul><li>[Token livstid](tokens-overview.md)</li><li>Inställningar för tokenkompatibilitet</li></ul> |
| Logga in | Inga | Gör det möjligt för en användare att logga in på sitt konto. Med det här användarflödet kan du konfigurera: <ul><li>[Multifaktorautentisering](custom-policy-multi-factor-authentication.md)</li><li>[Token livstid](tokens-overview.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li><li>Blockera inloggning</li><li>Tvinga till återställning av lösenord</li><li>Håll mig inloggad (KMSI)</ul><br>Du kan inte anpassa användargränssnittet med det här användarflödet. |
| Registrera dig | Inga | Gör det möjligt för en användare att skapa ett konto. Med det här användarflödet kan du konfigurera: <ul><li>[Multifaktorautentisering](custom-policy-multi-factor-authentication.md)</li><li>[Token livstid](tokens-overview.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li><li>[Krav på lösenordskomplexitet](user-flow-password-complexity.md)</li></ul> |
| Registrera dig och logga in | Ja | Gör det möjligt för en användare att skapa ett konto eller logga in på sitt konto. Med det här användarflödet kan du konfigurera: <ul><li>[Multifaktorautentisering](custom-policy-multi-factor-authentication.md)</li><li>[Token livstid](tokens-overview.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li><li>[Krav på lösenordskomplexitet](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Återställning av lösenord v1.1 | Inga | Tillåter en användare att välja ett nytt lösenord efter att ha verifierat sin e-post (ny sidlayout tillgänglig). Med det här användarflödet kan du konfigurera: <ul><li>[Multifaktorautentisering](custom-policy-multi-factor-authentication.md)</li><li>Inställningar för tokenkompatibilitet</li><li>[Krav på lösenordskomplexitet](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Återställning av lösenord v2 | Inga | Gör det möjligt för en användare att välja ett nytt lösenord när han har verifierat sin e-post. Med det här användarflödet kan du konfigurera: <ul><li>[Multifaktorautentisering](custom-policy-multi-factor-authentication.md)</li><li>Inställningar för tokenkompatibilitet</li><li>[Åldershantering](basic-age-gating.md)</li><li>[krav på lösenordskomplexitet](user-flow-password-complexity.md)</li></ul> |
| Profilredigering v2 | Ja | Gör det möjligt för en användare att konfigurera sina användarattribut. Med det här användarflödet kan du konfigurera: <ul><li>[Token livstid](tokens-overview.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li></ul> |
| Logga in v2 | Inga | Gör det möjligt för en användare att logga in på sitt konto. Med det här användarflödet kan du konfigurera: <ul><li>[Multifaktorautentisering](custom-policy-multi-factor-authentication.md)</li><li>[Token livstid](tokens-overview.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li><li>[Åldershantering](basic-age-gating.md)</li><li>Anpassning av inloggningssidan</li></ul> |
| Registrera dig v2 | Inga | Gör det möjligt för en användare att skapa ett konto. Med det här användarflödet kan du konfigurera: <ul><li>[Multifaktorautentisering](custom-policy-multi-factor-authentication.md)</li><li>[Token livstid](tokens-overview.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li><li>[Åldershantering](basic-age-gating.md)</li><li>[Krav på lösenordskomplexitet](user-flow-password-complexity.md)</li></ul> |
| Registrera dig och logga in v2 | Inga | Gör det möjligt för en användare att skapa ett konto eller logga in på sitt konto. Med det här användarflödet kan du konfigurera: <ul><li>[Multifaktorautentisering](custom-policy-multi-factor-authentication.md)</li><li>[Åldershantering](basic-age-gating.md)</li><li>[Krav på lösenordskomplexitet](user-flow-password-complexity.md)</li></ul> |
