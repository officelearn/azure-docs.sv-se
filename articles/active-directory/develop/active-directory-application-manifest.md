---
title: Förstå programmanifestet för Azure Active Directory | Microsoft Docs
description: Detaljerad täckning av applikationsmanifestet Azure Active Directory, som representerar ett programs identitet konfigurationen i en Azure AD-klient och används för att underlätta OAuth auktorisering, samtycke upplevelsen med mera.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, sureshja
ms.openlocfilehash: 0783c9885ec47bdd8c33c296e975547391900139
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505743"
---
# <a name="azure-active-directory-application-manifest"></a>Azure Active Directorys programmanifest
Appar som integrerar med Azure AD måste registreras med en Azure AD-klient. Den här appen kan konfigureras med hjälp av appmanifestet (under bladet Azure AD) i den [Azure-portalen](https://portal.azure.com).

## <a name="manifest-reference"></a>Manifest-referens

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Nyckel  |Värdetyp |Exempelvärde  |Beskrivning  |
|---------|---------|---------|---------|
|appID     |  Strängen för meddelandealternatividentifieraren       |""|  Den unika identifieraren för det program som har tilldelats en app av Azure AD.|
|appRoles     |    Typ av matris     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|Roller som ett program kan deklarera samling. Dessa roller kan tilldelas användare, grupper eller tjänstens huvudnamn.|
|availableToOtherTenants|boolesk|`true`|Om det här värdet anges till SANT, programmet är tillgängligt för andra klienter. Den är registrerad i om inställt på false, appen är endast tillgänglig för innehavaren. Mer information finns i: [så här loggar du in alla Azure Active Directory (AD)-användare med programmönstret för flera innehavare](active-directory-devhowto-multi-tenant-overview.md). |
|displayName     |sträng         |`MyRegisteredApp`         |Visningsnamn för programmet. |
|errorURL     |sträng         |`http://MyRegisteredAppError`         |URL för fel som påträffats i ett program. |
|groupMembershipClaims     |    sträng     |    `1`     |   En bitmask som konfigurerar anspråket ”grupper” utfärdats i en användare eller OAuth 2.0-åtkomsttoken som programmet förväntas. Värdena är: 0: Ingen, 1: säkerhetsgrupper och Azure AD-roller, 2: reserverad och 4: reserverad. Ställa in bitmask 7 får alla säkerhetsgrupper, distributionsgrupper och Azure AD-katalogroller som den inloggade användaren är medlem i. |
|optionalClaims     |  sträng       |     `null`    |    Den [valfria anspråk](active-directory-optional-claims.md) returneras i token av säkerhetstokentjänsten för den här specifika appen. |
|acceptMappedClaims    |      boolesk   | `true`        |    Om det här värdet anges till true, tillåter ett program att använda Anspråksmappning utan att ange en anpassad signeringsnyckel.|
|Startsida     |  sträng       |`http://MyRegistererdApp`         |    URL: en till programmets hemsida. |
|identifieruris finns     |  Strängmatris       | `http://MyRegistererdApp`        |   Användardefinierade URI(s) som unikt identifierar ett webbprogram inom dess Azure AD-klient eller inom en verifierad anpassad domän om programmet är flera innehavare. |
|keyCredentials     |   Typ av matris      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   Den här egenskapen innehåller referenser till programmet tilldelade autentiseringsuppgifter, sträng-baserade delade hemligheter och X.509-certifikat. Dessa autentiseringsuppgifter används när du begär åtkomsttoken (när appen fungerar som en klient i stället som som resurs). |
|knownClientApplications     |     Typ av matris    |    [guid]     |     Värdet används för sammanföra medgivande om du har en lösning som innehåller två delar, ett klientprogram och en anpassad web API-program. Om du anger appID av klientprogrammet i det här värdet behöver användaren bara godkänna en gång till klientprogrammet. Azure AD vet att medgivandedialogen klienten innebär implicit medgivandedialogen till webb-API och kommer automatiskt att etablera tjänsthuvudnamn för både klient- och webb-API på samma gång. Både klienten och webb-API-program måste registreras i samma klientorganisation.|
|logoutUrl     |   sträng      |     `http://MyRegisteredAppLogout`    |   URL till logga ut från programmet. |
|oauth2AllowImplicitFlow     |   boolesk      |  `false`       |       Anger om det här webbprogrammet kan begära OAuth2.0 implicit flöde token. Standardvärdet är FALSKT. Den här flaggan används för webbläsarbaserade appar som appar med Javascript en sida. |
|oauth2AllowUrlPathMatching     |   boolesk      |  `false`       |   Anger om, Azure AD som en del av OAuth 2.0-tokenbegäranden, ska tillåta sökväg vägmatchning av omdirigerings-URI: N mot programmets svarsurl: er. Standardvärdet är FALSKT. |
|oauth2Permissions     | Typ av matris         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Samling av OAuth 2.0 behörighetsomfattningar som webb-API (resurs)-program som exponerar för klientprogram. De här behörighetsomfattningarna kan ges klientprogram under medgivande. |
|oauth2RequiredPostResponse     | boolesk        |    `false`     |      Anger om, Azure AD som en del av OAuth 2.0-tokenbegäranden, ska tillåta POST-förfrågningar, till skillnad från GET-begäranden. Standardvärdet är FALSKT, som anger att GET-begäranden ska tillåtas. 
|objekt-ID     | Strängen för meddelandealternatividentifieraren        |     ""    |    Den unika identifieraren för programmet i katalogen. Detta ID är inte den identifierare som används för att identifiera appen i alla protokoll-transaktioner. Det är användaren för refererar till objektet i directory-frågor.|
|passwordCredentials     | Typ av matris        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    Finns i beskrivningen för egenskapen keyCredentials. |
|publicClient     |  boolesk       |      `false`   | Anger om ett program är en offentlig klient (till exempel ett installerat program som körs på en mobil enhet). Standardvärdet är false. |
|supportsConvergence     |  boolesk       |   `false`      | Den här egenskapen bör inte redigeras. Godkänner du standardvärdet. |
|svarsurl: er     |  Strängmatris       |   `http://localhost`     |  Den här egenskapen som flera värden innehåller listan över registrerade redirect_uri värden som Azure AD accepterar som mål när returnerar token. |
|requiredResourceAccess     |     Typ av matris    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   Anger resurser som det här programmet kräver åtkomst till och en uppsättning behörighetsomfattningen för OAuth och programroller som krävs under var och en av dessa resurser. Den här förkonfigurering av åtkomst till nödvändiga resurser styr samtycke upplevelsen.|
|resourceAppId     |    Strängen för meddelandealternatividentifieraren     |  ""      |   Den unika identifieraren för den resurs som programmet kräver åtkomst till. Det här värdet ska vara lika med appId deklarerats i målprogrammet för resursen. |
|resourceAccess     |  Typ av matris       | Se exempel värdet för egenskapen requiredResourceAccess. |   Listan över behörighetsomfattningar för OAuth2.0 och roller som programmet behöver för den angivna resursen (innehåller värden för ID och typ av de angivna resurserna)        |
|samlMetadataUrl    |sträng| `http://MyRegisteredAppSAMLMetadata` |URL:en till SAML-metadatan för programmet.| 

## <a name="next-steps"></a>Nästa steg
* Mer information om relationen mellan program och tjänstens huvudnamn objekt i ett program finns i [program och tjänstobjekt i Azure AD][AAD-APP-OBJECTS].
* Se den [ordlista för Azure AD-utvecklare] [ AAD-DEVELOPER-GLOSSARY] för definitioner av några av de viktigaste begreppen för utvecklare av Azure Active Directory (AD).

Använd följande avsnitt för kommentarer för att ge feedback som hjälper dig att förfina och forma vårt innehåll.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

