---
title: Lägga till autentisering i dina mobilappar med Visual Studio App Center och Azure-tjänster
description: Lär dig mer om tjänster som Visual Studio App Center som hjälper till att konfigurera användarautentisering och aktivera mobila program för att autentisera med sociala konton, Azure Active Directory och anpassad autentisering.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241038"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Lägga till autentisering och hantera användaridentiteter i dina mobilappar

Att ha en vy över användaren och deras beteende i hela programmet ger utvecklare möjlighet att bättre engagera användarna genom att skapa skräddarsydda upplevelser för dem. Oavsett om du är en programutvecklare som skapar ett samarbetsprogram för användare inom organisationen eller om du skapar nästa sociala nätverksplattform behöver du ett sätt att autentisera användare och hantera användaridentiteter. En identitetshanteringstjänst är en av de viktigaste funktionerna i en mobil backend-tjänst.

Använd följande tjänster för att aktivera användarautentisering i dina mobilappar.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Auth](/appcenter/auth/) är en molnbaserad identitetshanteringstjänst som utvecklare kan använda för att autentisera användare och hantera användaridentiteter. App Center Auth integreras också med andra delar av Visual Studio App Center. Utvecklare kan använda användaridentitet för att [visa användardata](/appcenter/data/index) i andra tjänster och till och med [skicka push-meddelanden till användare i stället för enskilda enheter](/appcenter/push/push-to-user#setting-user-identity). 

**Huvudfunktioner**
- Drivs av Azure Active Directory B2C (Azure AD B2C). 
    - I företagsklass.
    - Mycket tillgänglig.
    - Säker och global service.
- Ta med din egen identitet och möjligheten att använda andra populära identitets- och åtkomsthanteringsleverantörer, till exempel Auth0 och Firebase.
- Azure Active Directory-stöd.
    - Anslut befintliga Azure AD-klienter. 
    - Aktivera autentisering mot en företagsdomän.
    - Hantera åtkomst till känsliga data.
- Enkel användarupplevelse och magisk SDK-upplevelse genom att slå in Microsoft Authentication Library med Visual Studio App Center SDK.
- Plattformsstöd för iOS, Android, Xamarin och React Native.

**Referenser**
- [Registrera dig med Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Komma igång med App Center Auth](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) är en B2C-identitetshanteringstjänst (Business-to-consumer) som utvecklare kan använda för att autentisera sina kunder. Med den här white label-tjänsten kan utvecklare anpassa och styra hur användare interagerar säkert med sina webb-, dator-, mobil- eller ensidiga program. Med Azure AD B2C kan användarna registrera sig, logga in, återställa lösenord och redigera profiler. Azure AD B2C implementerar en form av OpenID Connect- och OAuth 2.0-protokoll. 

**Huvudfunktioner**
- Autentisera kunder säkert med den önskade identitetsleverantören.
- Hantera kundidentitet och åtkomst.
- Få inloggningsstöd för sociala medier som Facebook, GitHub, Google, LinkedIn, Twitter, WeChat och Weibo.
- Anslut till dina användarkonton med hjälp av branschstandardprotokoll, till exempel OpenID Connect eller SAML, för att göra identitetshantering möjlig på en mängd olika plattformar.
- Ge märkesregistrering och inloggningsupplevelser.
- Integrera enkelt med CRM-databaser, marknadsföringsanalysverktyg och kontoverifieringssystem.
- Samla in inloggnings-, inställnings- och konverteringsdata för kunder.

**Referenser**
- [Azure-portal](https://portal.azure.com/)
- [Azure AD B2C-dokumentation](/azure/active-directory-b2c/)
- [Snabbstarter](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Prover](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade identitets- och åtkomsthanteringstjänst, som hjälper dina anställda att logga in och få åtkomst till:
- Externa resurser, till exempel Microsoft Office 365, Azure-portalen och tusentals andra program som tjänstprogram (SaaS).
- Interna resurser, som appar i företagets nätverk och intranät, tillsammans med molnappar som utvecklats av din egen organisation.

**Huvudfunktioner**
- Sömlös, mycket säker åtkomst genom att ansluta användare till de program de behöver.
- Omfattande identitetsskydd och förbättrad säkerhet för identiteter och åtkomst baserat på användare, plats, enhet, data och programkontext.
- Tusentals förintegrerade appar för både kommersiella och anpassade program, till exempel Office 365, Salesforce.com och Box.
- Möjlighet att hantera åtkomst i stor skala.

**Referenser**
- [Azure-portal](https://portal.azure.com/)
- [Vad är Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Kom igång med Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Snabbstarter](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)