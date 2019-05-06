---
title: Lär dig mer om Microsoft Authentication Library (MSAL) | Azure
description: 'Microsoft Authentication Library (MSAL) gör det möjligt för apputvecklare att hämta token för att kunna anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft-APIS, från tredje part webb-API: er eller Web API. MSAL har stöd för flera arkitekturer för program och plattformar.'
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2198a5efe276ce872487cde7b2ddb5cb00c58c50
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080900"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Översikt över Microsoft Authentication Library (MSAL)
Microsoft Authentication Library (MSAL) gör det möjligt för utvecklare att hämta [token](developer-glossary.md#security-token) från Microsoft identity-plattformen slutpunkt för att komma åt skyddade webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft-APIS, från tredje part webb-API: er eller Web API. MSAL är tillgänglig för .NET, JavaScript, Android och iOS, som stöder många olika arkitekturer och plattformar.

MSAL ger dig många sätt att hämta token med en konsekvent API för flera plattformar. Med MSAL ger följande fördelar:

* Du behöver inte använda OAuth-bibliotek eller koda mot protokollet direkt i ditt program.
* Hämtar token för en användares räkning eller åt ett program (när det gäller för plattformen).
* Underhåller ett token-cache och uppdaterar token för dig när de är nära upphör att gälla. Du behöver inte hantera giltighetstid för token på egen hand.
* Kan du ange vilken målgrupp du vill att programmet ska logga in (org, flera organisationer, arbete och skola och personliga Microsoft-konton, sociala identiteter med Azure AD B2C, användare i suveräna och nationella moln).
* Kan du ställa in ditt program från configuration-filer.
* Hjälper dig att felsöka din app genom att exponera användbara undantag, loggning och telemetri.

## <a name="application-types-and-scenarios"></a>Programtyper och scenarier
Med MSAL en token kan fås från flera typer av program: webbappar, webb-API: er, enkelsidiga appar (JavaScript), mobila och interna program, och Daemon och serverbaserade program. 

MSAL kan användas i många Programscenarier, bland annat följande:

* [En sida-program (JavaScript)](scenario-spa-overview.md) 
* [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)
* [Webbprogram logga in en användare och anropa ett webb-API åt användaren](scenario-web-app-call-api-overview.md)
* [Skydda ett webb-API så att endast autentiserade användare har åtkomst till den](scenario-protected-web-api-overview.md)
* [Webb-API som anropar ett annat underordnat webb-API åt den inloggade användaren](scenario-web-api-call-api-overview.md)
* [Skrivbordsprogram som anropar ett webb-API åt den inloggade användaren](scenario-desktop-overview.md)
* [Mobila program som anropar ett webb-API för den användare som är inloggade interaktivt](scenario-mobile-overview.md).
* [Tjänsten/daemonprogram anropa webb-API för själva](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Språk och ramverk

| Bibliotek | Plattformar som stöds och ramverk|
| --- | --- | 
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/>[MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET framework, .NET Core, Xamarin Android, Xamarin iOS, Universal Windows Platform|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/>[MSAL.js (förhandsversion)](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript ramverk som AngularJS, Ember.js eller Durandal.js|
| ![MSAL för Android](media/sample-v2-code/logo_Android.png) <br/>[MSAL för Android (förhandsversion)](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| ![MSAL för iOS](media/sample-v2-code/logo_iOS.png) <br/>[MSAL.Objective-C (preview)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS|

## <a name="differences-between-adal-and-msal"></a>Skillnader mellan ADAL och MSAL
Active Directory Authentication Library (ADAL) integreras med Azure AD för utvecklare (v1.0) slutpunkt, där MSAL kan integreras med Microsoft identity-plattformen (v2.0) slutpunkt. V1.0 slutpunkten stöder arbetskonton, men inte personliga konton. V2.0-slutpunkten är med personliga Microsoft-konton och arbetskonton möjliggör i ett enda autentiseringssystem. Dessutom med MSAL kan du också få autentiseringar för Azure AD B2C.

Mer detaljerad information, Läs om [migrerar till MSAL.NET från ADAL.NET](msal-net-migration.md) och [migrerar till MSAL.js från ADAL.js](msal-compare-msal-js-and-adal-js.md).

            