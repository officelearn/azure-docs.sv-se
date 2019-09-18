---
title: Användar flödes versioner i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om de versioner av användar flöden som är tillgängliga i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c7b3ce6a4e72b90c6fd642b92d4a92eb02f4c92c
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063186"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Användar flödes versioner i Azure Active Directory B2C

>[!IMPORTANT]
> Alla användar flöden som anges i artikeln anses vara i offentlig för hands version, om de inte identifieras som **rekommenderade**. Du bör endast använda rekommenderade användar flöden för dina produktions program.

Användar flöden i Azure Active Directory B2C (Azure AD B2C) hjälper dig att skapa gemensamma [principer](active-directory-b2c-reference-policies.md) som fullständigt beskriver kund identitets upplevelser. Dessa upplevelser omfattar registrering, inloggning, lösen ords återställning eller profil redigering. I Azure AD B2C kan du välja från en samling med både rekommenderade användar flöden och för hands versioner av användar flöden.

Nya användar flöden läggs till som nya versioner. När användar flöden blir stabila rekommenderar vi att de används. Användar flöden markeras som **rekommenderade** om de har testats grundligt. Användar flöden beaktas i för hands versionen tills det markeras som rekommenderat. Använd ett rekommenderat användar flöde för alla produktions program, men Välj från andra versioner för att testa nya funktioner när de blir tillgängliga. Du bör inte använda äldre versioner av rekommenderade användar flöden.

## <a name="v1"></a>V1

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Återställ lösenord | Ja | Gör det möjligt för en användare att välja ett nytt lösen ord när de har verifierat sin e-post. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Tokenkompatibilitetsinställningar</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Profilredigering | Ja | Gör det möjligt för en användare att konfigurera sina användarattribut. Med det här användar flödet kan du konfigurera: <ul><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li></ul> |
| Resurs ägare | Nej | Gör det möjligt för en användare med ett lokalt konto att logga in direkt i interna program (ingen webbläsare krävs). Med det här användar flödet kan du konfigurera: <ul><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li></ul> |
| Logga in | Nej | Gör det möjligt för en användare att logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li><li>Blockera inloggning</li><li>Framtvinga lösen ords återställning</li><li>Håll mig inloggad (KMSI avgör)</ul><br>Du kan inte anpassa användar gränssnittet med det här användar flödet. |
| Registrera dig | Nej | Gör det möjligt för en användare att skapa ett konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrera sig och logga in | Ja | Gör det möjligt för en användare att skapa ett konto eller logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>V2

| Användarflöde | Rekommenderas | Beskrivning |
| --------- | ----------- | ----------- |
| Återställning av lösenord v2 | Nej | Gör det möjligt för en användare att välja ett nytt lösen ord när de har verifierat sin e-post. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Tokenkompatibilitetsinställningar</li><li>[Ålders hantera](basic-age-gating.md)</li><li>[krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Logga in v2 | Nej | Gör det möjligt för en användare att logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li><li>[Ålders hantera](basic-age-gating.md)</li><li>Anpassning av inloggnings Sidan</li></ul> |
| Registrera sig v2 | Nej | Gör det möjligt för en användare att skapa ett konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Livs längd för token](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitetsinställningar</li><li>Sessionsbeteende</li><li>[Ålders hantera](basic-age-gating.md)</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrera dig och logga in v2 | Nej | Gör det möjligt för en användare att skapa ett konto eller logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Ålders hantera](basic-age-gating.md)</li><li>[Krav för lösen ords komplexitet](active-directory-b2c-reference-password-complexity.md)</li></ul> |
