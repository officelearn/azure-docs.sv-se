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
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b7a383eb8df8fcfd358bce226168e5fbeb42f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298299"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory-appmanifestet

Application manifest innehåller en definition av alla attribut för ett programobjekt i Microsoft identity-plattformen. Det fungerar också som en mekanism för att uppdatera programobjektet. Mer information om entiteten programmet och dess schema, finns det [Graph-API-program entity-dokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Du kan konfigurera attribut för en app via Azure portal eller programmässigt med [REST API](https://docs.microsoft.com/en-us/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) eller [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0#applications). Det finns dock vissa scenarier där du behöver redigera appmanifestet om du vill konfigurera en app-attributet. Några vanliga scenarier:

* Om du har registrerat appen som Azure AD flera innehavare och personliga Microsoft-konton kan du inte ändra stöds Microsoft-konton i Användargränssnittet. Du måste i stället använda redigeringsprogrammet för applikationsmanifestet för att ändra kontotyp som stöds.
* Om du behöver definiera behörigheter och roller som har stöd för din app måste du ändra programmanifestet.

## <a name="configure-the-app-manifest"></a>Konfigurera appmanifestet

Konfigurera applikationsmanifestet:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj den **Azure Active Directory** tjänsten och välj sedan **appregistreringar** eller **appregistreringar (förhandsversion)**.
1. Välj den app som du vill konfigurera.
1. På appens **översiktssida** väljer du avsnittet **Manifest**. En webbaserad redigeringsprogrammet för applikationsmanifestet öppnas så att du kan redigera manifestet i portalen. Du kan också markera **hämta** att redigera manifestet lokalt och sedan använda **överför** för att återställa den till ditt program.

## <a name="manifest-reference"></a>Manifest-referens

> [!NOTE]
> Om du inte ser den **exempelvärde** kolumn efter den **beskrivning**, Maximera ditt webbläsarfönster och rulla/sveper tills du ser den **exempelvärde** kolumn.

| Nyckel  | Värdetyp | Beskrivning  | Exempelvärde |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Kan ha värdet null Int32 | Anger vilken åtkomst-token version som förväntas av resursen. Detta ändrar versionen och formatet för JWT produceras oberoende av slutpunkten eller klienten för att begära åtkomst-token.<br/><br/>Den slutpunkt som används, v1.0 eller v2.0, väljs av klienten och påverkar endast versionen av id_tokens. Resurser måste du uttryckligen konfigurera `accesstokenAcceptedVersion` att ange formatet stöds åtkomst-token.<br/><br/>Möjliga värden för `accesstokenAcceptedVersion` är 1, 2 eller null. Om värdet är null, standard detta 1, vilket motsvarar v1.0-slutpunkten. | `2` |
| `addIns` | Samling | Definierar egna funktionssätt konsumerande tjänsten kan använda för att anropa en app i specifika sammanhang. Program som kan återge filen strömmar kan exempelvis ange egenskapen tilläggsprogram för dess ”FileHandler”-funktioner. Detta gör att tjänster som Office 365 anropa programmet i kontexten för ett dokument som du arbetar på. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Boolean | Anger vilken typ av återställning program. Azure AD härleder programtypen från replyUrlsWithType som standard. Det finns vissa scenarier där Azure AD inte kan fastställa typen av app klienten (t.ex. [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) flöde där HTTP-begäran sker utan en URL-omdirigering). I sådana fall tolkar Azure AD programtypen baserat på värdet för den här egenskapen. Om det här värdet anges till true återställningsplats programtypen har angetts som offentlig klient, till exempel en installerad app som körs på en mobil enhet. Standardvärdet är false, vilket innebär att återställningsplats programtypen är konfidentiell klient, till exempel webbapp. | `false` |
| `availableToOtherTenants` | Boolean | SANT om programmet delas med andra klienter; Annars, FALSKT. <br><br> _Obs! Detta ersätts av `signInAudience` i den [Appregistreringar (förhandsversion)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) upplevelse._ | |
| `appId` | String | Anger den unika identifieraren för den app som har tilldelats en app av Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Samling | Anger samlingen av roller som en app kan deklarera. Dessa roller kan tilldelas användare, grupper eller tjänstens huvudnamn. Fler exempel och information i [Lägg till roller i ditt program och tar emot dem i token](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | String | Visningsnamn för appen. <br><br> _Obs! Detta ersätts av `name` i den [Appregistreringar (förhandsversion)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) upplevelse._ | `"MyRegisteredApp"` |
| `errorUrl` | String | som inte stöds. | |
| `groupMembershipClaims` | String | Konfigurerar den `groups` anspråk som utfärdats i en användare eller OAuth 2.0-åtkomsttoken som förväntar sig att appen. Använd någon av följande giltiga strängvärden för att ställa in det här attributet:<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (för säkerhetsgrupper och Azure AD-roller)<br/>- `"All"` (detta får alla säkerhetsgrupper, distributionsgrupper och Azure AD-katalogroller som den inloggade användaren är medlem i. | `"SecurityGroup"` |
| `homepage` | String | URL:en till programmets hemsida. <br><br> _Obs! Detta ersätts av `signInUrl` i den [Appregistreringar (förhandsversion)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) upplevelse._ | `"https://MyRegisteredApp"` |
| `objectId` | String | Den unika identifieraren för appen i katalogen. <br><br> _Obs! Detta ersätts av `id` i den [Appregistreringar (förhandsversion)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) upplevelse._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | String | De valfria anspråk som returneras i token av säkerhetstokentjänsten för den här specifika appen.<br>Appar som stöder både personliga konton och Azure AD (registrerad via portalen för registrering av appen) kan inte använda valfria anspråk för tillfället. Appar som är registrerade för bara Azure AD med v2.0-slutpunkten får valfria anspråk som begärts i manifestet. Mer information finns i [valfria anspråk](active-directory-optional-claims.md). | `null` |
| `id` | String | Den unika identifieraren för appen i katalogen. Detta ID är inte den identifierare som används för att identifiera appen i alla protokoll-transaktioner. Den används för refererar till objektet i directory-frågor. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | Användardefinierade URI(s) som unikt identifierar en webbapp i sin Azure AD-klient eller i en verifierad anpassad domän om appen är flera innehavare. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | String | Anger länkar till appens användningsvillkor och sekretesspolicy. Användningsvillkor och sekretesspolicy exponeras för användare via medgivande användarupplevelsen. Mer information finns i [så här: Lägga till användningsvillkor och sekretesspolicy för registrerad Azure AD-appar](howto-add-terms-of-service-privacy-statement.md). | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Samling | Innehåller referenser till app-tilldelade autentiseringsuppgifter, sträng-baserade delade hemligheter och X.509-certifikat. Dessa autentiseringsuppgifter används när du begär åtkomsttoken (när appen fungerar som en klient i stället som som resurs). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | Används för sammanföra medgivande om du har en lösning som består av två delar: en klientapp och en anpassad webb-API-app. Om du anger appID för klientappen i det här värdet behöver användaren bara godkänna en gång klientappen. Azure AD vet att medgivandedialogen klienten innebär implicit medgivandedialogen till webb-API och kommer automatiskt att etablera tjänsthuvudnamn för både klient- och webb-API på samma gång. Både klienten och webb-API-app måste vara registrerad i samma klientorganisation. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | String | Läsa värdet som pekar på CDN-URL: en till logotyp som laddades upp i portalen. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | String | URL till logga ut från appen. | `"https://MyRegisteredAppLogout"` |
| `name` | String | Visningsnamn för appen. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Boolean | Anger om det här webbprogrammet kan begära OAuth2.0 implicit flöde åtkomsttoken. Standardvärdet är FALSKT. Den här flaggan används för webbläsarbaserade appar som Javascript enkelsidiga appar. Mer information finns i Ange `OAuth 2.0 implicit grant flow` i innehållsförteckningen och finns i avsnitt om implicit flöde. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Boolean | Anger om det här webbprogrammet kan begära OAuth2.0 implicit flöde ID-token. Standardvärdet är FALSKT. Den här flaggan används för webbläsarbaserade appar som Javascript enkelsidiga appar. | `false` |
| `oauth2Permissions` | Samling | Anger samlingen av OAuth 2.0 behörighetsomfattningar som webb-API (resurs)-app som visar på klientappar. De här behörighetsomfattningarna kan ges klientprogram under medgivande. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Boolean | Anger om, Azure AD som en del av OAuth 2.0-tokenbegäranden, ska tillåta POST-förfrågningar, till skillnad från GET-begäranden. Standardvärdet är FALSKT, som anger att GET-begäranden ska tillåtas. | `false` |
| `parentalControlSettings` | String | `countriesBlockedForMinors` Anger de länder där appen är blockerad för minderåriga.<br>`legalAgeGroupRule` Anger den juridiska avdelningen ålder regel som gäller för användare av appen. Kan anges till `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`, eller `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Samling | Se beskrivningen av den `keyCredentials` egenskapen. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Samling | Visar en lista över program och behörigheter som krävs för medgivande. Kräver en administratör har angett samtycke till programmet. preAuthorizedApplications kräver inte användaren att godkänna behörigheterna som krävs. Behörigheterna i preAuthorizedApplications kräver inte användarens medgivande. Men kräver ytterligare begärda behörigheter som inte listas i preAuthorizedApplications användarens medgivande. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Boolean | Anger om det här programmet är en offentlig klient (till exempel ett installerat program som körs på en mobil enhet). <br><br> _Obs! Detta ersätts av `allowPublicClient` i den [Appregistreringar (förhandsversion)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) upplevelse._ | |
| `publisherDomain` | String | Verifierade publisher-domän för programmet. Skrivskyddat läge. | https://www.contoso.com |
| `replyUrls` | Strängmatris | Den här egenskapen för flera värden innehåller listan över registrerade redirect_uri värden som Azure AD accepterar som mål när returnerar token. <br><br> _Obs! Detta ersätts av `replyUrlsWithType` i den [Appregistreringar (förhandsversion)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) upplevelse._ | |
| `replyUrlsWithType` | Samling | Den här egenskapen för flera värden innehåller listan över registrerade redirect_uri värden som Azure AD accepterar som mål när returnerar token. Varje uri-värdet ska innehålla ett tillhörande app TYPVÄRDE. Värden som stöds är: `Web`, `InstalledClient`. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Samling | Med dynamisk medgivande `requiredResourceAccess` Driver administratörsupplevelsen medgivande och godkännande användarupplevelsen för användare som använder statiska medgivande. Detta dock inte driva medgivande användarupplevelsen för vanliga fall.<br>`resourceAppId` är den unika identifieraren för den resurs som appen kräver åtkomst till. Det här värdet ska vara lika med appId deklarerats i målappen för resursen.<br>`resourceAccess` är en matris som innehåller behörighetsomfattningar för OAuth2.0 och roller som appen kräver för den angivna resursen. Innehåller den `id` och `type` värdena för de angivna resurserna. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | String | URL till SAML-metadata för appen. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | String | Anger Webbadressen till appens startsida. | `https://MyRegisteredApp` |
| `signInAudience` | String | Anger vilka Microsoft-konton stöds för det aktuella programmet. Värden som stöds är:<ul><li>**AzureADMyOrg** -användare med ett Microsoft arbets- eller skolkonto i min organisation är Azure AD-klient (d.v.s. enskild klient)</li><li>**AzureADMultipleOrgs** -användare med ett Microsoft arbets- eller skolkonto i alla organisationer Azure AD-klient (t.ex. flera innehavare)</li> <li>**AzureADandPersonalMicrosoftAccount** -användare med ett personligt microsoftkonto eller ett arbets- eller skolkonto konto i alla organisationer Azure AD-klient</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | Anpassade strängar som kan användas för att kategorisera och identifiera programmet. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Vanliga problem

### <a name="manifest-limits"></a>Manifest gränser

Ett programmanifest har flera attribut som kallas samlingar; till exempel approles, keycredentials, knownClientApplications, identifieruris finns, rediretUris, requiredResourceAccess och oauth2Permissions. I hela appen manifestet för alla program, har det totala antalet poster i alla samlingar som kombineras har högst 1200. Om du redan har 100 omdirigering URI: er som anges i applikationsmanifestet, är endast vänster med poster som 1100 återstående ska användas för alla andra samlingar som kombineras som utgör manifestet.

> [!NOTE]
> Om du försöker lägga till fler än 1200 poster i applikationsmanifestet, kanske du ser ett fel **”det gick inte att uppdatera programmet xxxxxx. Felinformation: Storleken på manifestet har överskridit gränsen. Minska antalet värden och försök igen med din begäran ”.**

### <a name="unsupported-attributes"></a>Attribut som inte stöds

Applikationsmanifestet representerar schemat för den underliggande programmodellen i Azure AD. Eftersom det underliggande schemat utvecklas kommer redigeringsprogrammet för applikationsmanifestet att uppdateras för att återspegla det nya schemat från tid till annan. Därför kan märka du att nya attribut som visas i programmanifestet. I sällsynta fall kanske du märker en syntaktiska eller semantisk ändring i de befintliga attribut eller så finns det ett attribut som fanns tidigare inte stöds längre. Exempelvis visas nya attribut i den [appregistreringar (förhandsversion)](https://developer.microsoft.com/graph/blogs/new-app-registration/) som är kända med ett annat namn på App-registreringar (GA)-upplevelse.


| App-registreringar (GA)    | App-registreringar (förhandsversion) |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Beskrivningar för dessa attribut finns i den [manifest referens](#manifest-reference) avsnittet.

När du försöker ladda upp ett tidigare nedladdat manifest, visas något av följande fel. Detta beror sannolikt redigeringsprogrammet för applikationsmanifestet stöder nu en nyare version av schema, som inte matchar det som du försöker ladda upp.

- ”**Det gick inte att uppdatera xxxxxx program. Felinformation: Ogiltigt objekt-ID: ”odefinierad'. [].** "
- ”**Det gick inte att uppdatera xxxxxx program. Felinformation: En eller flera egenskapsvärden som angetts är ogiltiga. [].** "
- ”**Det gick inte att uppdatera xxxxxx program. Felinformation: Inte tillåtet för att ange availableToOtherTenants i den här api-version för uppdateringen. [].** "
- ”**Det gick inte att uppdatera xxxxxx program. Felinformation: Uppdateringar ”svarsurl: er”-egenskapen är inte tillåtet för det här programmet. Använd egenskapen 'replyUrlsWithType' i stället. [].** "
- ”**Det gick inte att uppdatera xxxxxx program. Felinformation: Ett värde utan ett typnamn har hittats och inga förväntad typ är tillgängliga. När modellen har angetts måste alla värden i nyttolasten har en typ som kan vara antingen angetts i nyttolasten, uttryckligen av anroparen eller implicit härleds från överordnat värde. []**"

När du ser något av dessa fel, rekommenderar vi följande:

1. Redigera attribut individuellt i redigeringsprogrammet för applikationsmanifestet i stället för att ladda upp ett tidigare nedladdat manifest. Använd den [manifest referens](#manifest-reference) tabell för att förstå syntax och semantik gamla och nya attribut så att du har kan redigera de attribut som du är intresserad av. 
1. Om arbetsflödet kräver att du kan spara i manifesten i centrallagret för senare användning, föreslår vi att rebasing sparade manifesten i databasen med den som du ser i den **appregistreringar (förhandsversion)** upplevelse.

## <a name="next-steps"></a>Nästa steg

* Mer information om förhållandet mellan program och tjänstens huvudnamn objekt i en app finns i [program och tjänstobjekt i Azure AD](app-objects-and-service-principals.md).
* Se den [ordlista för Microsoft identity-plattformen utvecklare](developer-glossary.md) för definitioner av några av de viktigaste Microsoft identity-plattform developer begreppen.

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
