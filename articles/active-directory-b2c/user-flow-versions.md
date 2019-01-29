---
title: Användaren flow-versioner i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om vilka versioner av användarflöden som är tillgängliga i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b3750768eb7346938faec7e3c1ead232923a68bd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159496"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Användaren flow-versioner i Azure Active Directory B2C

>[!IMPORTANT]
> Alla flöden för användare som anges i artikeln anses vara i offentlig förhandsversion, såvida inte identifieras som **rekommenderas**. Du bör endast använda rekommenderade användarflöden för dina produktionsprogram.

Användarflöden i Azure Active Directory (Azure AD) B2C hjälper dig att konfigurera vanliga [principer](active-directory-b2c-reference-policies.md) som helt beskriver identitet kundupplevelser. Dessa upplevelser är registrering, inloggning, lösenordsåterställning eller profilredigering. Du kan välja från en samling både rekommenderade användarflöden och användarflöden för förhandsgranskning i Azure AD B2C. 

Den nya användarflöden har lagts till som nya versioner. När användarflöden blir stabila, kommer de rekommenderas för användning. Användarflöden är märkta **rekommenderas** om de har testats noggrant. Användarflöden betraktas förhandsversion tills har markerats som rekommenderas. Använda en rekommenderad användarflödet för alla produktionsprogram, men vill testa nya funktioner blir tillgängliga från andra versioner. Du bör inte använda äldre versioner av rekommenderade användarflöden.

## <a name="v1"></a>V1

| Användarflödet | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Lösenordsåterställning | Ja | Används att välja ett nytt lösenord när du har verifierat sin e-post. Med det här användarflödet kan konfigurera du: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Tokenkompatibilitetsinställningar</li><li>[Krav på lösenordskomplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Profilredigering | Ja | Används att konfigurera sina användarattribut. Med det här användarflödet kan konfigurera du: <ul><li>[Livslängd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li></ul> |
| resursägaren | Nej | Kan en användare med ett lokalt konto att logga in direkt i interna program (inga webbläsare krävs). Med det här användarflödet kan konfigurera du: <ul><li>[Livslängd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li></ul> |
| Logga in | Nej | Gör det möjligt för en användare att logga in på sitt konto. Med det här användarflödet kan konfigurera du: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livslängd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li><li>Blockera inloggning</li><li>Framtvinga återställning av lösenord</li><li>Håll mig inloggad (KMSI)</ul><br>Du kan inte anpassa användargränssnittet med det här användarflödet. |
| Registrera dig | Nej | Används att skapa ett konto. Med det här användarflödet kan konfigurera du: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livslängd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li><li>[Krav på lösenordskomplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrera dig och logga in | Ja | Används att skapa ett konto eller logga in på sitt konto. Med det här användarflödet kan konfigurera du: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livslängd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li><li>[Krav på lösenordskomplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>V2

| Användarflödet | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Återställning av lösenord v2 | Nej | Används att välja ett nytt lösenord när du har verifierat sin e-post. Med det här användarflödet kan konfigurera du: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Tokenkompatibilitetsinställningar</li><li>[Åldershantering](basic-age-gating.md)</li><li>[Krav på lösenordskomplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Logga in v2 | Nej | Gör det möjligt för en användare att logga in på sitt konto. Med det här användarflödet kan konfigurera du: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livslängd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li><li>[Åldershantering](basic-age-gating.md)</li><li>På inloggningssidan anpassning</li></ul> |
| Registrera sig v2 | Nej | Används att skapa ett konto. Med det här användarflödet kan konfigurera du: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livslängd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li><li>[Åldershantering](basic-age-gating.md)</li><li>[Krav på lösenordskomplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrera sig och logga in v2 | Nej | Används att skapa ett konto eller logga in sitt konto. Med det här användarflödet kan konfigurera du: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Åldershantering](basic-age-gating.md)</li><li>[Krav på lösenordskomplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
