---
title: Förstå appmanifestet Azure Active Directory | Microsoft Docs
description: Detaljerad täckning av appmanifestet Azure Active Directory, som representerar ett programs identitet konfigurationen i en Azure AD-klient och används för att underlätta OAuth auktorisering, samtycke upplevelsen med mera.
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
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127028"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory-appmanifestet

Appar som integreras med Azure Active Directory (AD Azure) måste registreras med en Azure AD-klient. Du kan konfigurera appen i den [Azure-portalen](https://portal.azure.com) genom att välja **Azure Active Directory**, välja den app du vill konfigurera och sedan välja **Manifest**.

## <a name="manifest-reference"></a>Manifest-referens

> [!NOTE]
> Om du inte kan se beskrivningarna, Maximera ditt webbläsarfönster- eller bläddra/Svep om du vill se beskrivningarna.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| Nyckel  | Värdetyp | Exempelvärde | Beskrivning  |
|---------|---------|---------|---------|
| `appID` | Strängen för meddelandealternatividentifieraren | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | Anger den unika identifieraren för den app som har tilldelats en app av Azure AD. |
| `appRoles` | Typ av matris | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | Anger samlingen av roller som en app kan deklarera. Dessa roller kan tilldelas användare, grupper eller tjänstens huvudnamn. |
| `availableToOtherTenants`| boolesk | `true` | Om det här värdet anges till SANT, appen är tillgänglig för andra klienter. Den är registrerad i om inställt på false, appen är endast tillgänglig för innehavaren. Mer information finns i [så här: Logga in alla Azure AD-användare med hjälp av mönstret app för flera klienter](howto-convert-app-to-be-multi-tenant.md). |
| `displayName` | sträng | `MyRegisteredApp` | Visningsnamn för appen. |
| `errorURL` | sträng | `http://MyRegisteredAppError` | URL för fel som påträffats i en app. |
| `groupMembershipClaims` | sträng | `1` | En bitmask som konfigurerar den `groups` anspråk som utfärdats i en användare eller OAuth 2.0-åtkomsttoken som förväntar sig att appen. Värdena är:<br>0: ingen<br>1: säkerhetsgrupper och Azure AD-roller<br>2: reserverad<br>4: reserverad<br>Ställa in bitmask 7 får alla säkerhetsgrupper, distributionsgrupper och Azure AD-katalogroller som den inloggade användaren är medlem i. |
| `optionalClaims` | sträng | `null` | De valfria anspråk som returneras i token av säkerhetstokentjänsten för den här specifika appen. Mer information finns i [valfria anspråk](active-directory-optional-claims.md). |
| `acceptMappedClaims` | boolesk | `true` | Om det här värdet anges till `true`, tillåter att appen använder Anspråksmappning utan att ange en anpassad signeringsnyckel. |
| `homepage` | sträng | `http://MyRegisteredApp` | Anger Webbadressen till appens startsida. |
| `informationalUrls` | sträng | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | Anger länkar till appens användningsvillkor och sekretesspolicy. Användningsvillkor och sekretesspolicy exponeras för användare via medgivande användarupplevelsen. Mer information finns i [så här: lägga till användningsvillkor och sekretesspolicy för registrerad Azure AD-appar](howto-add-terms-of-service-privacy-statement.md). |
| `identifierUris` | Strängmatris | `http://MyRegistererdApp` | Användardefinierade URI(s) som unikt identifierar en webbapp i sin Azure AD-klient eller i en verifierad anpassad domän om appen är flera innehavare. |
| `keyCredentials` | Typ av matris | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | Innehåller referenser till app-tilldelade autentiseringsuppgifter, sträng-baserade delade hemligheter och X.509-certifikat. Dessa autentiseringsuppgifter används när du begär åtkomsttoken (när appen fungerar som en klient i stället som som resurs). |
| `knownClientApplications` | Typ av matris | `[GUID]` | Används för sammanföra medgivande om du har en lösning som består av två delar: en klientapp och en anpassad webb-API-app. Om du anger appID för klientappen i det här värdet behöver användaren bara godkänna en gång klientappen. Azure AD vet att medgivandedialogen klienten innebär implicit medgivandedialogen till webb-API och kommer automatiskt att etablera tjänsthuvudnamn för både klient- och webb-API på samma gång. Både klienten och webb-API-app måste vara registrerad i samma klientorganisation. |
| `logoutUrl` | sträng | `http://MyRegisteredAppLogout` | URL till logga ut från appen. |
| `oauth2AllowImplicitFlow` | boolesk | `false` | Anger om det här webbprogrammet kan begära OAuth2.0 implicit flöde token. Standardvärdet är FALSKT. Den här flaggan används för webbläsarbaserade appar som appar med Javascript en sida. |
| `oauth2AllowUrlPathMatching` | boolesk | `false` | Anger om, Azure AD som en del av OAuth 2.0-tokenbegäranden, ska tillåta sökvägsmatchning för omdirigerings-URI mot appens svarsurl: er. Standardvärdet är FALSKT. |
| `oauth2Permissions` | Typ av matris | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | Anger samlingen av OAuth 2.0 behörighetsomfattningar som webb-API (resurs)-app som visar på klientappar. De här behörighetsomfattningarna kan ges klientprogram under medgivande. |
| `oauth2RequiredPostResponse` | boolesk | `false` | Anger om, Azure AD som en del av OAuth 2.0-tokenbegäranden, ska tillåta POST-förfrågningar, till skillnad från GET-begäranden. Standardvärdet är FALSKT, som anger att GET-begäranden ska tillåtas. |
| `objectId` | Strängen för meddelandealternatividentifieraren | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | Den unika identifieraren för appen i katalogen. Detta ID är inte den identifierare som används för att identifiera appen i alla protokoll-transaktioner. Den används för refererar till objektet i directory-frågor. |
| `parentalControlSettings` | sträng | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` Anger de länder där appen är blockerad för minderåriga.<br>`legalAgeGroupRule` Anger den juridiska avdelningen ålder regel som gäller för användare av appen. Kan anges till `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`, eller `BlockMinors`.  |
| `passwordCredentials` | Typ av matris | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | Se beskrivningen av den `keyCredentials` egenskapen. |
| `publicClient` | boolesk | `false` | Anger om en app är en offentlig klient, till exempel en installerad app som körs på en mobil enhet. Standardvärdet är FALSKT. |
| `replyUrls` | Strängmatris | `"http://localhost"` | Den här egenskapen för flera värden innehåller listan över registrerade redirect_uri värden som Azure AD accepterar som mål när returnerar token. |
| `requiredResourceAccess` | Typ av matris | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | Med dynamisk medgivande `requiredResourceAccess` Driver administratörsupplevelsen medgivande och godkännande användarupplevelsen för användare som använder statiska medgivande. Detta dock inte driva medgivande användarupplevelsen för vanliga fall.<br>`resourceAppId` är den unika identifieraren för den resurs som appen kräver åtkomst till. Det här värdet ska vara lika med appId deklarerats i målappen för resursen.<br>`resourceAccess` är en matris som innehåller behörighetsomfattningar för OAuth2.0 och roller som appen kräver för den angivna resursen. Innehåller den `id` och `type` värdena för de angivna resurserna. |
| `samlMetadataUrl` | sträng | `http://MyRegisteredAppSAMLMetadata` | URL till SAML-metadata för appen. |

## <a name="next-steps"></a>Nästa steg

* Mer information om förhållandet mellan program och tjänstens huvudnamn objekt i en app finns i [program och tjänstobjekt i Azure AD](app-objects-and-service-principals.md).
* Se den [ordlista för Azure AD-utvecklare](developer-glossary.md) för definitioner av några av de viktigaste begreppen för utvecklare av Azure Active Directory (AD).

Använd följande avsnitt för kommentarer för att ge feedback som hjälper dig att förfina och forma vårt innehåll.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

