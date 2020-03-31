---
title: Läs mer om MSAL | Azure
titleSuffix: Microsoft identity platform
description: Med MSAL (Microsoft Authentication Library) kan programutvecklare hämta token för att anropa skyddade webb-API:er. Dessa webb-API:er kan vara Microsoft Graph, andra Microsoft-APIS, webb-API:er från tredje part eller ditt eget webb-API. MSAL stöder flera programarkitekturer och plattformar.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c20d93c70484dc7ea800898da4309af2699c718e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085732"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Översikt över Microsoft Authentication Library (MSAL)
Med MICROSOFT Authentication Library (MSAL) kan utvecklare hämta [token](developer-glossary.md#security-token) från slutpunkten för Microsoft-identitetsplattformen för att komma åt skyddade webb-API:er. Dessa webb-API:er kan vara Microsoft Graph, andra Microsoft-APIS, webb-API:er från tredje part eller ditt eget webb-API. MSAL är tillgängligt för .NET, JavaScript, Android och iOS, som stöder många olika programarkitekturer och plattformar.

MSAL ger dig många sätt att hämta token, med ett konsekvent API för ett antal plattformar. Att använda MSAL ger följande fördelar:

* Du behöver inte använda OAuth-biblioteken eller koden direkt mot protokollet i ditt program.
* Hämtar token på uppdrag av en användare eller på uppdrag av ett program (när tillämpligt på plattformen).
* Upprätthåller en tokencache och uppdaterar token för dig när de är nära att upphöra att gälla. Du behöver inte hantera token utgångsdatum på egen hand.
* Hjälper dig att ange vilken målgrupp du vill att ditt program ska logga in (din organisation, flera organisationer, arbets- och skol- och Microsoft-personliga konton, sociala identiteter med Azure AD B2C, användare i suveräna och nationella moln).
* Hjälper dig att konfigurera programmet från konfigurationsfiler.
* Hjälper dig att felsöka appen genom att exponera användbara undantag, loggning och telemetri.

## <a name="application-types-and-scenarios"></a>Programtyper och scenarier
Med MSAL kan en token hämtas från ett antal programtyper: webbprogram, webb-API:er, ensidiga appar (JavaScript), mobila och inbyggda program samt demoner och serverprogram.

MSAL kan användas i många programscenarier, bland annat följande:

* [Program med en sida (JavaScript)](scenario-spa-overview.md)
* [Logga in användare i webbapp](scenario-web-app-sign-user-overview.md)
* [Webbprogram signerar en användare och anropar ett webb-API för användarens räkning](scenario-web-app-call-api-overview.md)
* [Skydda ett webb-API så att endast autentiserade användare kan komma åt det](scenario-protected-web-api-overview.md)
* [Webb-API anropar ett annat nedströms webb-API för den inloggade användarens räkning](scenario-web-api-call-api-overview.md)
* [Skrivbordsprogram som anropar ett webb-API för den inloggade användarens räkning](scenario-desktop-overview.md)
* [Mobilapplikation anropar ett webb-API för den användare som är inloggad interaktivt](scenario-mobile-overview.md).
* [Desktop / service daemon program ringer webb-API på uppdrag av sig själv](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Språk och ramverk

| Bibliotek | Plattformar och ramverk som stöds|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Universal Windows-plattformen|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript-ramverk som AngularJS, Ember.js eller Durandal.js|
| [MSAL för Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL för iOS och macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS och macOS|
| [MSAL Java (förhandsgranskning)](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|
| [MSAL Python (förhandsgranskning)](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Python|

## <a name="differences-between-adal-and-msal"></a>Skillnader mellan ADAL och MSAL

Active Directory Authentication Library (ADAL) integreras med slutpunkten för Azure AD för utvecklare (v1.0), där MSAL integreras med slutpunkten för Microsoft Identity Platform (v2.0). V1.0-slutpunkten stöder arbetskonton, men inte personliga konton. V2.0-slutpunkten är sammanslagningen av Microsofts personliga konton och arbetskonton till ett enda autentiseringssystem. Dessutom kan du med MSAL även hämta autentiseringar för Azure AD B2C.

Mer specifik information finns i om [du migrerar till MSAL.NET från ADAL.NET](msal-net-migration.md) och [migrerar till MSAL.js från ADAL.js](msal-compare-msal-js-and-adal-js.md).
