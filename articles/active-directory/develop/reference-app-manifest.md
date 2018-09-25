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
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 74bcd1e795186eba652d2da986c290e1989dd1d1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041582"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory-appmanifestet

Appar som integreras med Azure Active Directory (AD Azure) måste registreras med en Azure AD-klient. Du kan konfigurera appen i den [Azure-portalen](https://portal.azure.com) genom att välja **appregistreringar** under **Azure Active Directory**, välja den app du vill konfigurera, och sedan att välja **Manifest**.

## <a name="manifest-reference"></a>Manifest-referens

> [!NOTE]
> Om du inte ser den **exempelvärde** kolumn efter den **beskrivning**, Maximera ditt webbläsarfönster och rulla/sveper tills du ser den **exempelvärde** kolumn.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| Nyckel  | Värdetyp | Beskrivning  | Exempelvärde |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Kan ha värdet null Int32 | Anger den godkända åtkomst-token versionen för den aktuella API-resursen. Möjliga värden är 1, 2, null. Standardvärdet är null vilket kommer att behandlas som 2. | `2` |
| `allowPublicClient` | boolesk | Anger vilken typ av återställning program. Azure AD härleder programtypen från replyUrlsWithType som standard. Det finns vissa scenarier där Azure AD inte kan fastställa typen av app klienten (t.ex. [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) flöde där HTTP-begäran sker utan en URL-omdirigering). I sådana fall tolkar Azure AD programtypen baserat på värdet för den här egenskapen. Om det här värdet anges till true återställningsplats programtypen har angetts som offentlig klient, till exempel en installerad app som körs på en mobil enhet. Standardvärdet är false, vilket innebär att återställningsplats programtypen är konfidentiell klient, till exempel webbapp. | `false` |
| `appId` | Strängen för meddelandealternatividentifieraren | Anger den unika identifieraren för den app som har tilldelats en app av Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Typ av matris | Anger samlingen av roller som en app kan deklarera. Dessa roller kan tilldelas användare, grupper eller tjänstens huvudnamn. Fler exempel och information i [Lägg till roller i ditt program och tar emot dem i token](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `groupMembershipClaims` | sträng | En bitmask som konfigurerar den `groups` anspråk som utfärdats i en användare eller OAuth 2.0-åtkomsttoken som förväntar sig att appen. Värdena är:<br>0: ingen<br>1: säkerhetsgrupper och Azure AD-roller<br>2: reserverad<br>4: reserverad<br>Ställa in bitmask 7 får alla säkerhetsgrupper, distributionsgrupper och Azure AD-katalogroller som den inloggade användaren är medlem i. | `1` |
| `optionalClaims` | sträng | De valfria anspråk som returneras i token av säkerhetstokentjänsten för den här specifika appen. Mer information finns i [valfria anspråk](active-directory-optional-claims.md). | `null` |
| `id` | Strängen för meddelandealternatividentifieraren | Den unika identifieraren för appen i katalogen. Detta ID är inte den identifierare som används för att identifiera appen i alla protokoll-transaktioner. Den används för refererar till objektet i directory-frågor. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | Strängmatris | Användardefinierade URI(s) som unikt identifierar en webbapp i sin Azure AD-klient eller i en verifierad anpassad domän om appen är flera innehavare. | <code>[<br>&nbsp;&nbsp;"https://MyRegistererdApp"<br>]</code> |
| `informationalUrls` | sträng | Anger länkar till appens användningsvillkor och sekretesspolicy. Användningsvillkor och sekretesspolicy exponeras för användare via medgivande användarupplevelsen. Mer information finns i [så här: lägga till användningsvillkor och sekretesspolicy för registrerad Azure AD-appar](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Typ av matris | Innehåller referenser till app-tilldelade autentiseringsuppgifter, sträng-baserade delade hemligheter och X.509-certifikat. Dessa autentiseringsuppgifter används när du begär åtkomsttoken (när appen fungerar som en klient i stället som som resurs). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | Typ av matris | Används för sammanföra medgivande om du har en lösning som består av två delar: en klientapp och en anpassad webb-API-app. Om du anger appID för klientappen i det här värdet behöver användaren bara godkänna en gång klientappen. Azure AD vet att medgivandedialogen klienten innebär implicit medgivandedialogen till webb-API och kommer automatiskt att etablera tjänsthuvudnamn för både klient- och webb-API på samma gång. Både klienten och webb-API-app måste vara registrerad i samma klientorganisation. | `[GUID]` |
| `logoUrl` | sträng | Läsa värdet som pekar på CDN-URL: en till logotyp som laddades upp i portalen. | `https://MyRegisteredAppLogo` |
| `logoutUrl` | sträng | URL till logga ut från appen. | `https://MyRegisteredAppLogout` |
| `name` | sträng | Visningsnamn för appen. | `MyRegisteredApp` |
| `oauth2AllowImplicitFlow` | boolesk | Anger om det här webbprogrammet kan begära OAuth2.0 implicit flöde åtkomsttoken. Standardvärdet är FALSKT. Den här flaggan används för webbläsarbaserade appar som Javascript enkelsidiga appar. Mer information finns i Ange `OAuth 2.0 implicit grant flow` i innehållsförteckningen och finns i avsnitt om implicit flöde. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | boolesk | Anger om det här webbprogrammet kan begära OAuth2.0 implicit flöde ID-token. Standardvärdet är FALSKT. Den här flaggan används för webbläsarbaserade appar som Javascript enkelsidiga appar. | `false` |
| `oauth2Permissions` | Typ av matris | Anger samlingen av OAuth 2.0 behörighetsomfattningar som webb-API (resurs)-app som visar på klientappar. De här behörighetsomfattningarna kan ges klientprogram under medgivande. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"u| Anger samlingen av OAuth 2.0 behörighetsomfattningar som webb-API (resurs)-app som visar på klientappar. De här behörighetsomfattningarna kan ges klientprogram under medgivande. ser_impersonation ”<br>&nbsp;&nbsp;}<br>]</code> |
| `oauth2RequiredPostResponse` | boolesk | Anger om, Azure AD som en del av OAuth 2.0-tokenbegäranden, ska tillåta POST-förfrågningar, till skillnad från GET-begäranden. Standardvärdet är FALSKT, som anger att GET-begäranden ska tillåtas. | `false` |
| `parentalControlSettings` | sträng | `countriesBlockedForMinors` Anger de länder där appen är blockerad för minderåriga.<br>`legalAgeGroupRule` Anger den juridiska avdelningen ålder regel som gäller för användare av appen. Kan anges till `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`, eller `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Typ av matris | Se beskrivningen av den `keyCredentials` egenskapen. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Typ av matris | Visar en lista över program och behörigheter som krävs för medgivande. Kräver en administratör har angett samtycke till programmet. preAuthorizedApplications kräver inte användaren att godkänna behörigheterna som krävs. Behörigheterna i preAuthorizedApplications kräver inte användarens medgivande. Men kräver ytterligare begärda behörigheter som inte listas i preAuthorizedApplications användarens medgivande. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `replyUrlsWithType` | Strängmatris | Den här egenskapen för flera värden innehåller listan över registrerade redirect_uri värden som Azure AD accepterar som mål när returnerar token. Varje uri-värdet ska innehålla ett tillhörande app TYPVÄRDE. Värden som stöds är: `Web`, `InstalledClient`. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Typ av matris | Med dynamisk medgivande `requiredResourceAccess` Driver administratörsupplevelsen medgivande och godkännande användarupplevelsen för användare som använder statiska medgivande. Detta dock inte driva medgivande användarupplevelsen för vanliga fall.<br>`resourceAppId` är den unika identifieraren för den resurs som appen kräver åtkomst till. Det här värdet ska vara lika med appId deklarerats i målappen för resursen.<br>`resourceAccess` är en matris som innehåller behörighetsomfattningar för OAuth2.0 och roller som appen kräver för den angivna resursen. Innehåller den `id` och `type` värdena för de angivna resurserna. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | sträng | URL till SAML-metadata för appen. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | sträng | Anger Webbadressen till appens startsida. | `https://MyRegisteredApp` |
| `signInAudience` | sträng | Anger vilka microsoft-konton stöds för det aktuella programmet. Värden som stöds är:<ul><li>**AzureADMyOrg** -användare med ett Microsoft arbets- eller skolkonto i min organisation är Azure AD-klient (d.v.s. enskild klient)</li><li>**AzureADMultipleOrgs** -användare med ett Microsoft arbets- eller skolkonto i alla organisationer Azure AD-klient (t.ex. flera innehavare)</li> <li>**AzureADandPersonalMicrosoftAccount** -användare med ett personligt microsoftkonto eller ett arbets- eller skolkonto konto i alla organisationer Azure AD-klient</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | Strängmatris | Anpassade strängar som kan användas för att kategorisera och identifiera programmet. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

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
