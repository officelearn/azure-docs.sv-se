---
title: "Förstå Azure Active Directorys programmanifest | Microsoft Docs"
description: "Detaljerad täckning av Azure Active Directory-programmanifestet som representerar ett programs identitet konfiguration i en Azure AD-klient och används för att underlätta OAuth auktorisering, medgivande upplevelse och mycket mer."
services: active-directory
documentationcenter: 
author: sureshja
manager: mtillman
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: f3284d4cbb15f21522549c678410815b54344744
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-application-manifest"></a>Azure Active Directory-programmanifestet
Appar som integreras med Azure AD måste vara registrerad med Azure AD-klient. Den här appen kan konfigureras med appmanifestet (under Azure AD-bladet) i den [Azure-portalen](https://portal.azure.com).

## <a name="manifest-reference"></a>Manifestet referens

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Nyckel  |Värdetyp |Exempelvärde  |Beskrivning  |
|---------|---------|---------|---------|
|appID     |  Strängen för meddelandealternatividentifieraren       |""|  Den unika identifieraren för det program som har tilldelats en app av Azure AD.|
|appRoles     |    Typ av matris     |[{<br>&emsp;”allowedMemberTypes”: [<br>&emsp;&nbsp;&nbsp;&nbsp;”Användare”<br>&emsp;],<br>&emsp;”Beskrivning”: ”Läs åtkomsten endast till enhetsinformation”,<br>&emsp;”Visningsnamn”: ”Read Only”,<br>&emsp;”id”: guid,<br>&emsp;”isEnabled”: true<br>&emsp;”värde”: ”ReadOnly”<br>}]|Roller som ett program kan förklara samling. Rollerna kan tilldelas användare, grupper eller tjänstens huvudnamn.|
|AvailableToOtherTenants|Booleskt värde|true|Om det här värdet anges till true, programmet är tillgängligt för andra klienter.  Den är registrerad i om inställt på false, appen är bara tillgängliga för klienten.  Läs mer: [loggar in alla Azure Active Directory (AD)-användare med flera innehavare programmönster](active-directory-devhowto-multi-tenant-overview.md). |
|Visningsnamn     |sträng         |MyRegisteredApp         |Visningsnamn för programmet. |
|errorURL     |sträng         |http:<i></i>//MyRegisteredAppError         |URL till fel påträffades i ett program. |
|GroupMembershipClaims     |    sträng     |    1     |   En bitmask som konfigurerar ”grupper”-anspråket utfärdat i en användare eller OAuth 2.0-åtkomsttoken som programmet förväntas. Värdena är: 0: ingen 1: säkerhetsgrupper och Azure AD-roller, 2: reserverade och 4: reserverat. Ange bitmask till 7 får alla säkerhetsgrupper, distributionsgrupper och Azure AD directory roller som den inloggade användaren är medlem i.      |
|optionalClaims     |  sträng       |     null    |    Valfria anspråk i token som returnerades av säkerhetstokentjänsten för den här specifika appen.     |
|acceptMappedClaims    |      Booleskt värde   | true        |    Om det här värdet anges till true, kan ett program att använda anspråk mappning utan att ange en anpassad signeringsnyckeln.|
|Startsida     |  sträng       |http:<i></i>//MyRegistererdApp         |    URL till programmets startsida.     |
|identifierUris     |  Strängmatris       | http:<i></i>//MyRegistererdApp        |   Användardefinierade URI(s) som unikt identifierar ett webbprogram i sin Azure AD-klient eller i en anpassad domän för verifierade om programmet är flera innehavare.      |
|keyCredentials     |   Typ av matris      |   [{<br>&nbsp;”customKeyIdentifier”: null.<br>”endDate” ”: 2018-09-13T00:00:00Z”,<br>”keyId” ”:\<guid >”,<br>”startDate” ”: 2017-09-12T00:00:00Z”,<br>”typ”: ”AsymmetricX509Cert”<br>”användning”: ”Verifiera”<br>”värde”: null<br>}]      |   Den här egenskapen innehåller referenser till programmet tilldelade autentiseringsuppgifter, string-baserade delade hemligheter och X.509-certifikat.  Dessa autentiseringsuppgifter komma till användning när du begär åtkomst-token (när appen fungerar som en klient i stället som som resurs).     |
|knownClientApplications     |     Typ av matris    |    [guid]     |     Värdet används för sammanföra medgivande om du har en lösning som innehåller två delar, ett klientprogram och en anpassad webbplats API-program. Om du anger appID av klientprogrammet i det här värdet kan måste användaren bara godkänna en gång till klientprogrammet. Azure AD vet att principer för klienten innebär implicit principer för webb-API och kommer automatiskt att etablera tjänstens huvudnamn för både klient- och webb-API på samma gång (både klienten och webb-API-program måste vara registrerad i samma innehavaren).|
|logoutUrl     |   sträng      |     http:<i></i>//MyRegisteredAppLogout    |   Logga ut från programmet URL-adress.      |
|oauth2AllowImplicitFlow     |   Booleskt värde      |  false       |       Anger om det här webbprogrammet kan begära OAuth2.0 implicita flödet för token. Standardvärdet är FALSKT. Det här används för webbläsarbaserade appar som Javascript-ensidesappar. |
|oauth2AllowUrlPathMatching     |   Booleskt värde      |  false       |   Anger om, Azure AD som en del av OAuth 2.0 token-förfrågningar, ska tillåta sökvägsmatchning för omdirigerings-URI mot programmets replyUrls. Standardvärdet är FALSKT.      |
|oauth2Permissions     | Typ av matris         |      [{<br>”adminConsentDescription”: ”Tillåt programmet åtkomst till resurser å den inloggade användaren vägnar”.,<br>”adminConsentDisplayName”: ”åtkomst resource1”<br>”id” ”:\<guid >”,<br>”isEnabled”: true<br>”typ”: ”användare”<br>”userConsentDescription”: ”Tillåt programmet åtkomst resource1 å dina vägnar”.,<br>”userConsentDisplayName”: ”komma åt resurser”<br>”värde”: ”user_impersonation”<br>}]   |  Insamling av behörighetsomfattningen för OAuth 2.0 som webb-API (resurs)-program visar för klientprogram. Klientprogram kan bevilja dessa behörighetsomfattningen under medgivande. |
|oauth2RequiredPostResponse     | Booleskt värde        |    false     |      Anger om, Azure AD som en del av OAuth 2.0 token-förfrågningar, ska tillåta POST-begäranden, till skillnad från GET-begäranden. Standardvärdet är FALSKT, vilket innebär att GET-begäranden ska tillåtas.   
|objekt-ID     | Strängen för meddelandealternatividentifieraren        |     ""    |    Unik identifierare för programmet i katalogen.  Detta är inte det ID som används för att identifiera appen i någon transaktion för protokollet.  Användaren är refererar till objektet i directory-frågor.|
|passwordCredentials     | Typ av matris        |   [{<br>”customKeyIdentifier”: null.<br>”endDate” ”: 2018-10-19T17:59:59.6521653Z”,<br>”keyId” ”:\<guid >”,<br>”startDate” ”: 2016-10-19T17:59:59.6521653Z”,<br>”värde”: null<br>}]      |    Se beskrivningen för egenskapen keyCredentials.     |
|PublicClient     |  Booleskt värde       |      false   | Anger om ett program är en offentlig klient (till exempel ett installerat program som körs på en mobil enhet). Standardvärdet är false.        |
|supportsConvergence     |  Booleskt värde       |   false      | Den här egenskapen bör inte redigeras.  Acceptera standardvärdet.        |
|replyUrls     |  Strängmatris       |   http:<i></i>//localhost     |  Flera värden egenskapen innehåller listan över registrerade redirect_uri värden som Azure AD tar emot som mål när returining token. |
|RequiredResourceAccess     |     Typ av matris    |    [{<br>”resourceAppId”: ”00000002-0000-0000-c000-000000000000”<br>”resourceAccess”: [{<br>&nbsp;&nbsp;&nbsp;&nbsp;”id”: ”311a71cc-e848-46a1-bdf8-97ff7156d8e6”<br>&nbsp;&nbsp;&nbsp;&nbsp;”typ”: ”omfång”<br>&nbsp;&nbsp;}]<br>}]     |   Anger resurser som det här programmet kräver åtkomst till och uppsättning behörighetsomfattningen för OAuth och roller för programmet som krävs under var och en av dessa resurser. Den här före konfiguration av nödvändiga resursåtkomst enheter medgivande-upplevelse.|
|resourceAppId     |    Strängen för meddelandealternatividentifieraren     |  ""      |   Den unika identifieraren för den resurs som programmet kräver åtkomst till. Det här värdet ska vara lika med appId som deklarerats i målprogrammet för resursen.     |
|resourceAccess     |  Typ av matris       | Se exempelvärdet för egenskapen requiredResourceAccess.        |   Listan över OAuth2.0 behörighetsomfattningen och app-roller som programmet kräver för den angivna resursen (innehåller värdena ID och typ av de angivna resurserna)        |
|samlMetadataUrl|sträng|http:<i></i>//MyRegisteredAppSAMLMetadata|URL för SAML-metadata för programmet.| 

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

