---
title: Användar flödes versioner i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om de versioner av användar flöden som är tillgängliga i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: df411ee288b9759adbb3f8f84c28e4fed05f3c4c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258097"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Användar flödes versioner i Azure Active Directory B2C

Användar flöden i Azure Active Directory B2C (Azure AD B2C) hjälper dig att skapa gemensamma [principer](active-directory-b2c-reference-policies.md) som fullständigt beskriver kund identitets upplevelser. Dessa upplevelser omfattar registrering, inloggning, lösen ords återställning eller profil redigering. I Azure AD B2C kan du välja från en samling med både rekommenderade användar flöden och för hands versioner av användar flöden.

Nya användar flöden läggs till som nya versioner. När användar flöden blir stabila rekommenderar vi att de används. Användar flöden markeras som **rekommenderade** om de har testats grundligt. Användar flöden beaktas i för hands versionen tills det markeras som rekommenderat. Använd ett rekommenderat användar flöde för alla produktions program, men Välj från andra versioner för att testa nya funktioner när de blir tillgängliga. Du bör inte använda äldre versioner av rekommenderade användar flöden.

>[!IMPORTANT]
> Om ett användar flöde inte identifieras som **rekommenderat**anses det vara i för *hands version*. Du bör endast använda rekommenderade användar flöden för dina produktions program.

## <a name="v1"></a>V1

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Återställ lösenord | Ja | Gör det möjligt för en användare att välja ett nytt lösen ord när de har verifierat sin e-post. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Inställningar för tokenkompatibilitet</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Profilredigering | Ja | Gör det möjligt för en användare att konfigurera sina användarattribut. Med det här användar flödet kan du konfigurera: <ul><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li></ul> |
| Logga in med ROPC | Nej | Gör det möjligt för en användare med ett lokalt konto att logga in direkt i interna program (ingen webbläsare krävs). Med det här användar flödet kan du konfigurera: <ul><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Inställningar för tokenkompatibilitet</li></ul> |
| Logga in | Nej | Gör det möjligt för en användare att logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li><li>Blockera inloggning</li><li>Framtvinga lösen ords återställning</li><li>Håll mig inloggad (KMSI avgör)</ul><br>Du kan inte anpassa användar gränssnittet med det här användar flödet. |
| Registrera dig | Nej | Gör det möjligt för en användare att skapa ett konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrera sig och logga in | Ja | Gör det möjligt för en användare att skapa ett konto eller logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v11"></a>V 1.1

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Lösen ords återställning v 1.1 | Nej | Låter en användare välja ett nytt lösen ord när de har verifierat e-postmeddelandet (ny sidlayout är tillgänglig). Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Inställningar för tokenkompatibilitet</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Återställning av lösenord v2 | Nej | Gör det möjligt för en användare att välja ett nytt lösen ord när de har verifierat sin e-post. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Inställningar för tokenkompatibilitet</li><li>[Ålders hantera](basic-age-gating.md)</li><li>[krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Profilredigering v2 | Ja | Gör det möjligt för en användare att konfigurera sina användarattribut. Med det här användar flödet kan du konfigurera: <ul><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li></ul> |
| Logga in v2 | Nej | Gör det möjligt för en användare att logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li><li>[Ålders hantera](basic-age-gating.md)</li><li>Anpassning av inloggnings Sidan</li></ul> |
| Registrera sig v2 | Nej | Gör det möjligt för en användare att skapa ett konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Inställningar för tokenkompatibilitet</li><li>Sessionsbeteende</li><li>[Ålders hantera](basic-age-gating.md)</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrera dig och logga in v2 | Nej | Gör det möjligt för en användare att skapa ett konto eller logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Ålders hantera](basic-age-gating.md)</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
