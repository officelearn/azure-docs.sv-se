---
title: Förstå Azure Active Directory-programmanifestet | Microsoft Docs
description: Detaljerad täckning av Azure Active Directory-programmanifestet som representerar ett programs identitet konfiguration i en Azure AD-klient och används för att underlätta OAuth auktorisering, medgivande upplevelse och mycket mer.
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
ms.reviewer: elisol
ms.openlocfilehash: 9f73f31c7afd7ca13107653d097e1ac11ef94f0d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-application-manifest"></a>Azure Active Directory-programmanifestet
Appar som integreras med Azure AD måste vara registrerad med Azure AD-klient. Den här appen kan konfigureras med appmanifestet (under Azure AD-bladet) i den [Azure-portalen](https://portal.azure.com).

## <a name="manifest-reference"></a>Manifestet referens

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Nyckel  |Värdetyp |Exempelvärde  |Beskrivning  |
|---------|---------|---------|---------|
|appID     |  Strängen för meddelandealternatividentifieraren       |""|  Den unika identifieraren för det program som har tilldelats en app av Azure AD.|
|appRoles     |    Typ av matris     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|Roller som ett program kan förklara samling. Rollerna kan tilldelas användare, grupper eller tjänstens huvudnamn.|
|AvailableToOtherTenants|boolesk|`true`|Om det här värdet anges till true, programmet är tillgängligt för andra klienter. Den är registrerad i om inställt på false, appen är endast tillgänglig för innehavaren. Mer information finns: [loggar in alla Azure Active Directory (AD)-användare med flera innehavare programmönster](active-directory-devhowto-multi-tenant-overview.md). |
|Visningsnamn     |sträng         |`MyRegisteredApp`         |Visningsnamn för programmet. |
|errorURL     |sträng         |`http://MyRegisteredAppError`         |URL till fel påträffades i ett program. |
|GroupMembershipClaims     |    sträng     |    `1`     |   En bitmask som konfigurerar ”grupper”-anspråket utfärdat i en användare eller OAuth 2.0-åtkomsttoken som programmet förväntas. Värdena är: 0: ingen 1: säkerhetsgrupper och Azure AD-roller, 2: reserverade och 4: reserverat. Ange bitmask till 7 får alla säkerhetsgrupper, distributionsgrupper och Azure AD directory roller som den inloggade användaren är medlem i. |
|optionalClaims     |  sträng       |     `null`    |    Den [valfria anspråk](active-directory-optional-claims.md) i token som returnerades av säkerhetstokentjänsten för den här specifika appen. |
|acceptMappedClaims    |      boolesk   | `true`        |    Om det här värdet anges till true, kan ett program att använda anspråk mappning utan att ange en anpassad signeringsnyckeln.|
|Startsida     |  sträng       |`http://MyRegistererdApp`         |    URL till programmets startsida. |
|identifierUris     |  Strängmatris       | `http://MyRegistererdApp`        |   Användardefinierade URI(s) som unikt identifierar ett webbprogram i sin Azure AD-klient eller i en anpassad domän för verifierade om programmet är flera innehavare. |
|keyCredentials     |   Typ av matris      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   Den här egenskapen innehåller referenser till programmet tilldelade autentiseringsuppgifter, string-baserade delade hemligheter och X.509-certifikat. Dessa autentiseringsuppgifter används när du begär åtkomst-token (när appen fungerar som en klient i stället som som resurs). |
|knownClientApplications     |     Typ av matris    |    [guid]     |     Värdet används för sammanföra medgivande om du har en lösning som innehåller två delar, ett klientprogram och en anpassad webbplats API-program. Om du anger appID av klientprogrammet i det här värdet kan måste användaren bara godkänna en gång till klientprogrammet. Azure AD vet att principer för klienten innebär implicit principer för webb-API och kommer automatiskt att etablera tjänstens huvudnamn för både klient- och webb-API på samma gång. Både klienten och webb-API-program måste registreras i samma klientorganisation.|
|logoutUrl     |   sträng      |     `http://MyRegisteredAppLogout`    |   Logga ut från programmet URL-adress. |
|oauth2AllowImplicitFlow     |   boolesk      |  `false`       |       Anger om det här webbprogrammet kan begära OAuth2.0 implicita flödet för token. Standardvärdet är FALSKT. Den här flaggan används för webbläsarbaserade appar som Javascript-ensidesappar. |
|oauth2AllowUrlPathMatching     |   boolesk      |  `false`       |   Anger om, Azure AD som en del av OAuth 2.0 token-förfrågningar, ska tillåta sökvägsmatchning för omdirigerings-URI mot programmets replyUrls. Standardvärdet är FALSKT. |
|oauth2Permissions     | Typ av matris         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Insamling av behörighetsomfattningen för OAuth 2.0 som webb-API (resurs)-program visar för klientprogram. Klientprogram kan bevilja dessa behörighetsomfattningen under medgivande. |
|oauth2RequiredPostResponse     | boolesk        |    `false`     |      Anger om, Azure AD som en del av OAuth 2.0 token-förfrågningar, ska tillåta POST-begäranden, till skillnad från GET-begäranden. Standardvärdet är FALSKT, vilket innebär att GET-begäranden ska tillåtas. 
|objekt-ID     | Strängen för meddelandealternatividentifieraren        |     ""    |    Unik identifierare för programmet i katalogen. Detta ID är inte det ID som används för att identifiera appen i någon transaktion för protokollet. Användaren är refererar till objektet i directory-frågor.|
|passwordCredentials     | Typ av matris        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    Se beskrivningen för egenskapen keyCredentials. |
|PublicClient     |  boolesk       |      `false`   | Anger om ett program är en offentlig klient (till exempel ett installerat program som körs på en mobil enhet). Standardvärdet är false. |
|supportsConvergence     |  boolesk       |   `false`      | Den här egenskapen bör inte redigeras. Acceptera standardvärdet. |
|replyUrls     |  Strängmatris       |   `http://localhost`     |  Flera värden egenskapen innehåller listan över registrerade redirect_uri värden som Azure AD tar emot som mål när du returnerar token. |
|RequiredResourceAccess     |     Typ av matris    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   Anger resurser som det här programmet kräver åtkomst till och uppsättning behörighetsomfattningen för OAuth och roller för programmet som krävs under var och en av dessa resurser. Den här före konfiguration av nödvändiga resursåtkomst enheter medgivande-upplevelse.|
|resourceAppId     |    Strängen för meddelandealternatividentifieraren     |  ""      |   Den unika identifieraren för den resurs som programmet kräver åtkomst till. Det här värdet ska vara lika med appId som deklarerats i målprogrammet för resursen. |
|resourceAccess     |  Typ av matris       | Se exempelvärdet för egenskapen requiredResourceAccess. |   Listan över OAuth2.0 behörighetsomfattningen och app-roller som programmet kräver för den angivna resursen (innehåller värdena ID och typ av de angivna resurserna)        |
|samlMetadataUrl    |sträng| `http://MyRegisteredAppSAMLMetadata` |URL:en till SAML-metadatan för programmet.| 

## <a name="next-steps"></a>Nästa steg
* Läs mer om förhållandet mellan ett program program och tjänstens huvudnamn objekt [program och tjänstens huvudnamn objekt i Azure AD][AAD-APP-OBJECTS].
* Finns det [Azure AD-utvecklare ordlista] [ AAD-DEVELOPER-GLOSSARY] definitioner av vissa grundbegrepp för utvecklare i Azure Active Directory (AD).

Använd följande kommentarer avsnitt för att ge feedback som hjälper dig att förfina och formar innehållet.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
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

