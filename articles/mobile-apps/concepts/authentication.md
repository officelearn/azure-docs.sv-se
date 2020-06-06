---
title: Lägg till autentisering i dina mobilappar med Visual Studio App Center-och Azure-tjänster
description: Lär dig mer om tjänsterna, till exempel Visual Studio App Center som hjälper dig att konfigurera användarautentisering och göra det möjligt för mobila program att autentisera med sociala konton, Azure Active Directory och anpassad autentisering.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ce7fcbb9f8386f2d3455168a78d6c0a9ab33a24d
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451020"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Lägg till autentisering och hantera användar identiteter i dina mobila appar

Att ha en vy över användaren och deras beteende i ditt program gör det möjligt för utvecklare att bättre engagera användare genom att skapa anpassade upplevelser för dem. Oavsett om du är programutvecklare som bygger ett samarbets program för användare i organisationen eller om du skapar nästa sociala nätverks plattform, behöver du ett sätt att autentisera användare och hantera användar identiteter. En tjänst för identitets hantering är en av de viktigaste funktionerna i en mobil Server dels tjänst.

Använd följande tjänster för att aktivera användarautentisering i dina mobila appar.

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) är en B2C-tjänst (Business-to-Consumer) som utvecklare kan använda för att autentisera sina kunder. Med den här tjänsten med vita etiketter kan utvecklare anpassa och styra hur användare på ett säkert sätt kan interagera med sina webb-, skriv bords-, mobil-eller program på en enda sida. Med Azure AD B2C kan användarna registrera sig, logga in, återställa lösenord och redigera profiler. Azure AD B2C implementerar en form av OpenID Connect- och OAuth 2.0-protokoll. 

**Huvudfunktioner**
- Autentisera kunder på ett säkert sätt med deras prioriterade identitets leverantör.
- Hantera kund identitet och åtkomst.
- Få inloggnings stöd för sociala medier som Facebook, GitHub, Google, LinkedIn, Twitter, WeChat och Weibo.
- Anslut till dina användar konton genom att använda bransch standard protokoll som OpenID Connect eller SAML för att göra identitets hanteringen möjlig på olika plattformar.
- Tillhandahålla anpassad registrering och inloggnings upplevelser.
- Integrera enkelt med CRM-databaser, verktyg för marknadsförings analys och system för konto verifiering.
- Avbilda inloggnings-, preferens-och konverterings data för kunder.

**Referenser**
- [Azure Portal](https://portal.azure.com/)
- [Azure AD B2C dokumentation](/azure/active-directory-b2c/)
- [Snabbstarter](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Exempel](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade identitets-och åtkomst hanterings tjänst, som hjälper dina anställda att logga in och få åtkomst till:
- Externa resurser, till exempel Microsoft Office 365, Azure Portal och tusentals andra program vara som en tjänst (SaaS).
- Interna resurser, som appar i företagets nätverk och intranät, tillsammans med molnappar som utvecklats av din egen organisation.

**Huvudfunktioner**
- Smidig och mycket säker åtkomst genom att ansluta användare till de program som de behöver.
- Omfattande identitets skydd och förbättrad säkerhet för identiteter och åtkomst baserat på användare, plats, enhet, data och program kontext.
- Tusentals förintegrerade appar för både kommersiella och anpassade program, till exempel Office 365, Salesforce.com och box.
- Möjlighet att hantera åtkomst i stor skala.

**Referenser**
- [Azure Portal](https://portal.azure.com/)
- [Vad är Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Kom igång med Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Snabbstarter](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)