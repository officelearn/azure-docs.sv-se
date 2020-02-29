---
title: Migrera till Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Lär dig mer om skillnaderna mellan Microsoft Authentication Library (MSAL) och Azure AD Authentication Library (ADAL) och hur du migrerar till MSAL.
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
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164937"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrera program till Microsoft Authentication Library (MSAL)

Både Microsoft Authentication Library (MSAL) och Azure AD Authentication Library (ADAL) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Fram till nu har de flesta utvecklare arbetat med Azure AD för utvecklare Platform (v 1.0) för att autentisera Azure AD-identiteter (arbets-och skol konton) genom att begära token med Azure AD Authentication Library (ADAL). Använda MSAL:

- Du kan autentisera en bredare uppsättning av Microsoft-identiteter (Azure AD-identiteter och Microsoft-konton, och sociala och lokala konton via Azure AD B2C) när du använder Microsoft Identity Platform-slutpunkten.
- Användarna får bästa möjliga upplevelse med enkel inloggning.
- Ditt program kan möjliggöra stegvist tillstånd och stöd för villkorlig åtkomst är enklare.
- Du får nytta av innovationen.

**MSAL är nu det rekommenderade auth-biblioteket som ska användas med Microsoft Identity Platform**. Inga nya funktioner kommer att implementeras på ADAL. Ansträngningarna fokuserar på att förbättra MSAL.

I följande artiklar beskrivs skillnaderna mellan MSAL-och ADAL-biblioteken och hjälper dig att migrera till MSAL:
- [Migrera till MSAL.NET](msal-net-migration.md)
- [Migrera till MSAL. js](msal-compare-msal-js-and-adal-js.md)
- [Migrera till MSAL. Android](migrate-android-adal-msal.md)
- [Migrera till MSAL. iOS/macOS](migrate-objc-adal-msal.md)
- [Migrera till MSAL python](migrate-python-adal-msal.md)
- [Migrera till MSAL för Java](migrate-adal-msal-java.md)
- [Migrera Xamarin-appar med hjälp av utjämnare till MSAL.NET](msal-net-migration-ios-broker.md)
