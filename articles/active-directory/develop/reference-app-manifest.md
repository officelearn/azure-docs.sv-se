---
title: Förstå Azure Active Directory-appens manifest | Microsoft Docs
description: Detaljerad täckning av Azure Active Directory app-manifestet som representerar ett programs identitets konfiguration i en Azure AD-klient och används för att under lätta OAuth-auktorisering, medgivande upplevelse och mycket annat.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: a665f2ffe3ac2d27fb4e4403922c72520dfb37e8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834876"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory app-manifest

Applikations manifestet innehåller en definition av alla attribut för ett program objekt i Microsoft Identity Platform. Den fungerar också som en mekanism för att uppdatera programobjektet. Mer information om programentiteten och dess schema finns i dokumentationen för [Graph API application Entity](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Du kan konfigurera en Apps attribut via Azure Portal eller program mässigt med hjälp av [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) eller [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Det finns dock vissa scenarier där du måste redigera appens manifest för att konfigurera appens attribut. Några vanliga scenarier:

* Om du har registrerat appen som Azure AD-konto för flera klienter och personliga Microsoft-konton kan du inte ändra de Microsoft-konton som stöds i användar gränssnittet. I stället måste du använda program manifest redigeraren för att ändra den konto typ som stöds.
* Om du behöver definiera behörigheter och roller som appen stöder måste du ändra applikations manifestet.

## <a name="configure-the-app-manifest"></a>Konfigurera app-manifestet

Så här konfigurerar du applikations manifestet:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj tjänsten **Azure Active Directory** och välj sedan **Appregistreringar**.
1. Välj den app som du vill konfigurera.
1. På appens **översiktssida** väljer du avsnittet **Manifest**. En webbaserad manifest redigerare öppnas, så att du kan redigera manifestet i portalen. Alternativt kan du välja **Ladda ned** för att redigera manifestet lokalt och sedan använda **upload** för att tillämpa det igen på ditt program.

## <a name="manifest-reference"></a>Manifest referens

> [!NOTE]
> Om du inte kan se kolumnen **exempel värde** efter **beskrivningen**kan du maximera webbläsarfönstret och rulla/svep tills du ser kolumnen **exempel värde** .

| Nyckel  | Värdetyp | Beskrivning  | Exempelvärde |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Nullable Int32 | Anger den åtkomsttoken som förväntas av resursen. Detta ändrar versionen och formatet på det JWT-tillverkade oberoende av slut punkten eller klienten som användes för att begära åtkomsttoken.<br/><br/>Slut punkten som används, v 1.0 eller v 2.0 väljs av klienten och påverkar endast versionen av id_tokens. Resurser måste uttryckligen konfigureras `accesstokenAcceptedVersion` för att ange det åtkomsttoken som stöds.<br/><br/>Möjliga värden `accesstokenAcceptedVersion` är 1, 2 eller null. Om värdet är null, är standardvärdet 1, som motsvarar v 1.0-slutpunkten. <br/><br/>Om `signInAudience` är`AzureADandPersonalMicrosoftAccount`, måste värdet vara`2`  | `2` |
| `addIns` | Collection | Definierar ett anpassat beteende som en förbruknings tjänst kan använda för att anropa en app i vissa sammanhang. Till exempel kan program som kan rendera fil strömmar ange egenskapen addins för "FileHandler"-funktionen. Detta gör att tjänster som Office 365 anropar programmet i kontexten för ett dokument som användaren arbetar med. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Boolesk | Anger typ av återställnings program. Azure AD härleder program typen från replyUrlsWithType som standard. Det finns vissa scenarier där Azure AD inte kan fastställa klientens app-typ (t. ex. [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) flöde där http-begäran sker utan en URL-omdirigering). I dessa fall tolkar Azure AD program typen baserat på värdet för den här egenskapen. Om det här värdet är inställt på Sant anges typ av återställnings program som offentlig klient, till exempel en installerad app som körs på en mobil enhet. Standardvärdet är false, vilket innebär att återställnings program typen är konfidentiell klient, till exempel Web App. | `false` |
| `availableToOtherTenants` | Boolesk | sant om programmet delas med andra klienter. annars FALSE. <br><br> _Obs! Detta är endast tillgängligt i Appregistreringar (bakåtkompatibelt). Ersätts `signInAudience` av i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen._ | |
| `appId` | Sträng | Anger den unika identifieraren för den app som är tilldelad en app av Azure AD. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Collection | Anger den samling roller som en app kan deklarera. Dessa roller kan tilldelas till användare, grupper eller tjänstens huvud namn. Fler exempel och information finns i [lägga till app-roller i ditt program och ta emot dem i token](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | Sträng | Appens visnings namn. <br><br> _Obs! Detta är endast tillgängligt i Appregistreringar (bakåtkompatibelt). Ersätts `name` av i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen._ | `"MyRegisteredApp"` |
| `errorUrl` | Sträng | Som inte stöds. | |
| `groupMembershipClaims` | Sträng | Konfigurerar det `groups` anspråk som utfärdats i en användare eller OAuth 2,0-åtkomsttoken som appen förväntar sig. Ange det här attributet genom att använda något av följande giltiga sträng värden:<br/><br/>- `"None"`<br/>- `"SecurityGroup"`(för säkerhets grupper och Azure AD-roller)<br/>- `"All"`(detta kommer att hämta alla säkerhets grupper, distributions grupper och Azure AD-katalog roller som den inloggade användaren är medlem i. | `"SecurityGroup"` |
| `homepage` | Sträng | URL:en till programmets hemsida. <br><br> _Obs! Detta är endast tillgängligt i Appregistreringar (bakåtkompatibelt). Ersätts `signInUrl` av i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen._ | `"https://MyRegisteredApp"` |
| `objectId` | Sträng | Den unika identifieraren för appen i katalogen. <br><br> _Obs! Detta är endast tillgängligt i Appregistreringar (bakåtkompatibelt). Ersätts `id` av i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen._ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | Sträng | De valfria anspråk som returneras i token av säkerhetstokentjänsten för den här speciella appen.<br>För närvarande kan appar som stöder både personliga konton och Azure AD (registrerade via appens registrerings Portal) inte använda valfria anspråk. Appar som registrerats för bara Azure AD med hjälp av v 2.0-slutpunkten kan dock hämta de valfria anspråk som de begär i manifestet. Mer information finns i [valfria anspråk](active-directory-optional-claims.md). | `null` |
| `id` | Sträng | Den unika identifieraren för appen i katalogen. Detta ID är inte den identifierare som används för att identifiera appen i någon protokoll transaktion. Den används för att referera till objektet i katalog frågor. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | Användardefinierade URI: er som unikt identifierar en webbapp i sin Azure AD-klient eller inom en verifierad anpassad domän om appen är flera innehavare. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | Sträng | Anger länkar till appens användnings villkor och sekretess policy. Användnings villkoren och sekretess policyn är tilldelad till användarna genom användar medgivande upplevelsen. Mer information finns i [How to: Lägg till användnings villkor och sekretess policy för registrerade Azure AD](howto-add-terms-of-service-privacy-statement.md)-appar. | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Collection | Innehåller referenser till app-tilldelade autentiseringsuppgifter, strängbaserade delade hemligheter och X. 509-certifikat. Dessa autentiseringsuppgifter används när du begär åtkomsttoken (när appen fungerar som en klient, i stället för resurs). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | Används för att ge bunt om du har en lösning som innehåller två delar: en klient app och en anpassad webb-API-app. Om du anger appID för-klient programmet i det här värdet behöver användaren bara godkänna en gång till klient programmet. Azure AD känner till att samtycka till klienten innebär att implicit samtycka till webb-API: et och automatiskt etablera tjänstens huvud namn för både klient-och webb-API: n samtidigt. Både klienten och webb-API-appen måste vara registrerade i samma klient organisation. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | Sträng | Skrivskyddat värde som pekar på CDN-URL: en till en logo typ som laddades upp i portalen. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | Sträng | URL: en för att logga ut från appen. | `"https://MyRegisteredAppLogout"` |
| `name` | Sträng | Appens visnings namn. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Boolesk | Anger om den här webbappen kan begära OAuth 2.0 implicit flödes åtkomst-token. Standardvärdet är false. Den här flaggan används för webbläsarbaserade appar, t. ex. java script-appar med en sida. Mer information `OAuth 2.0 implicit grant flow` finns i innehålls förteckningen och i avsnitten om implicit flöde. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Boolesk | Anger om den här webbappen kan begära OAuth 2.0-token för implicit flödes-ID. Standardvärdet är false. Den här flaggan används för webbläsarbaserade appar, t. ex. java script-appar med en sida. | `false` |
| `oauth2Permissions` | Collection | Anger samlingen OAuth 2,0-behörighets omfattningar som webb-API: n (resurs) visar för klient program. Dessa behörighets omfattningar kan beviljas till klient program under medgivande. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Boolesk | Anger om, som en del av OAuth 2,0-Tokenbegäran, tillåter Azure AD att begär Anden skickas, i stället för att hämta förfrågningar. Standardvärdet är false, vilket anger att endast GET-begäranden ska tillåtas. | `false` |
| `parentalControlSettings` | Sträng | `countriesBlockedForMinors`anger de länder där appen blockeras för minderåriga.<br>`legalAgeGroupRule`anger den juridiska ålders grupps regel som gäller för användare av appen. Kan ställas in `Allow`på `RequireConsentForPrivacyServices` `RequireConsentForMinors`, `RequireConsentForKids`,, eller `BlockMinors`.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Collection | Se beskrivningen av `keyCredentials` egenskapen. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Collection | Visar en lista över program och begärda behörigheter för implicit medgivande. Kräver att en administratör har gett sitt medgivande till programmet. preAuthorizedApplications kräver inte att användaren godkänner de begärda behörigheterna. Behörigheter som anges i preAuthorizedApplications kräver inte användar medgivande. Alla ytterligare begärda behörigheter som inte listas i preAuthorizedApplications kräver dock användar medgivande. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Boolesk | Anger om det här programmet är en offentlig klient (till exempel ett installerat program som körs på en mobil enhet). <br><br> _Obs! Detta är endast tillgängligt i Appregistreringar (bakåtkompatibelt). Ersätts `allowPublicClient` av i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen._ | |
| `publisherDomain` | Sträng | Den verifierade Publisher-domänen för programmet. Skrivskyddad. | https://www.contoso.com |
| `replyUrls` | Sträng mat ris | Den här egenskapen för flera värden innehåller listan över registrerade redirect_uri-värden som Azure AD accepterar som mål när token returneras. <br><br> _Obs! Detta är endast tillgängligt i Appregistreringar (bakåtkompatibelt). Ersätts `replyUrlsWithType` av i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen._ | |
| `replyUrlsWithType` | Collection | Den här egenskapen för flera värden innehåller listan över registrerade redirect_uri-värden som Azure AD accepterar som mål när token returneras. Varje URI-värde ska innehålla ett värde för en associerad app-typ. Typ värden som stöds är `Web`: `InstalledClient`,. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Collection | Med dynamiskt medgivande `requiredResourceAccess` bevarar administratörs upplevelsen och användar medgivande upplevelsen för användare som använder det statiska godkännandet. Detta innebär dock inte att användar medgivande uppstår i det allmänna fallet.<br>`resourceAppId`är den unika identifieraren för den resurs som appen kräver åtkomst till. Värdet ska vara lika med det appId som deklarerats för mål resurs appen.<br>`resourceAccess`är en matris som visar behörighets omfattningarna för OAuth 2.0 och de app-roller som appen kräver från den angivna resursen. Innehåller värdena `type` och för de angivna resurserna. `id` | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | Sträng | URL: en till SAML metadata för appen. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | Sträng | Anger webb adressen till appens start sida. | `https://MyRegisteredApp` |
| `signInAudience` | Sträng | Anger vilka Microsoft-konton som stöds för det aktuella programmet. Värden som stöds är:<ul><li>**AzureADMyOrg** – användare med ett arbets-eller skol konto för Microsoft i min organisations Azure AD-klient (dvs. en enda klient)</li><li>**AzureADMultipleOrgs** – användare med ett arbets-eller skol konto för Microsoft i en organisations Azure AD-klient (dvs. flera innehavare)</li> <li>**AzureADandPersonalMicrosoftAccount** – användare med en personlig Microsoft-konto eller ett arbets-eller skol konto i en organisations Azure AD-klient</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | Anpassade strängar som kan användas för att kategorisera och identifiera programmet. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Vanliga problem

### <a name="manifest-limits"></a>Manifest gränser

Ett program manifest har flera attribut som kallas samlingar. till exempel approles, inloggnings uppgifter, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess och oauth2Permissions. I det fullständiga applikations manifestet för ett program, har det totala antalet poster i alla samlingar i kombination varit begränsade till 1200. Om du redan har 100 omdirigerings-URI: er som anges i applikations manifestet är du bara kvar med 1100 återstående poster som ska användas i alla andra samlingar tillsammans som utgör manifestet.

> [!NOTE]
> Om du försöker lägga till fler än 1200 poster i applikations manifestet kan du se ett fel **"Det gick inte att uppdatera programmet xxxxxx. Felinformation: Manifestets storlek har överskridit gränsen. Minska antalet värden och gör om begäran. "**

### <a name="unsupported-attributes"></a>Attribut som inte stöds

Applikations manifestet representerar schemat för den underliggande program modellen i Azure AD. När det underliggande schemat utvecklas kommer manifest redigeraren att uppdateras för att avspegla det nya schemat. Därför kan det hända att du upptäcker nya attribut som visas i applikations manifestet. I sällsynta fall kan du märka en syntaktisk eller semantisk ändring i befintliga attribut, eller så kan du hitta ett attribut som tidigare inte stöds längre. Till exempel visas nya attribut i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) som är kända med ett annat namn i Appregistreringar (äldre).


| Appregistreringar (bakåtkompatibelt)| Appregistreringar           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Beskrivningar för de här attributen finns i [referens](#manifest-reference) avsnittet för manifestet.

När du försöker överföra ett tidigare nedladdat manifest kan du se något av följande fel. Detta beror förmodligen på att manifest redigeraren nu stöder en nyare version av schemat, som inte överensstämmer med den som du försöker ladda upp.

- "**Det gick inte att uppdatera xxxxxx-programmet. Fel information: Ogiltig objekt identifierare ' odefinierad '. [].** "
- "**Det gick inte att uppdatera xxxxxx-programmet. Fel information: Ett eller flera angivna egenskaps värden är ogiltiga. [].** "
- "**Det gick inte att uppdatera xxxxxx-programmet. Fel information: Det är inte tillåtet att ange availableToOtherTenants i den här API-versionen för uppdatering. [].** "
- "**Det gick inte att uppdatera xxxxxx-programmet. Fel information: Det går inte att uppdatera replyUrls-egenskapen för det här programmet. Använd egenskapen ' replyUrlsWithType ' i stället. [].** "
- "**Det gick inte att uppdatera xxxxxx-programmet. Fel information: Ett värde utan ett typ namn hittades och ingen förväntad typ är tillgänglig. När modellen anges måste varje värde i nytto lasten ha en typ som antingen kan anges i nytto lasten, uttryckligen av anroparen eller implicit härledd från det överordnade värdet. []** "

När du ser något av dessa fel rekommenderar vi följande:

1. Redigera attributen individuellt i manifest redigeraren i stället för att ladda upp ett tidigare nedladdat manifest. Använd [referens](#manifest-reference) tabellen i manifestet för att förstå syntaxen och semantiken för gamla och nya attribut så att du kan redigera de attribut som du är intresse rad av. 
1. Om arbets flödet kräver att du sparar manifesten på käll lagrings platsen för att använda senare, rekommenderar vi att du bygger in de sparade manifesten i din lagrings plats med den som visas i Appregistreringars upplevelsen.

## <a name="next-steps"></a>Nästa steg

* Mer information om relationen mellan appens program och tjänst huvud objekt finns i [program-och tjänst huvud objekt i Azure AD](app-objects-and-service-principals.md).
* Se [ord listan för Microsoft Identity Platform Developer](developer-glossary.md) för definitioner av några av de grundläggande koncepten för utvecklare av Microsoft Identity Platform.

Använd följande kommentarer-avsnitt för att ge feedback som hjälper dig att förfina och forma vårt innehåll.

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
