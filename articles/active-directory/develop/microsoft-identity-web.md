---
title: Översikt över Microsoft Identity Web Authentication Library
titleSuffix: Microsoft identity platform
description: 'Lär dig mer om Microsoft Identity Web, ett bibliotek för autentisering och auktorisering för ASP.NET Core program som integreras med Azure Active Directory, Azure AD B2C och Microsoft Graph och andra webb-API: er.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b82e300128a41f8315132e1ff93af33c853edb15
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173523"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft Identity Web Authentication Library

Microsoft Identity Web är en uppsättning ASP.NET Core-bibliotek som gör det enklare att lägga till stöd för autentisering och auktorisering till webbappar och webb-API: er som integreras med Microsoft Identity Platform. Det ger ett API-lager med en enkel yta som samverkar med ASP.NET Core, dess autentisering mellanprogram och [Microsoft Authentication Library (MSAL) för .net](https://github.com/azuread/microsoft-authentication-library-for-dotnet).

## <a name="supported-application-scenarios"></a>Program scenarier som stöds

Om du skapar ASP.NET Core webbappar eller webb-API: er och vill använda Azure Active Directory (Azure AD) eller Azure AD B2C för identitets-och åtkomst hantering (IAM) rekommenderar vi att du använder Microsoft Identity Web för alla dessa scenarier:

- [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)
- [Webbapp som loggar in användare och anropar ett webb-API för deras räkning](scenario-web-app-call-api-overview.md)
- [Skyddat webb-API som bara autentiserade användare kan komma åt](scenario-protected-web-api-overview.md)
- [Skyddat webb-API som anropar ett annat (underordnat) webb-API för den inloggade användarens räkning](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>Hämta biblioteket

Du kan hämta Microsoft Identity Web från [NuGet](#nuget), [.net Core Project templates](#project-templates)och [GitHub](#github).

#### <a name="nuget"></a>NuGet

Microsoft Identity Web finns på NuGet som en uppsättning paket som tillhandahåller modulära funktioner baserat på appens behov. Använd .NET CLI- `dotnet add` kommandot eller Visual Studios **NuGet Package Manager** för att installera de paket som är lämpliga för ditt projekt:

- [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) – huvud paketet. Krävs av alla appar som använder Microsoft Identity Web.
- [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) – valfritt. Lägger till användar gränssnitt för användar inloggning och utloggning och en tillhör ande kontrollant för Web Apps.
- [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) – valfritt. Ger förenklad interaktion med Microsoft Graph API.
- [Microsoft. Identity. Web. MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) – valfritt. Ger förenklad interaktion med Microsoft Graph API [beta-slutpunkten](/graph/api/overview?view=graph-rest-beta&preserve-view=true).

#### <a name="project-templates"></a>Projektmallar

Microsoft Identity Web Project-mallar ingår i .NET 5,0 och är tillgängliga för nedladdning för ASP.NET Core 3,1-projekt.

Om du använder ASP.NET Core 3,1 installerar du mallarna med .NET CLI:

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

Följande diagram visar en övergripande vy över de typer av appar som stöds och deras relevanta argument:

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Diagram över tillgängliga dot-projektmallar för net CLI för Microsoft Identity Web":::
<br /><sup><b>*</b></sup>`MultiOrg`stöds inte med `webapi2` , men kan aktive ras i *appsettings.jspå* genom att ställa in klient till `common` eller`organizations`
<br /><sup><b>**</b></sup>`--calls-graph`stöds inte för Azure AD B2C

Det här exemplet på .NET CLI-kommandot, som vi tar från vår [själv studie kurs](tutorial-blazor-server.md), genererar ett nytt snabb Server projekt som innehåller rätt paket och start kod (plats hållare-värden som visas):

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id "00000000-0000-0000-0000-000000000000" --tenant-id "11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft Identity Web är ett projekt med öppen källkod som finns på GitHub: <a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">AzureAD/Microsoft-Identity- <span class="docon docon-navigate-external x-hidden-focus"></span> Web</a>

[Wiki-lagringsplatsen](https://github.com/AzureAD/microsoft-identity-web/wiki) innehåller ytterligare dokumentation, och om du behöver hjälp eller upptäcka en bugg kan du [skicka in ett problem](https://github.com/AzureAD/microsoft-identity-web/issues).

## <a name="features"></a>Funktioner

Microsoft Identity Web innehåller flera funktioner som inte tillhandahålls om du använder standard ASP.NET 3,1-projektmallar.

| Funktion                                                                                  | ASP.NET Core 3,1                                                     | Microsoft Identity Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [Logga in användare](scenario-web-app-sign-user-app-configuration.md) i Web Apps             | <li>Arbets- eller skolkonton<li>Sociala identiteter (med Azure AD B2C) | <li>Arbets- eller skolkonton<li>Personliga Microsoft-konton<li>Sociala identiteter (med Azure AD B2C)     |
| [Skydda webb-API: er](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>Arbets- eller skolkonton<li>Sociala identiteter (med Azure AD B2C) | <li>Arbets- eller skolkonton<li>Personliga Microsoft-konton<li>Sociala identiteter (med Azure AD B2C)     |
| Verifiering av utfärdare i appar för flera klient organisationer                                                   | Nej                                                                   | Ja, för [alla moln](authentication-national-cloud.md) och [Azure AD B2C](../../active-directory-b2c/index.yml) |
| Webbapp/API [anrop Microsoft Graph] [scenario-API-anrop-Graph]                             | Nej                                                                   | Ja                                                                                                     |
| Webbapp/API [anropar webb-API] [scenario-API-anrop-API]                                       | Nej                                                                   | Ja                                                                                                     |
| Har stöd för autentiseringsuppgifter för certifikat                                                         | Nej                                                                   | Ja, inklusive Azure Key Vault                                                                          |
| Stegvist godkännande och stöd för villkorlig åtkomst i Web Apps                           | Nej                                                                   | Ja, i MVC, kniv sidor och blixt                                                                    |
| Token krypterings certifikat i webb-API: er                                                | Nej                                                                   | Ja                                                                                                     |
| [Verifiering av omfattningar/app-roll] [scenario – API-validering] i webb-API: er                        | Nej                                                                   | Ja                                                                                                     |
| `WWW-Authenticate` Skapa sidhuvud i webb-API: er                                         | Nej                                                                   | Ja                                                                                                     |

## <a name="next-steps"></a>Nästa steg

Om du vill se hur Microsoft Identity Web fungerar, kan du prova vår själv studie kurs:

[Självstudie: skapa en snabb server app som använder Microsoft Identity Platform för autentisering](tutorial-blazor-server.md)

Microsoft Identity Web wiki på GitHub innehåller omfattande referens dokumentation för olika aspekter av biblioteket. Du kan till exempel hitta certifikat användning, stegvisa medgivande och referens för villkorlig åtkomst här:

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">Använda certifikat med Microsoft. Identity. Web <span class="docon docon-navigate-external x-hidden-focus"></span> </a> GitHub
- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">Stegvist medgivande och villkorlig åtkomst <span class="docon docon-navigate-external x-hidden-focus"></span> </a> GitHub

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[scenario-API-anrops-API]: scenario-Web-API-Call-API-Call-API. MD # alternativ-1-anrop-Microsoft-Graph-with-SDK [scenario-API-Call-Graph]: scenario-Web-API-Call-API-Call-Graph. MD # alternativ-1-Call-Microsoft-Graph-with-SDK [scenario-API-verifiering]: scenario-protected-web-api-verification-scope-app-roles.md