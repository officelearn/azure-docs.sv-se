---
title: Förstå Azure Active Directory-appens manifest | Microsoft Docs
description: Detaljerad täckning av Azure Active Directory app-manifestet som representerar ett programs identitets konfiguration i en Azure AD-klient och används för att under lätta OAuth-auktorisering, medgivande upplevelse och mycket annat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: a12715ba9aac77461d4968bd9b8f3de30af243c4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262755"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory app-manifest

Applikations manifestet innehåller en definition av alla attribut för ett program objekt i Microsoft Identity Platform. Den fungerar också som en mekanism för att uppdatera programobjektet. Mer information om programentiteten och dess schema finns i dokumentationen för [Graph API application Entity](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Du kan konfigurera en Apps attribut via Azure Portal eller program mässigt med hjälp av [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) eller [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Det finns dock vissa scenarier där du måste redigera appens manifest för att konfigurera appens attribut. Några vanliga scenarier:

* Om du har registrerat appen som Azure AD-konto för flera klienter och personliga Microsoft-konton kan du inte ändra de Microsoft-konton som stöds i användar gränssnittet. I stället måste du använda program manifest redigeraren för att ändra den konto typ som stöds.
* Om du behöver definiera behörigheter och roller som appen stöder måste du ändra applikations manifestet.

## <a name="configure-the-app-manifest"></a>Konfigurera app-manifestet

Så här konfigurerar du applikations manifestet:

1. Gå till [Azure-portalen](https://portal.azure.com). Sök efter och välj tjänsten **Azure Active Directory** .
1. Välj **Appregistreringar**.
1. Välj den app som du vill konfigurera.
1. På appens **översiktssida** väljer du avsnittet **Manifest**. En webbaserad manifest redigerare öppnas, så att du kan redigera manifestet i portalen. Alternativt kan du välja **Ladda ned** för att redigera manifestet lokalt och sedan använda **upload** för att tillämpa det igen på ditt program.

## <a name="manifest-reference"></a>Manifest referens


### <a name="key-value-type-accesstokenacceptedversion-nullable-int32"></a>Nyckel, värdetyp: `accessTokenAcceptedVersion`, Nullable Int32 
Anger den åtkomsttoken som förväntas av resursen. Den här parametern ändrar versionen och formatet på det JWT-tillverkade oberoende av slut punkten eller klienten som används för att begära åtkomsttoken.

Slut punkten som används, v 1.0 eller v 2.0 väljs av klienten och påverkar endast versionen av id_tokens. Resurser måste konfigurera `accesstokenAcceptedVersion` uttryckligen för att ange det åtkomsttoken som stöds.

Möjliga värden för `accesstokenAcceptedVersion` är 1, 2 eller null. Om värdet är null är parametern standardvärdet 1, som motsvarar v 1.0-slutpunkten. 

Om `signInAudience` är `AzureADandPersonalMicrosoftAccount`måste värdet vara `2`  

Exempelvärde: `2` 

### <a name="key-value-type-addins-collection"></a>Nyckel, värdetyp: `addIns`, samling 
Definierar ett anpassat beteende som en förbruknings tjänst kan använda för att anropa en app i vissa sammanhang. Till exempel kan program som kan rendera fil strömmar ange egenskapen addins för "FileHandler"-funktionen. Med den här parametern kan tjänster som Office 365 anropa programmet i kontexten för ett dokument som användaren arbetar med. 

Exempel värde: 
<code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>

### <a name="key-value-type-allowpublicclient-boolean"></a>Nyckel, värdetyp: `allowPublicClient`, Boolean 
Anger typ av återställnings program. Azure AD härleder program typen från replyUrlsWithType som standard. Det finns vissa scenarier där Azure AD inte kan fastställa klientens app-typ. Ett sådant scenario är till exempel det [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) -flöde där http-begäran sker utan URL-omdirigering). I dessa fall tolkar Azure AD program typen baserat på värdet för den här egenskapen. Om det här värdet är inställt på Sant anges typ av återställnings program som offentlig klient, till exempel en installerad app som körs på en mobil enhet. Standardvärdet är false, vilket innebär att återställnings program typen är konfidentiell klient, till exempel Web App. 

Exempelvärde: `false` 

### <a name="key-value-type-availabletoothertenants-boolean"></a>Nyckel, värdetyp: `availableToOtherTenants`, Boolean 
sant om programmet delas med andra klienter. annars FALSE. <br><br> Obs! det här är endast tillgängligt i den **Appregistreringar (äldre)** . Ersätts av `signInAudience` i [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelsen. 

### <a name="key-value-type-appid-string"></a>Nyckel, värdetyp: `appId`, sträng 
Anger den unika identifieraren för den app som är tilldelad en app av Azure AD. 

Exempelvärde: `"601790de-b632-4f57-9523-ee7cb6ceba95"` 

### <a name="key-value-type-approles-collection"></a>Nyckel, värdetyp: `appRoles`, samling 
Anger den samling roller som en app kan deklarera. Dessa roller kan tilldelas till användare, grupper eller tjänstens huvud namn. Fler exempel och information finns i [lägga till app-roller i ditt program och ta emot dem i token](howto-add-app-roles-in-azure-ad-apps.md) 

Exempel värde: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  

### <a name="key-value-type-displayname-string"></a>Nyckel, värdetyp: `displayName`, sträng 
Appens visnings namn. <br><br> Obs! det här är endast tillgängligt i den **Appregistreringar (äldre)** . Ersätts av `name` i [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelsen. 

Exempelvärde: `"MyRegisteredApp"` 

### <a name="key-value-type-errorurl-string"></a>Nyckel, värdetyp: `errorUrl`, sträng 
Stöds inte. 

### <a name="key-value-type-groupmembershipclaims-string"></a>Nyckel, värdetyp: `groupMembershipClaims`, sträng 
Konfigurerar `groups`-anspråk som utfärdats i en användare eller OAuth 2,0-åtkomsttoken som appen förväntar sig. Ange det här attributet genom att använda något av följande giltiga sträng värden:
- `"None"`
- `"SecurityGroup"` (för säkerhets grupper och Azure AD-roller)
- `"All"` (detta kommer att hämta alla säkerhets grupper, distributions grupper och Azure AD-katalog roller som den inloggade användaren är medlem i. 

Exempelvärde: `"SecurityGroup"` 

### <a name="key-value-type-homepage-string"></a>Nyckel, värdetyp: `homepage`, sträng 
URL: en till programmets start sida. <br><br> Obs! det här är endast tillgängligt i den **Appregistreringar (äldre)** . Ersätts av `signInUrl` i [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelsen. 

Exempelvärde: `"https://MyRegisteredApp"` 

### <a name="key-value-type-objectid-string"></a>Nyckel, värdetyp: `objectId`, sträng 
Den unika identifieraren för appen i katalogen. <br><br> Detta är endast tillgängligt i den **Appregistreringar (äldre)** . Ersätts av `id` i [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelsen. 

Exempelvärde: `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` 

### <a name="key-value-type-optionalclaims-string"></a>Nyckel, värdetyp: `optionalClaims`, sträng 
De valfria anspråk som returneras i token av säkerhetstokentjänsten för den här speciella appen.<br>För närvarande kan appar som stöder både personliga konton och Azure AD (registrerade via appens registrerings Portal) inte använda valfria anspråk. Appar som registrerats för bara Azure AD med hjälp av v 2.0-slutpunkten kan dock hämta de valfria anspråk som de begär i manifestet. Mer information finns i [valfria anspråk](active-directory-optional-claims.md). 

Exempel värde:  
`null` 

### <a name="key-value-type-id-string"></a>Nyckel, värdetyp: `id`, sträng 
Den unika identifieraren för appen i katalogen. Detta ID är inte den identifierare som används för att identifiera appen i någon protokoll transaktion. Den används för att referera till objektet i katalog frågor. 

Exempelvärde: `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` 

### <a name="key-value-type-identifieruris-string-array"></a>Nyckel, värdetyp: `identifierUris`, sträng mat ris 
Användardefinierade URI: er som unikt identifierar en webbapp i sin Azure AD-klient eller inom en verifierad anpassad domän om appen är flera innehavare. 

Exempel värde: 
<code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> 

### <a name="key-value-type-informationalurls-string"></a>Nyckel, värdetyp: `informationalUrls`, sträng 
Anger länkar till appens användnings villkor och sekretess policy. Användnings villkoren och sekretess policyn är tilldelad till användarna genom användar medgivande upplevelsen. Mer information finns i [så här gör du: Lägg till användnings villkor och sekretess policy för registrerade Azure AD-appar](howto-add-terms-of-service-privacy-statement.md). 

Exempel värde: 
<code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> 

### <a name="key-value-type-keycredentials-collection"></a>Nyckel, värdetyp: `keyCredentials`, samling 
Innehåller referenser till app-tilldelade autentiseringsuppgifter, strängbaserade delade hemligheter och X. 509-certifikat. ). 

Exempel värde: 
<code>[<br>&nbsp;{<br>&nbsp;&These credentials are used when requesting access tokens (when the app is acting as a client rather that as resourcenbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-knownclientapplications-string-array"></a>Nyckel, värdetyp: `knownClientApplications`, sträng mat ris 
Används för att ge bunt om du har en lösning som innehåller två delar: en klient app och en anpassad webb-API-app. Om du anger appID för-klient programmet i det här värdet behöver användaren bara godkänna en gång till klient programmet. Azure AD är medveten om att samtycka till klienten innebär att implicit samtycka till webb-API: et. Tjänsten etablerar automatiskt tjänstens huvud namn för både klient-och webb-API: n på samma tidpunkt. Både klienten och webb-API-appen måste vara registrerade i samma klient organisation. 

Exempelvärde: `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` 

### <a name="key-value-type-logourl-string"></a>Nyckel, värdetyp: `logoUrl`, sträng 
Skrivskyddat värde som pekar på CDN-URL: en till en logo typ som laddades upp i portalen. 

Exempelvärde: `"https://MyRegisteredAppLogo"` 

### <a name="key-value-type-logouturl-string"></a>Nyckel, värdetyp: `logoutUrl`, sträng 
URL: en för att logga ut från appen. 

Exempel värde:  
`"https://MyRegisteredAppLogout"` 

### <a name="key-value-type-name-string"></a>Nyckel, värdetyp: `name`, sträng 
Appens visnings namn. 

Exempelvärde: `"MyRegisteredApp"` 

### <a name="key-value-type-oauth2allowimplicitflow-boolean"></a>Nyckel, värdetyp: `oauth2AllowImplicitFlow`, Boolean 
Anger om den här webbappen kan begära OAuth 2.0 implicit flödes åtkomst-token. Standardvärdet är false. Den här flaggan används för webbläsarbaserade appar, t. ex. java script-appar med en sida. Om du vill veta mer kan du ange `OAuth 2.0 implicit grant flow` i innehålls förteckningen och se avsnitten om implicit flöde. 

Exempelvärde: `false` 

### <a name="key-value-type-oauth2allowidtokenimplicitflow-boolean"></a>Nyckel, värdetyp: `oauth2AllowIdTokenImplicitFlow`, Boolean 
Anger om den här webbappen kan begära OAuth 2.0-token för implicit flödes-ID. Standardvärdet är false. Den här flaggan används för webbläsarbaserade appar, t. ex. java script-appar med en sida. 

Exempelvärde: `false` 

### <a name="key-value-type-oauth2permissions-collection"></a>Nyckel, värdetyp: `oauth2Permissions`, samling 
Anger samlingen OAuth 2,0-behörighets omfattningar som webb-API: n (resurs) visar för klient program. Dessa behörighets omfattningar kan beviljas till klient program under medgivande. 

Exempel värde: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>

### <a name="key-value-type-oauth2requiredpostresponse-boolean"></a>Nyckel, värdetyp: `oauth2RequiredPostResponse`, Boolean 
Anger om, som en del av OAuth 2,0-Tokenbegäran, tillåter Azure AD att begär Anden skickas, i stället för att hämta förfrågningar. Standardvärdet är false, vilket anger att endast GET-begäranden ska tillåtas. 

Exempelvärde: `false` 

### <a name="key-value-type-parentalcontrolsettings-string"></a>Nyckel, värdetyp: `parentalControlSettings`, sträng 

`countriesBlockedForMinors` anger de länder där appen blockeras för minderåriga.<br>`legalAgeGroupRule` anger den juridiska ålders grupps regel som gäller för användare av appen. Kan anges till `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`eller `BlockMinors`.  

Exempel värde: 
<code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> 

### <a name="key-value-type-passwordcredentials-collection"></a>Nyckel, värdetyp: `passwordCredentials`, samling 
Se beskrivningen av egenskapen `keyCredentials`. 

Exempel värde: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> 

### <a name="key-value-type-preauthorizedapplications-collection"></a>Nyckel, värdetyp: `preAuthorizedApplications`, samling 
Visar en lista över program och begärda behörigheter för implicit medgivande. Kräver att en administratör har gett sitt medgivande till programmet. preAuthorizedApplications kräver inte att användaren godkänner de begärda behörigheterna. Behörigheter som anges i preAuthorizedApplications kräver inte användar medgivande. Alla ytterligare begärda behörigheter som inte listas i preAuthorizedApplications kräver dock användar medgivande. 

Exempel värde: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-publicclient-boolean"></a>Nyckel, värdetyp: `publicClient`, Boolean 
Anger om det här programmet är en offentlig klient (till exempel ett installerat program som körs på en mobil enhet). 

Den här egenskapen är endast tillgänglig i den **Appregistreringar (äldre)** . Ersätts av `allowPublicClient` i [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelsen. 

### <a name="key-value-type-publisherdomain-string"></a>Nyckel, värdetyp: `publisherDomain`, sträng 
Den verifierade Publisher-domänen för programmet. Skrivskyddad. 

Exempelvärde: `https://www.contoso.com`

### <a name="key-value-type-replyurls-string-array"></a>Nyckel, värdetyp: `replyUrls`, sträng mat ris 
Den här egenskapen för flera värden innehåller en lista över registrerade redirect_uri värden som Azure AD accepterar som mål när token returneras. <br><br> Den här egenskapen är endast tillgänglig i den **Appregistreringar (äldre)** . Ersätts av `replyUrlsWithType` i [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelsen. 

### <a name="key-value-type-replyurlswithtype-collection"></a>Nyckel, värdetyp: `replyUrlsWithType`, samling 
Den här egenskapen för flera värden innehåller en lista över registrerade redirect_uri värden som Azure AD accepterar som mål när token returneras. Varje URI-värde ska innehålla ett värde för en associerad app-typ. Typ värden som stöds är: <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> Läs mer om [begränsningar och begränsningar för replyUrl](https://docs.microsoft.com/azure/active-directory/develop/reply-url). 

Exempel värde: 
<code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> 

### <a name="key-value-type-requiredresourceaccess-collection"></a>Nyckel, värdetyp: `requiredResourceAccess`, samling 
Med det dynamiska godkännandet får `requiredResourceAccess` enheternas godkännande upplevelse och användar medgivande upplevelsen för användare som använder sig av statiskt medgivande. Den här parametern styr dock inte användar medgivande upplevelsen för det allmänna fallet.<br>`resourceAppId` är den unika identifieraren för den resurs som appen kräver åtkomst till. Värdet ska vara lika med det appId som deklarerats för mål resurs appen.<br>`resourceAccess` är en matris som visar behörighets omfattningarna för OAuth 2.0 och de app-roller som appen kräver från den angivna resursen. Innehåller `id` och `type` värden för de angivna resurserna. 

Exempel värde: 
<code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> 

### <a name="key-value-type-samlmetadataurl-string"></a>Nyckel, värdetyp: `samlMetadataUrl`, sträng 
URL: en till SAML metadata för appen. 

Exempelvärde: `https://MyRegisteredAppSAMLMetadata` 

### <a name="key-value-type-signinurl-string"></a>Nyckel, värdetyp: `signInUrl`, sträng 

Anger webb adressen till appens start sida. 

Exempelvärde: `https://MyRegisteredApp` 

### <a name="key-value-type-signinaudience-string"></a>Nyckel, värdetyp: `signInAudience`, sträng 
Anger vilka Microsoft-konton som stöds för det aktuella programmet. Värden som stöds är:
- **AzureADMyOrg** – användare med ett arbets-eller skol konto för Microsoft i min organisations Azure AD-klient (till exempel en enskild klient)
- **AzureADMultipleOrgs** – användare med ett arbets-eller skol konto för Microsoft i en organisations Azure AD-klient (till exempel flera innehavare)
- **AzureADandPersonalMicrosoftAccount** – användare med en personlig Microsoft-konto eller ett arbets-eller skol konto i en organisations Azure AD-klient 

Exempel värde:  
`AzureADandPersonalMicrosoftAccount` 

### <a name="key-value-type-tags-string-array"></a>Nyckel, värdetyp: `tags`, sträng mat ris 
Anpassade strängar som kan användas för att kategorisera och identifiera programmet. 

Exempel värde: 
<code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code>

## <a name="common-issues"></a>Vanliga problem

### <a name="manifest-limits"></a>Manifest gränser

Ett program manifest har flera attribut som kallas samlingar. till exempel approles, inloggnings uppgifter, knownClientApplications, identifierUris, rediretUris, requiredResourceAccess och oauth2Permissions. I det fullständiga applikations manifestet för ett program, har det totala antalet poster i alla samlingar i kombination varit begränsade till 1200. Om du tidigare har angett 100 omdirigerings-URI: er i applikations manifestet är du bara kvar med 1100 återstående poster som ska användas i alla andra samlingar som utgör manifestet.

> [!NOTE]
> Om du försöker lägga till fler än 1200 poster i applikations manifestet kan du se ett fel **"Det gick inte att uppdatera programmet xxxxxx. Fel information: Manifestets storlek har överskridit gränsen. Minska antalet värden och gör om begäran. "**

### <a name="unsupported-attributes"></a>Attribut som inte stöds

Applikations manifestet representerar schemat för den underliggande program modellen i Azure AD. När det underliggande schemat utvecklas kommer manifest redigeraren att uppdateras för att avspegla det nya schemat. Därför kan det hända att du upptäcker nya attribut som visas i applikations manifestet. I sällsynta fall kan du märka en syntaktisk eller semantisk ändring i befintliga attribut, eller så kan du hitta ett attribut som tidigare inte stöds längre. Till exempel visas nya attribut i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908), som är kända med ett annat namn i Appregistreringar (äldre).

| Appregistreringar (bakåtkompatibelt)| Appregistreringar           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Beskrivningar för de här attributen finns i [referens avsnittet för manifestet](#manifest-reference) .

När du försöker överföra ett tidigare nedladdat manifest kan du se något av följande fel. Det här felet beror förmodligen på att manifest redigeraren nu stöder en nyare version av schemat, som inte överensstämmer med den som du försöker ladda upp.

* "Det gick inte att uppdatera xxxxxx-programmet. Fel information: ogiltig objekt identifierare ' undefined '. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Fel information: ett eller flera angivna egenskaps värden är ogiltiga. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Fel information: det går inte att ange availableToOtherTenants i den här API-versionen för uppdatering. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Fel information: uppdateringar av egenskapen ' replyUrls ' tillåts inte för det här programmet. Använd egenskapen ' replyUrlsWithType ' i stället. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Fel information: ett värde utan ett typ namn hittades och ingen förväntad typ är tillgänglig. När modellen anges måste varje värde i nytto lasten ha en typ som antingen kan anges i nytto lasten, uttryckligen av anroparen eller implicit härledd från det överordnade värdet. []"

När du ser något av dessa fel rekommenderar vi följande åtgärder:

1. Redigera attributen individuellt i manifest redigeraren i stället för att ladda upp ett tidigare nedladdat manifest. Använd referens tabellen i [manifestet](#manifest-reference) för att förstå syntaxen och semantiken för gamla och nya attribut så att du kan redigera de attribut som du är intresse rad av. 
1. Om arbets flödet kräver att du sparar manifesten på käll lagrings platsen för att använda senare, rekommenderar vi att du bygger in de sparade manifesten i din lagrings plats med den som visas i **Appregistreringars** upplevelsen.

## <a name="next-steps"></a>Nästa steg

* Mer information om relationen mellan appens program och tjänst huvud objekt finns i [program-och tjänst huvud objekt i Azure AD](app-objects-and-service-principals.md).
* Se [ord listan för Microsoft Identity Platform Developer](developer-glossary.md) för definitioner av vissa kärn utvecklings koncept för Microsoft Identity Platform.

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
