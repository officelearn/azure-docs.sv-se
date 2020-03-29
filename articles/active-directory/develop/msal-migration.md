---
title: Migrera till Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Lär dig mer om skillnaderna mellan MICROSOFT Authentication Library (MSAL) och Azure AD Authentication Library (ADAL) och hur du migrerar till MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164937"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrera program till Microsoft Authentication Library (MSAL)

Både Microsoft Authentication Library (MSAL) och Azure AD Authentication Library (ADAL) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Hittills har de flesta utvecklare arbetat med Azure AD för utvecklarplattform (v1.0) för att autentisera Azure AD-identiteter (arbets- och skolkonton) genom att begära token med Hjälp av Azure AD Authentication Library (ADAL). Använda MSAL:

- Du kan autentisera en bredare uppsättning Microsoft-identiteter (Azure AD-identiteter och Microsoft-konton samt sociala och lokala konton via Azure AD B2C) när den använder slutpunkten för Microsoft-identitetsplattformen.
- Användarna får den bästa upplevelsen för enkel inloggning.
- Ditt program kan aktivera inkrementellt medgivande och det är enklare att stödja villkorlig åtkomst.
- Du drar nytta av innovationen.

**MSAL är nu det rekommenderade auth-biblioteket som ska användas med Microsofts identitetsplattform**. Inga nya funktioner kommer att implementeras på ADAL. Insatserna är inriktade på att förbättra MSAL.

I följande artiklar beskrivs skillnaderna mellan MSAL- och ADAL-biblioteken och hjälper dig att migrera till MSAL:
- [Migrera till MSAL.NET](msal-net-migration.md)
- [Migrera till MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrera till MSAL.Android](migrate-android-adal-msal.md)
- [Migrera till MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [Migrera till MSAL Python](migrate-python-adal-msal.md)
- [Migrera till MSAL för Java](migrate-adal-msal-java.md)
- [Migrera Xamarin-appar med hjälp av asynkrona meddelandeköer till MSAL.NET](msal-net-migration-ios-broker.md)
