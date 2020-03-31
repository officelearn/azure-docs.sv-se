---
title: Mallresurser för Azure API Management | Microsoft-dokument
description: Lär dig mer om vilka typer av resurser som är tillgängliga för användning i utvecklarportalmallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249508"
---
# <a name="azure-api-management-template-resources"></a>Mallresurser för Azure API Management
Azure API Management innehåller följande typer av resurser som kan användas i utvecklarportalmallarna.  
  
-   [Strängresurser](#strings)  
  
-   [Glyfer-resurser](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a>Strängresurser  
 API Management innehåller en omfattande uppsättning strängresurser som kan användas i utvecklarportalen. Dessa resurser är lokaliserade till alla språk som stöds av API Management. Standarduppsättningen med mallar använder dessa resurser för sidhuvuden, etiketter och alla konstanta strängar som visas i utvecklarportalen. Om du vill använda en strängresurs i mallarna anger du resurssträngprefixet följt av strängnamnet, som visas i följande exempel.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Följande exempel kommer från mallen Produktlista och visar **Produkter** högst upp på sidan.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Följande lokaliseringsalternativ stöds:

| Nationell inställning    | Språk               |
|-----------|------------------------|
| ”en”      | "Engelska"              |
| "Cs"      | "Čeština"              |
| "De"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "Hu"      | "Magyar"               |
| "Det"      | "Italiano"             |
| "Ja-JP"   | "中ンン"                |
| "ko"      | "" ""                |
| "Det är inte så här"      | "Nederlands"           |
| "Mycket bra"      | "Polski"               |
| "pt-br"   | "Português (Brasil)"   |
| "pt-pt"   | "Português (Portugal)" |
| "Ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "中ンンン)"           |
| "zh-hant" | "中ンン)"           |

 Se följande tabeller för de strängresurser som är tillgängliga för användning i utvecklarportalmallarna. Använd tabellnamnet som prefix för strängresurserna i tabellen.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings (800)](#AppStrings)  
  
-   [CommonResources (Gemensamma Resurser)](#CommonResources)  
  
-   [CommonStrings (vanliga egenskaper)](#CommonStrings)  
  
-   [Dokumentation](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProdukterSträngar](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [Logga inResurser](#SigninResources)  
  
-   [TeckeninStrings](#SigninStrings)  
  
-   [Registreringsstrubbningar](#SignupStrings)  
  
-   [PrenumerationslistaSträngar](#SubscriptionListStrings)  
  
-   [PrenumerationStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a>ApisStrings  
  
|Namn|Text|  
|----------|----------|  
|PageTitleApis|API:er|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Namn|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Förhandsgranskning av program|  
|WebApplicationsRequirementsHeader|Krav|  
|WebApplicationsScreenshotAlt|Skärmbild|  
|WebApplicationsScreenshotsHeader|Skärmbilder|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Namn|Text|  
|----------|----------|  
|WebDevelopersAppDeleteKonfirmation|Vill du ta bort programmet?|  
|WebDevelopersAppNotPublished|Ej publicerad|  
|WebDevelopersAppNotSubmitted|Inte skickat|  
|WebDevelopersAppTableCategoryHeader|Kategori|  
|WebDevelopersAppTableNameHeader|Namn|  
|WebDevelopersAppTableStateHeader|Status|  
|WebDevelopersEditLink|Redigera|  
|WebDevelopersRegisterAppLink|Registrera program|  
|WebDevelopersTa bortLänk|Ta bort|  
|WebDevelopersSubmitLink|Skicka|  
|WebDevelopersYourApplicationsHeader|Dina program|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a>AppStrings (800)  
  
|Namn|Text|  
|----------|----------|  
|WebApplicationsHeader|Program|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a>CommonResources (Gemensamma Resurser)  
  
|Namn|Text|  
|----------|----------|  
|NoItemsToDisplay|Inga resultat hittades.|  
|AllmäntUtsespråk|Något är inte rätt. Det kan vara ett tillfälligt tekniskt fel eller en bugg. Snälla, försök igen.|  
|AllmäntJsonExceptionMessage|Något är inte rätt. Det kan vara ett tillfälligt tekniskt fel eller en bugg. Ladda om sidan och försök igen.|  
|BekräftelseMessageUnsavedChanges|Det finns några ändringar som inte sparats. Vill du avbryta och ignorera ändringarna?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http Begär kroppen för stor.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a>CommonStrings (vanliga egenskaper)  
  
|Namn|Text|  
|----------|----------|  
|KnappLabelCancel|Avbryt|  
|KnapplabelSave|Spara|  
|AllmäntUtsespråk|Något är inte rätt. Det kan vara ett tillfälligt tekniskt fel eller en bugg. Snälla, försök igen.|  
|NoItemsToDisplay|Det finns inga objekt att visa.|  
|PersonsökareButtonLabelFirst|Första|  
|PersonsökareButtonLabelLast|Sista|  
|PersonsökareButtonLabelNext|Nästa|  
|PersonsökareButtonLabelFörbäd|Föregående|  
|PagerLabelPageNOfM|Sida {0} av {1}|  
|PasswordTooKort|Lösenordet är för kort|  
|E-postAsPassword|Använd inte din e-post som lösenord|  
|PasswordSameAsUserName|Ditt lösenord får inte innehålla ditt användarnamn|  
|PasswordTwoCharacterClasses|Använda olika teckenklasser|  
|PasswordTooManyRepetitions|För många repetitioner|  
|PasswordSequenceFound (LösenordSequenceFound)|Ditt lösenord innehåller sekvenser|  
|PagerLabelPageSize|Sidstorlek|  
|CurtainLabelLastning|Läser in ...|  
|TabellplatshållareIng inte för attdisplay|Det finns inga data för den valda perioden och|  
|KnappLabelClose|Stäng|  
  
###  <a name="documentation"></a><a name="Documentation"></a>Dokumentation  
  
|Namn|Text|  
|----------|----------|  
|WebDokumentationInvalidHeaderErrorMessage|Ogiltigt{0}huvud ' '|  
|WebDokumentationInvalidRequestErrorMessage|Url för ogiltig begäran|  
|TextboxLabelAccessToken|Åtkomsttoken *|  
|Rullgardinsmenyn|Primär-{0}|  
|RullgardinsmenynOptionSecondaryKeyFormat|Sekundär-{0}|  
|WebDokumentationAbonnemangKeyText|Din prenumerationsnyckel|  
|WebDocumentationTemplatesLägga tilldheaders|Lägga till nödvändiga HTTP-huvuden|  
|WebDokumentationTemplatesBasicAuthSample|Exempel på grundläggande auktorisering|  
|WebDokumentationTemplatesCurlForBasicAuth|För grundläggande auktorisering: --user {username}:{password}|  
|WebDocumentationTemplatesCurlValuesForPath|Ange värden för sökvägsparametrar (visas som {...}), din prenumerationsnyckel och dina värden för frågeparametrar|  
|WebDocumentationTemplatesDeveloperKey|Ange din prenumerationsnyckel|  
|WebDokumentationTemplatesJavaApache|I det här exemplet används Apache HTTP-klienten från HTTP-komponenter (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDokumentationTemplatesOptionalParams|Ange värden för valfria parametrar efter behov|  
|WebDokumentationTemplatesPhpPackage|I det här exemplet används HTTP_Request2-paketet. (För mer information:https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Ange värden för sökvägsparametrar (visas som {...}) och begärandetext om det behövs|  
|WebDokumentationTemplatesRequestBody|Ange förfrå underställe|  
|WebDocumentationTemplatesRequiredParams|Ange värden för följande obligatoriska parametrar|  
|WebDocumentationTemplatesVärdeesForPath|Ange värden för sökvägsparametrar (visas som {...})|  
|OAuth2AuthorizationEndpointDescription|Auktoriseringsslutpunkten används för att interagera med resursägaren och erhålla ett auktoriseringsbidrag.|  
|OAuth2AuthorizationEndpointName|Slutpunkt för auktorisering|  
|OAuth2TokenEndpointDescription|Tokenslutpunkten används av klienten för att hämta en åtkomsttoken genom att presentera dess auktoriseringsbidrag eller uppdateringstoken.|  
|OAuth2TokenEndpointName|Tokenslutpunkt|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p\> Klienten initierar flödet genom att dirigera resursägarens användaragent till auktoriseringsslutpunkten.  Klienten innehåller klientidentifieraren, det begärda scopet, det lokala tillståndet och en uri för omdirigering som auktoriseringsservern skickar tillbaka användaragenten när åtkomsten har beviljats (eller nekats).     </p\> <p\> Auktoriseringsservern autentiserar resursägaren (via användaragenten) och fastställer om resursägaren beviljar eller nekar klientens åtkomstbegäran.     </p\> <p\> Förutsatt att resursägaren beviljar åtkomst omdirigerar auktoriseringsservern tillbaka användaren och agenten till klienten med hjälp av den omdirigerings-URI som angavs tidigare (i begäran eller under klientregistrering).  Omdirigerings-URI innehåller en auktoriseringskod och alla lokala tillstånd som tillhandahållits av klienten tidigare.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p\> Om användaren nekar åtkomstbegäran om begäran är ogiltig, kommer klienten att informeras med hjälp av följande parametrar som lagts till i omdirigeringen: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Begäran om auktorisering|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p\> Klientappen måste skicka användaren till auktoriseringsslutpunkten för att kunna initiera OAuth-processen.          Vid auktoriseringsslutpunkten autentiserar användaren och beviljar eller nekar sedan åtkomst till appen.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p\> Förutsatt att resursägaren beviljar åtkomst omdirigerar auktoriseringsservern tillbaka användaren och agenten till klienten med hjälp av den omdirigerings-URI som angavs tidigare (i begäran eller under klientregistrering).  Omdirigerings-URI innehåller en auktoriseringskod och alla lokala tillstånd som tillhandahållits av klienten tidigare. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p\> Klienten begär en åtkomsttoken från auktoriseringsserverns tokenslutpunkt genom att inkludera auktoriseringskoden som togs emot i föregående steg.  När begäran görs autentiserar klienten med auktoriseringsservern.  Klienten innehåller den omdirigerings-URI som används för att hämta auktoriseringskoden för verifiering. </p\> <p\> Auktoriseringsservern autentiserar klienten, validerar auktoriseringskoden och ser till att omdirigerings-URI:n har fått matchar den URI som används för att omdirigera klienten i steg (C).  Om det är giltigt svarar auktoriseringsservern tillbaka med en åtkomsttoken och eventuellt en uppdateringstoken. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p\> Om klientautentiseringen för begäran misslyckades eller är ogiltig svarar auktoriseringsservern med statuskoden HTTP 400 (Bad Request) (om inget annat anges) och innehåller följande parametrar med svaret. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p\> Klienten gör en begäran till tokenslutpunkten genom att skicka följande parametrar med formatet "application/x-www-form-urlencoded" med en teckenkodning av UTF-8 i http-begäranhetstexten. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p\> Auktoriseringsservern utfärdar en åtkomsttoken och valfri uppdateringstoken och konstruerar svaret genom att lägga till följande parametrar i entiteten för HTTP-svaret med statuskoden 200 (OK). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p\> Klienten autentiserar med auktoriseringsservern och begär en åtkomsttoken från tokenslutpunkten. </p\> <p\> Auktoriseringsservern autentiserar klienten och utfärdar, om den är giltig, en åtkomsttoken. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\> Om begäran misslyckades klientautentisering eller är ogiltig auktoriseringsservern svarar med en HTTP 400 (Bad Request) statuskod (om inte annat anges) och innehåller följande parametrar med svaret. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p\> Klienten gör en begäran till tokenslutpunkten genom att lägga till följande parametrar med formatet "application/x-www-form-urlencoded" med en teckenkodning av UTF-8 i http-begäranhetenhet-body. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\> Om begäran om åtkomsttoken är giltig och auktoriserad utfärdar auktoriseringsservern en åtkomsttoken och valfri uppdateringstoken och konstruerar svaret genom att lägga till följande parametrar i entiteten för HTTP-svaret med statuskoden 200 (OK). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p\> Klienten initierar flödet genom att dirigera resursägarens användaragent till auktoriseringsslutpunkten.  Klienten innehåller klientidentifieraren, det begärda scopet, det lokala tillståndet och en uri för omdirigering som auktoriseringsservern skickar tillbaka användaragenten när åtkomsten har beviljats (eller nekats). </p\> <p\> Auktoriseringsservern autentiserar resursägaren (via användaragenten) och fastställer om resursägaren beviljar eller nekar klientens åtkomstbegäran. </p\> <p\> Förutsatt att resursägaren beviljar åtkomst omdirigerar auktoriseringsservern tillbaka användaren och agenten till klienten med hjälp av den omdirigerings-URI som angavs tidigare.  Omdirigerings-URI innehåller åtkomsttoken i URI-fragmentet. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p\> Om resursägaren nekar åtkomstbegäran eller om begäran misslyckas av andra skäl än en saknad eller ogiltig omdirigering URI, informerar auktoriseringsservern klienten genom att lägga till följande parametrar i fragmentkomponenten i omdirigerings-URI med hjälp av formatet "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p\> Klientappen måste skicka användaren till auktoriseringsslutpunkten för att kunna initiera OAuth-processen.      Vid auktoriseringsslutpunkten autentiserar användaren och beviljar eller nekar sedan åtkomst till appen. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p\> Om resursägaren beviljar åtkomstbegäran utfärdar auktoriseringsservern en åtkomsttoken och levererar den till klienten genom att lägga till följande parametrar i fragmentkomponenten i omdirigerings-URI med formatet "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Auktoriseringskodflödet är optimerat för klienter som kan upprätthålla sekretessen för sina autentiseringsuppgifter (t.ex. webbserverprogram som implementeras med PHP, Java, Python, Ruby, ASP.NET, etc.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Bidrag till auktoriseringskod|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Klientautentiseringsflödet är lämpligt i de fall då klienten (ditt program) begär åtkomst till de skyddade resurserna under dess kontroll. Klienten betraktas som en resursägare, så ingen slutanvändares interaktion krävs.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Beviljande av klientautentiseringsuppgifter|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicit flöde är optimerat för klienter som inte kan upprätthålla sekretessen för sina autentiseringsuppgifter kända för att driva en viss omdirigering URI. Dessa klienter implementeras vanligtvis i en webbläsare med ett skriptspråk som JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit bidrag|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Flödet för lösenord för resursägare är lämpligt i de fall där resursägaren har en förtroenderelation med klienten (ditt program), till exempel enhetsoperativsystemet eller ett mycket privilegierat program. Det här flödet är lämpligt för klienter som kan hämta resursägarens autentiseringsuppgifter (användarnamn och lösenord, vanligtvis med hjälp av ett interaktivt formulär).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Beviljande av lösenord för resursägare|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p\> Resursägaren förser klienten med sitt användarnamn och lösenord. </p\> <p\> Klienten begär en åtkomsttoken från auktoriseringsserverns tokenslutpunkt genom att inkludera autentiseringsuppgifterna som tas emot från resursägaren.  När begäran görs autentiserar klienten med auktoriseringsservern. </p\> <p\> Auktoriseringsservern autentiserar klienten och validerar resursägarens autentiseringsuppgifter och utfärdar en åtkomsttoken om det är giltigt. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\> Om begäran misslyckades klientautentisering eller är ogiltig auktoriseringsservern svarar med en HTTP 400 (Bad Request) statuskod (om inte annat anges) och innehåller följande parametrar med svaret. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p\> Klienten gör en begäran till tokenslutpunkten genom att lägga till följande parametrar med formatet "application/x-www-form-urlencoded" med en teckenkodning av UTF-8 i http-begäranhetenhet-body. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\> Om begäran om åtkomsttoken är giltig och auktoriserad utfärdar auktoriseringsservern en åtkomsttoken och valfri uppdateringstoken och konstruerar svaret genom att lägga till följande parametrar i entiteten för HTTP-svaret med statuskoden 200 (OK). </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Begäran om åtkomsttoken|  
|OAuth2Step_AuthorizationRequest_Name|Begäran om auktorisering|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Krävs. Åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Krävs. Åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Krävs. Åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Krävs. Åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Krävs. Klientidentifierare.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|Krävs om klienten inte autentiserar med auktoriseringsservern.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Krävs. Klientidentifieraren.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Krävs. Auktoriseringskoden som genereras av auktoriseringsservern.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Krävs. Auktoriseringskoden som tas emot från auktoriseringsservern.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Valfri. Läsbar ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Valfri. Läsbar ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Valfri. Läsbar ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Valfri. Läsbar ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Valfri. Läsbar ASCII-text som ger ytterligare information.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Valfri. En URI som identifierar en webbsida som kan läsas av människor med information om felet.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Valfri. En URI som identifierar en webbsida som kan läsas av människor med information om felet.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Valfri. En URI som identifierar en webbsida som kan läsas av människor med information om felet.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Valfri. En URI som identifierar en webbsida som kan läsas av människor med information om felet.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Valfri. En URI som identifierar en webbsida som kan läsas av människor med information om felet.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Krävs. En enda ASCII-felkod från följande: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Krävs. En enda ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Krävs. En enda ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Krävs. En enda ASCII-felkod från följande: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Krävs. En enda ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Rekommenderas. Livstiden i sekunder för åtkomsttoken.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Rekommenderas. Livstiden i sekunder för åtkomsttoken.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Rekommenderas. Livstiden i sekunder för åtkomsttoken.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Rekommenderas. Livstiden i sekunder för åtkomsttoken.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Krävs. Värdet MÅSTE anges till "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Krävs. Värdet MÅSTE anges till "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Krävs. Värdet MÅSTE ställas in på "lösenord".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Krävs. Lösenordet för resursägaren.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Valfri. Omdirigeringsslutpunkten URI måste vara en absolut URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|KRÄVS om parametern "redirect_uri" ingick i auktoriseringsbegäran och deras värden måste vara identiska.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Valfri. Omdirigeringsslutpunkten URI måste vara en absolut URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Valfri. Uppdateringstoken, som kan användas för att hämta nya åtkomsttoken.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Valfri. Uppdateringstoken, som kan användas för att hämta nya åtkomsttoken.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Valfri. Uppdateringstoken, som kan användas för att hämta nya åtkomsttoken.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Krävs. Värdet MÅSTE anges till "kod".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Krävs. Värdet MÅSTE anges till "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Valfri. Omfattningen av åtkomstbegäran.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|VALFRITT om det är identiskt med det scope som begärs av klienten. annars krävs.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Valfri. Omfattningen av åtkomstbegäran.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|FRIVILLIGT, om det är identiskt med det scope som begärs av klienten; annars krävs.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Valfri. Omfattningen av åtkomstbegäran.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|VALFRITT om det är identiskt med det scope som begärs av klienten. annars krävs.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Valfri. Omfattningen av åtkomstbegäran.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|FRIVILLIGT, om det är identiskt med det scope som begärs av klienten; annars krävs.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|Krävs om parametern "tillstånd" fanns i klientauktoriseringsbegäran.  Det exakta värdet som mottagits från klienten.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Rekommenderas. Ett ogenomskinligt värde som används av klienten för att upprätthålla tillståndet mellan begäran och motringning.  Auktoriseringsservern innehåller det här värdet när du omdirigerar användaren och agenten tillbaka till klienten.  Parametern BÖR användas för att förhindra förfalskning av begäranden mellan webbplatser.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|Krävs om parametern "tillstånd" fanns i klientauktoriseringsbegäran.  Det exakta värdet som mottagits från klienten.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|Krävs om parametern "tillstånd" fanns i klientauktoriseringsbegäran.  Det exakta värdet som mottagits från klienten.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Rekommenderas. Ett ogenomskinligt värde som används av klienten för att upprätthålla tillståndet mellan begäran och motringning.  Auktoriseringsservern innehåller det här värdet när du omdirigerar användaren och agenten tillbaka till klienten.  Parametern BÖR användas för att förhindra förfalskning av begäranden mellan webbplatser.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|Krävs om parametern "tillstånd" fanns i klientauktoriseringsbegäran.  Det exakta värdet som mottagits från klienten.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Krävs. Typen av token som utfärdas.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Krävs. Typen av token som utfärdas.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Krävs. Typen av token som utfärdas.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Krävs. Typen av token som utfärdas.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Krävs. Resursägarens användarnamn.|  
|OAuth2OstöddTokenType|Tokentyp{0}' ' stöds inte.|  
|OAuth2InvalidState|Ogiltigt svar från auktoriseringsserver|  
|OAuth2GrantType_AuthorizationCode|Authorization code (Auktoriseringskod)|  
|OAuth2GrantType_Implicit|Implicit|  
|OAuth2GrantType_ClientCredentials|Klientautentiseringsuppgifter|  
|OAuth2GrantType_ResourceOwnerPassword|Lösenord för resursägare|  
|WebDokumentation302Code|302 Hittade|  
|WebDokumentation400Code|400 (Dålig begäran)|  
|OAuth2SendingMethod_AuthHeader|Auktoriseringshuvud|  
|OAuth2SendingMethod_QueryParam|Parameter för fråga|  
|OAuth2AuthorizationServerGeneralException|Ett fel uppstod när åtkomst tillåts via{0}|  
|OAuth2AuthorizationServerCommunicationException|Det gick inte att upprätta en HTTP-anslutning till auktoriseringsserver eller så har den oväntat stängts.|  
|WebDokumentationOAuth2GeneralErrorMessage|Ett oväntat fel uppstod.|  
|AuktoriseringServerkommunikationUttöste|Meddelandeserverkommunikationsundantag har inträffat. Kontakta administratören.|  
|TextblockAbonnemangKeyHeaderDescription|Prenumerationsnyckel som ger åtkomst till det här API:et. Finns i din <en href='/developer'\>Profil</a\>.|  
|TextblockOAuthHeaderDescription|OAuth 2.0-åtkomsttoken som erhållits från <i\> {0}</i\>. Bidragstyper som stöds:\> {1} <i</i\>.|  
|TextblockContentTypeHeaderDescription|Medietyp för brödtexten som skickas till API:et.|  
|ErrorMessageApiNAccessible|API:et som du försöker anropa är inte tillgängligt just nu. Kontakta API-utgivaren <a href="/issues"\>här</a\>.|  
|ErrorMessageApiTimedout|API:et som du försöker anropa tar längre tid än normalt att få tillbaka svaret. Kontakta API-utgivaren <a href="/issues"\>här</a\>.|  
|BadRequestParameterExpecterad|"'{0}' parameter förväntas"|  
|TooltipTextDoubleClickToSelectAll|Dubbelklicka för att markera alla.|  
|TooltipTextHideRevealSecret|Visa/dölj|  
|ButtonLinkOpenConsole|Prova|  
|AvsnittHuvudEnRequestBody|Begärandetext|  
|AvsnittHuvudEnRequestParameters|Parametrar för begäran|  
|AvsnittHuvudEnRequestUrl|URL för begäran|  
|AvsnittHuvudsvar|Svar|  
|AvsnittHuvudenRequestHeaders|Begärandehuvuden|  
|FormulärLabelSubtextOptional|valfri|  
|SectionHeadingCodeSamples|Kodexempel|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect ID-token som hämtats från <i\> {0}</i\>. Bidragstyper som stöds:\> {1} <i</i\>.|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Namn|Text|  
|----------|----------|  
|LänkLabel Tillbaka|tillbaka|  
|LinkLabelHomePage|hemsida|  
|LinkLabelSendUsEmail|Skicka oss ett e-postmeddelande|  
|PageTitleError|Tyvärr uppstod ett problem med att visa den begärda sidan|  
|TextblockPotentialCauseIntermittentIssue|Detta kan vara ett återkommande dataåtkomstproblem som redan är borta.|  
|TextblockPotentialCauseOldLink|Länken du har klickat på kan vara gammal och inte peka på rätt plats längre.|  
|TextblockPotentialCauseTechnicalProblem|Det kan finnas ett tekniskt problem på vår sida.|  
|TextblockPotentialLösningRefresh|Prova med att uppdatera sidan.|  
|TextblockPotentialSolutionStartOver|Börja om {0}från vår .|  
|TextblockPotentialSolutionTryAgain|Gå {0} och prova åtgärden du utförde igen.|  
|TextReportProblem|{0}beskriver vad som gick fel och vi kommer att titta på det så snart vi kan.|  
|TitlePotentialCause|Potentiell orsak|  
|TitelPotentialLösning|Det är kanske bara en tillfällig fråga, några saker att prova|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a>IssuesStrings  
  
|Namn|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Problem|  
|WebIssuesNoActiveAbonnemang|Du har inga aktiva prenumerationer. Du måste prenumerera på en produkt för att rapportera ett problem.|  
|WebIssuesNotSignin|Du är inte inloggad. Vänligen {0} rapportera ett problem eller skriva en kommentar.|  
|WebIssuesReportIssueButton|Rapportproblem|  
|WebIssuesSignIn|logga in|  
|WebIssuesStatusReportedBy|Status: {0} &#124; rapporterat av{1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a>NotFoundStrings  
  
|Namn|Text|  
|----------|----------|  
|LinkLabelHomePage|hemsida|  
|LinkLabelSendUsEmail|skicka oss ett e-postmeddelande|  
|PageTitleNotFound|Tyvärr kan vi inte hitta den sida du letar efter|  
|TextblockPotentialCauseMisspelledUrl|Du kan ha felstavat webbadressen om du skrev in den.|  
|TextblockPotentialCauseOldLink|Länken du har klickat på kan vara gammal och inte peka på rätt plats längre.|  
|TextblockPotentialLösningRetyp|Försök att skriva in webbadressen igen.|  
|TextblockPotentialSolutionStartOver|Börja om {0}från vår .|  
|TextReportProblem|{0}beskriver vad som gick fel och vi kommer att titta på det så snart vi kan.|  
|TitlePotentialCause|Potentiell orsak|  
|TitelPotentialLösning|Potentiell lösning|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Namn|Text|  
|----------|----------|  
|WebProductsAgreement|Genom att {0} prenumerera på produkten, godkänner jag `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Användningsvillkor|  
|WebProductsSubscribeButton|Prenumerera|  
|WebProductsUsageLimitsHeader|Användningsgränser|  
|WebProductsYouAreNotSprenumerera|Du prenumererar på denna produkt.|  
|WebProductsYouRequestedAbonnemang|Du har begärt prenumeration på den här produkten.|  
|ErrorYouNeedToAgreeWithLegalTerms|Du måste godkänna användarvillkoren innan du kan fortsätta.|  
|KnappLabelAddAbonnemang|Lägg till en prenumeration|  
|LinkLabelChangeSubscriptionName|ändra|  
|ButtonLabelKonkrök|Bekräfta|  
|TextblockMultipleSubscriptionsCount|Du {0} har prenumerationer på den här produkten:|  
|TextblockSingleSubscriptionsCount|Du {0} har prenumeration på den här produkten:|  
|TextblockSingleApisCount|Den här {0} produkten innehåller API:|  
|TextblockMultipleApisCount|Den här {0} produkten innehåller API:er:|  
|TextblockHeaderPrenumerera|Prenumerera på produkten|  
|TextblockAbonnemangBeskrivning|En ny prenumeration skapas på följande sätt:|  
|TextblockAbonnemangLimitReached|Gränsen för prenumerationer har nåtts.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a>ProdukterSträngar  
  
|Namn|Text|  
|----------|----------|  
|PageTitleProdukter|Produkter|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Namn|Text|  
|----------|----------|  
|TextboxExternalId-enheterDisabled|Logga in är inaktiverat av administratörerna för tillfället.|  
|TextboxExternalId-enheterSigninInvitation|Alternativt kan du logga in med|  
|TextboxExternalId-enheterSigninIninvitationPrimary|Logga in med:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a>Logga inResurser  
  
|Namn|Text|  
|----------|----------|  
|PrincipalNotFound (Huvudsakligt Saknar inte)|Huvudnamn hittades inte eller signaturen är ogiltig|  
|ErrorSsoAuthenticationFailed|SSO-autentisering misslyckades|  
|ErrorSsoAuthenticationFailedDetailed|Ogiltig token eller signatur kan inte verifieras.|  
|ErrorSsoTokenInvalid|SSO-token är ogiltig|  
|ValideringErrorSpecificEmailAlreadyExister|E-post '{0}' redan registrerad|  
|ValideringErrorSpecificEmailInvalid|E-post '{0}' är ogiltigt|  
|ValideringErrorPasswordInvalid|Lösenordet är ogiltigt. Korrigera felen och försök igen.|  
|Fastighetsförmedling|{0}är för kort|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Ogiltig e-postadress.|  
|ValideringMessageNewPasswordConfirmationRequired|Bekräfta nytt lösenord|  
|ValideringErrorPasswordConfirmationRequired|Bekräfta att lösenordet är tomt|  
|WebAuthenticationEmailChangeNotice|Ändra bekräftelse e-post {0}är på väg till . Följ instruktionerna i den för att bekräfta din nya e-postadress. Om e-postmeddelandet inte kommer till din inkorg inom de närmaste minuterna, kontrollera din skräppostmapp.|  
|WebAuthenticationEmailChangeNoticeHeader|Begäran om e-poständring har bearbetats|  
|WebAuthenticationEmailChangeNoticeTitle|Begärd e-poständring|  
|WebauthenticationEmailHasBeenRevertedNotice|Du e-post finns redan. Begäran har återställts|  
|ValideringErrorEmailAltörerExister|E-post finns redan|  
|ValideringErrorEmailInvalid|Ogiltig e-postadress|  
|TextboxLabelEmail|E-post|  
|ValideringErrorEmailRequired|E-postadress måste anges.|  
|WebAuthenticationErrorNoticeHeader|Fel|  
|WebAuthenticationFieldLengthErrorMessage|{0}måste vara en maximal längd på{1}|  
|TextboxLabelEmailFirstName|Förnamn|  
|ValidationErrorFirstNameRequired|Förnamn krävs.|  
|ValideringErrorFirstNameInvalid|Ogiltigt förnamn|  
|NoticeInvalidInvitationToken|Observera att bekräftelselänkar endast gäller i 48 timmar. Om du fortfarande befinner dig inom denna tidsram, se till att din länk är korrekt. Om din länk har gått ut upprepar du åtgärden som du försöker bekräfta.|  
|MeddelandeHeaderInvalidInvitationToken|Ogiltig inbjudningstoken|  
|NoticeTitleInvalidInvitationToken|Bekräftelsefel|  
|WebAuthenticationLastNameInvalidErrorMessage|Ogiltigt efternamn|  
|TextboxLabelEmailLästernamn|Efternamn|  
|ValidationErrorLastNameRequired|Efternamn krävs.|  
|WebAuthenticationLinkExpiredNotice|Bekräftelselänken som skickats till dig har upphört att gälla. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpirerad|Länken för återställning av lösenord är ogiltig eller har upphört att gälla.|  
|WebAuthenticationLinkExpiredNoticeTitle|Länk skickad|  
|WebAuthenticationNewPasswordLabel|Nytt lösenord|  
|ValideringMessageNewPasswordRequired|Nytt lösenord krävs.|  
|TextboxLabelAnmälandeSenderEmail|E-post till meddelanden|  
|TextboxLabelOrganizationName|Organisationsnamn|  
|WebAuthenticationOrganizationRequiredErrorMessage|Organisationsnamnet är tomt|  
|WebAuthenticationPasswordChangedNotice|Lösenordet har uppdaterats|  
|WebAuthenticationPasswordChangedNoticeTitle|Lösenordet har uppdaterats|  
|WebAuthenticationPasswordCompareErrorMessage|Lösenord matchar inte|  
|WebAuthenticationPasswordConfirmLabel|Bekräfta lösenord|  
|ValideringErrorPasswordInvalidDetailed|Lösenordet är för svagt.|  
|WebauthenticationPasswordLabel|lösenord|  
|ValideringErrorPasswordRequired|Lösenord måste anges.|  
|WebAuthenticationPasswordResetSendNotice|Ändra e-post för lösenordsbekräftelse är på väg till {0}. Följ instruktionerna i e-postmeddelandet för att fortsätta din lösenordsändringsprocess.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Begäran om återställning av lösenord har bearbetats|  
|WebAuthenticationPasswordResetSendNoticeTitle|Lösenordsåterställning begärd|  
|WebAuthenticationRequestNotFoundNotice|Begäran hittades inte|  
|WebAuthenticationSenderEmailRequiredErrorMessage|E-postmeddelandet är tomt|  
|WebAuthenticationSigninPasswordLabel|Bekräfta ändringen genom att ange ett lösenord|  
|WebAuthenticationSignupConfirmNotice|Registreringsbekräftelse {0}e-post är på\> väg till .<br / Följ instruktionerna i\> e-postmeddelandet för att aktivera ditt konto.<br / Om e-postmeddelandet inte kommer i din inkorg inom de närmaste minuterna, kontrollera din skräppostmapp.|  
|WebAuthenticationSignupConfirmNoticeHeader|Ditt konto har skapats|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|E-post för registreringsbekräftelse skickades igen|  
|WebAuthenticationSignupKonfirmEratIntsTitle|Skapat konto|  
|WebAuthenticationTokenRequiredErrorMessage|Token är tom|  
|WebAuthenticationUserAlreadyRegisteredNotice|Det verkar en användare med detta mail är redan registrerad i systemet. Om du har glömt ditt lösenord, försök att återställa det eller kontakta vårt supportteam.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Användaren har redan registrerats|  
|WebAuthenticationUserAlreadyRegistreradNoticeTitle|Redan registrerad|  
|KnappLabelChangePassword|Ändra lösenord|  
|KnappLabelChangeAccountInfo|Ändra kontoinformation|  
|ButtonLabelCloseAccount|Stäng konto|  
|WebAuthenticationInvalidCaptchaErrorMessage|Texten som skrivs in matchar inte texten på bilden. Försök igen.|  
|ValideringErrorCredentialsInvalid|E-post eller lösenord är ogiltigt. Korrigera felen och försök igen.|  
|WebAuthenticationRequestIsNotValid|Begäran är ogiltig|  
|WebAuthenticationUserIsNotConfirm|Bekräfta din registrering innan du försöker logga in.|  
|WebauthenticationInvalidemailFormaterad|E-post är ogiltig:{0}|  
|WebAuthenticationUserIntenelse|Användaren kunde inte hittas|  
|WebAuthenticationTenantNotRegistrerad|Ditt konto tillhör en Azure Active Directory-klient som inte har behörighet att komma åt den här portalen.|  
|WebAuthenticationAuthenticationFailed|Autentiseringen misslyckades.|  
|WebAuthenticationGooglePlusNotEnabled|Autentiseringen misslyckades. Om du har auktoriserat programmet kontaktar du administratören för att se till att Googles autentisering är korrekt konfigurerad.|  
|ValidationErrorTillämptATenantIsRequired|Tillåten klient krävs|  
|ValideringErrorTenantIsNotValid|Azure Active Directory-klienten '{0}' är ogiltig.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginAnvändandeDinprovider|Logga in {0} med ditt konto|  
|WebAuthenticationUserLimitNotice|Den här tjänsten har nått det maximala antalet tillåtna användare. Uppgradera `<a href="mailto:{0}"\>contact the administrator</a\>` deras tjänst och återaktivera användarregistreringen.|  
|WebAuthenticationUserLimitNoticeHeader|Användarregistrering inaktiverad|  
|WebAuthenticationUserLimitNoticeTitle|Användarregistrering inaktiverad|  
|WebAuthenticationAnvändarnaVisaVislandDisabledNotice|Registrering av användare har inaktiverats av administratören. Logga in hos extern identitetsleverantör.|  
|WebAuthenticationAnvändarnaVisaVislandDisabledNoticeHeader|Användarregistrering inaktiverad|  
|WebAuthenticationAnvändarregistrationDisabledNoticeTitle|Användarregistrering inaktiverad|  
|WebAuthenticationSignupPendingConfirmationNotice|Innan vi kan slutföra skapandet av ditt konto måste vi verifiera din e-postadress. Vi har skickat ett e-postmeddelande till {0}. Följ instruktionerna i e-postmeddelandet för att aktivera ditt konto. Om e-postmeddelandet inte kommer inom de närmaste minuterna, kontrollera din skräppostmapp.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Vi hittade ett obekräftat konto {0}för e-postadressen. För att slutföra skapandet av ditt konto måste vi verifiera din e-postadress. Vi har skickat ett e-postmeddelande till {0}. Följ instruktionerna i e-postmeddelandet för att aktivera ditt konto. Om e-postmeddelandet inte kommer inom de närmaste minuterna, kontrollera din skräppostmapp|  
|WebAuthenticationSignupConfirmationNästanDone|Nästan klar|  
|WebAuthenticationSignupConfirmationEmailSent|Vi har skickat ett e-postmeddelande till {0}. Följ instruktionerna i e-postmeddelandet för att aktivera ditt konto. Om e-postmeddelandet inte kommer inom de närmaste minuterna, kontrollera din skräppostmapp.|  
|WebAuthenticationEmailSentAnmälelseMessage|E-post som skickats till{0}|  
|WebAuthenticationNoAadTenantKonfigurerad|Ingen Azure Active Directory-klient som konfigurerats för tjänsten.|  
|KryssrutaLabelUserRegistrationTermsConsentRequired|Jag går `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`med på .|  
|TextblockUserRegistrationTermsProvided|Läs igenom`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Användningsvillkor|  
|ValideringMessageConsentNotAccepted|Du måste godkänna användarvillkoren innan du kan fortsätta.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a>TeckeninStrings  
  
|Namn|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Glömt ditt lösenord?|  
|WebAuthenticationIfAdministrator|Om du är administratör måste `<a href="{0}"\>here</a\>`du logga in .|  
|WebAuthenticationNotAMember|Inte medlem ännu? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Kom ihåg mig på den här datorn|  
|WebauthenticationSiginWithPassword|Logga in med ditt användarnamn och lösenord|  
|WebAuthenticationSigninTitle|Logga in|  
|WebAuthenticationSignUpNow|Registrera dig nu|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a>Registreringsstrubbningar  
  
|Namn|Text|  
|----------|----------|  
|PageTitleSignup|Registrera dig|  
|WebAuthenticationAlreadyAMember|Redan medlem?|  
|WebAuthenticationSkapNewAccount|Skapa ett nytt API Management-konto|  
|WebAuthenticationSigninNow|Logga in nu|  
|KnappLabelSignup|Registrera dig|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a>PrenumerationslistaSträngar  
  
|Namn|Text|  
|----------|----------|  
|PrenumerationKancelBekräfta|Vill du avbryta prenumerationen?|  
|PrenumerationFörnyaKonkrök|Vill du förnya prenumerationen?|  
|WebDevelopersManageAbonnemang|Hantera prenumerationer|  
|WebDevelopersPrimaryKey|Primärnyckel|  
|WebDevelopersRegenerateLink|Återskapa|  
|WebDevelopersSecondaryKey|Sekundär nyckel|  
|ButtonLabelShowKey|Visa|  
|KnappLabelRenewAbonnemang|Förnya|  
|WebDevelopersAbonnemangBegräserad|Begärd den{0}|  
|WebDevelopersSubscriptionRequestedState|Begärd|  
|WebDevelopersSubscriptionTableNameHeader|Namn|  
|WebDevelopersSubscriptionTableStateHeader|Status|  
|WebDevelopersUsageStatisticsLink|Analytics-rapporter|  
|WebDevelopersYourSubscriptions|Dina prenumerationer|  
|PrenumerationFörslagSmanet begärsDate|Begärd den|  
|PrenumerationFörslagSabelStartedDate|Började på|  
|PageTitleRenameAbonnemang|Byt namn på prenumeration|  
|PrenumerationErtyLabelName|Prenumerationens namn|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a>PrenumerationStrings  
  
|Namn|Text|  
|----------|----------|  
|AvsnittRubrikErSknäckkonto|Vill du stänga ditt konto?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Dölj|  
|KnappLabelRegenerateKey|Återskapa|  
|InformationMessageKeyVarregent|Är du säker på att du vill återskapa den här nyckeln?|  
|ButtonLabelShowKey|Visa|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Namn|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Uppdatera profil|  
|PageTitleUpdateProfile|Uppdatera kontoinformation|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a>Userprofile  
  
|Namn|Text|  
|----------|----------|  
|KnappLabelChangeAccountInfo|Ändra kontoinformation|  
|KnappLabelChangePassword|Ändra lösenord|  
|ButtonLabelCloseAccount|Stäng konto|  
|TextboxLabelEmail|E-post|  
|TextboxLabelEmailFirstName|Förnamn|  
|TextboxLabelEmailLästernamn|Efternamn|  
|TextboxLabelAnmälandeSenderEmail|E-post till meddelanden|  
|TextboxLabelOrganizationName|Organisationsnamn|  
|SubscriptionStateActive|Active|  
|SubscriptionStateKancelled|Avbrutet|  
|SubscriptionStateExpired|Upphörd|  
|SubscriptionStateRejected|Avvisad|  
|SubscriptionStateRequested|Begärd|  
|PrenumerationStatSanvändare|Inaktiverad|  
|StandardAbonnemangNameTemplate|{0}(standard)|  
|SubscriptionNameTemplate|Tillgång till utvecklare #{0}|  
|TextboxLabelSubscriptionName|Prenumerationens namn|  
|ValideringMessageSubscriptionNameRequired|Prenumerationsnamnet får inte vara tomt.|  
|ApiManagementUserLimitReached|Den här tjänsten har nått det maximala antalet tillåtna användare. Uppgradera till en högre prisnivå.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a>Glyfer-resurser  
 API Management developer portal mallar kan använda glyfer från [Glyphicons från Bootstrap](https://getbootstrap.com/components/#glyphicons). Denna uppsättning glyfer innehåller över 250 glyfer i teckensnittsformat från [Glyphicon](https://glyphicons.com/) Halflings-uppsättningen. Om du vill använda en glyf från den här uppsättningen använder du följande syntax.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 För den fullständiga listan över glyfer, se [Glyphicons från Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](api-management-developer-portal-templates.md)för API Management med hjälp av mallar .
