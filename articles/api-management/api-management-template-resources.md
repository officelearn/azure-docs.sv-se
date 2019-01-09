---
title: Azure API Management-Mallresurser | Microsoft Docs
description: Läs mer om vilka typer av resurser som är tillgängliga för användning i utvecklarportalsmallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: cfbb04ea56eacfd859b6ce520da5396ad91d9abf
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121302"
---
# <a name="azure-api-management-template-resources"></a>Azure API Management-Mallresurser
Azure API Management ger följande typer av resurser för användning i utvecklaren portal mallar.  
  
-   [Strängresurser](#strings)  
  
-   [Tecken resurser](#glyphs)  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a> Strängresurser  
 API Management ger en omfattande uppsättning strängresurserna för användning i developer-portalen. Dessa resurser är lokaliserade till alla språk som stöds av API Management. En standarduppsättning av mallar använder de här resurserna för sidhuvuden, etiketter och alla konstant strängar som visas i developer-portalen. Om du vill använda en strängresurs i dina mallar, anger du prefixet resource sträng följt av namnet på sträng, som visas i följande exempel.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 I följande exempel från listmall produkt och visar **produkter** överst på sidan.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
 Se följande tabell innehåller strängresurserna som är tillgängliga för användning i din utvecklarportalsmallar. Använd tabellnamnet som prefix för strängresurserna i tabellen.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Dokumentation](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [Användarprofil](#UserProfile)  
  
###  <a name="ApisStrings"></a> ApisStrings  
  
|Namn|Text|  
|----------|----------|  
|PageTitleApis|API:er|  
  
###  <a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Namn|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Förhandsversion av program|  
|WebApplicationsRequirementsHeader|Krav|  
|WebApplicationsScreenshotAlt|Skärmbild|  
|WebApplicationsScreenshotsHeader|Skärmbilder|  
  
###  <a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Namn|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Vill du ta bort programmet?|  
|WebDevelopersAppNotPublished|Inte publicerade|  
|WebDevelopersAppNotSubmitted|Inte skickat|  
|WebDevelopersAppTableCategoryHeader|Kategori|  
|WebDevelopersAppTableNameHeader|Namn|  
|WebDevelopersAppTableStateHeader|Status|  
|WebDevelopersEditLink|Redigera|  
|WebDevelopersRegisterAppLink|Registrera program|  
|WebDevelopersRemoveLink|Ta bort|  
|WebDevelopersSubmitLink|Skicka|  
|WebDevelopersYourApplicationsHeader|Dina program|  
  
###  <a name="AppStrings"></a> AppStrings  
  
|Namn|Text|  
|----------|----------|  
|WebApplicationsHeader|Appar|  
  
###  <a name="CommonResources"></a> CommonResources  
  
|Namn|Text|  
|----------|----------|  
|NoItemsToDisplay|Hittade inga resultat.|  
|GeneralExceptionMessage|Något är inte rätt. Det kan vara ett tillfälligt glapp eller en bugg. Försök igen.|  
|GeneralJsonExceptionMessage|Något är inte rätt. Det kan vara ett tillfälligt glapp eller en bugg. ., Läs in sidan och försök igen.|  
|ConfirmationMessageUnsavedChanges|Det finns vissa ändringar. Är du säker på att du vill avbryta och ta bort ändringarna?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http-begärans innehåll är för stort.|  
  
###  <a name="CommonStrings"></a> CommonStrings  
  
|Namn|Text|  
|----------|----------|  
|ButtonLabelCancel|Avbryt|  
|ButtonLabelSave|Spara|  
|GeneralExceptionMessage|Något är inte rätt. Det kan vara ett tillfälligt glapp eller en bugg. Försök igen.|  
|NoItemsToDisplay|Det finns inga objekt att visa.|  
|PagerButtonLabelFirst|Första|  
|PagerButtonLabelLast|Sista|  
|PagerButtonLabelNext|Nästa|  
|PagerButtonLabelPrevious|Föreg.|  
|PagerLabelPageNOfM|Sidan {0} av {1}|  
|PasswordTooShort|Lösenordet är för kort|  
|EmailAsPassword|Använd inte din e-postadress som lösenord|  
|PasswordSameAsUserName|Lösenordet får inte innehålla ditt användarnamn|  
|PasswordTwoCharacterClasses|Använd olika teckenklasser|  
|PasswordTooManyRepetitions|För många repetitioner|  
|PasswordSequenceFound|Lösenordet innehåller sekvenser|  
|PagerLabelPageSize|Sidstorlek|  
|CurtainLabelLoading|Laddar...|  
|TablePlaceholderNothingToDisplay|Det finns inga data för den valda perioden och omfånget|  
|ButtonLabelClose|Stäng|  
  
###  <a name="Documentation"></a> Dokumentation  
  
|Namn|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Ogiltigt huvud '{0}'|  
|WebDocumentationInvalidRequestErrorMessage|Ogiltig URL för begäran|  
|TextboxLabelAccessToken|Access token *|  
|DropdownOptionPrimaryKeyFormat|Primär-{0}|  
|DropdownOptionSecondaryKeyFormat|Sekundär-{0}|  
|WebDocumentationSubscriptionKeyText|Din prenumerationsnyckel|  
|WebDocumentationTemplatesAddHeaders|Lägg till de HTTP-sidhuvuden som krävs|  
|WebDocumentationTemplatesBasicAuthSample|Basic Authorization-exempel|  
|WebDocumentationTemplatesCurlForBasicAuth|för Basic Authorization använder du: --user {username}:{password}|  
|WebDocumentationTemplatesCurlValuesForPath|Ange värden för sökvägsparametrar (visas som {...}), prenumerationsnyckel och värden för frågeparametrar|  
|WebDocumentationTemplatesDeveloperKey|Ange prenumerationsnyckeln|  
|WebDocumentationTemplatesJavaApache|Det här exemplet används Apache HTTP-klienten från HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Ange värden för valfria parametrar om så behövs|  
|WebDocumentationTemplatesPhpPackage|Det här exemplet används HTTP_Request2-paketet. (Mer information: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Ange värden för sökvägsparametrar (visas som {...}) och förfrågans text om det behövs|  
|WebDocumentationTemplatesRequestBody|Ange förfrågans text|  
|WebDocumentationTemplatesRequiredParams|Ange värden för följande begärda parametrar|  
|WebDocumentationTemplatesValuesForPath|Ange värden för sökvägsparametrar (visas som {...})|  
|OAuth2AuthorizationEndpointDescription|Auktoriseringsslutpunkten används för att interagera med resursägaren och få ett auktoriseringsbeviljande.|  
|OAuth2AuthorizationEndpointName|Auktoriseringsslutpunkt|  
|OAuth2TokenEndpointDescription|Tokenslutpunkten används av klienten för att hämta en åtkomsttoken genom att uppvisa auktoriseringsbeviljande eller uppdateringstoken.|  
|OAuth2TokenEndpointName|Tokenslutpunkt|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> klienten startar flödet genom att dirigera ägare till resursen användar-agent till auktoriseringsslutpunkten.  Klienten lägger till dess klientidentifierare, begärda omfång, lokala tillstånd och en omdirigerings-URI som skickar auktoriseringsservern användaragenten tillbaka när åtkomst beviljas (eller nekas).     < /p\> < p\> auktoriseringsservern autentiserar resursägaren (via användar-agent) och upprättar om resursägaren beviljar eller nekar åtkomst klientbegäran.     < /p\> < p\> förutsatt att resursägaren beviljar åtkomst, omdirigerar auktoriseringsservern användaragenten tillbaka till klienten med hjälp av omdirigeringen-URI som angetts tidigare (i begäran eller under klienten registrati på).  Omdirigerings-URI som innehåller en auktoriseringskod och lokala tillstånd tillhandahålls av klienten tidigare.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> om användaren avvisar åtkomstbegäran för om begäran är ogiltig, klienten kommer att hålla dig informerad med följande parametrar läggs till i omdirigeringen: < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Begäran om auktorisering|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> klientappen måste skicka användaren till auktoriseringsslutpunkten för att initiera OAuth-processen.          Vid auktoriseringsslutpunkten autentiseras användaren och beviljas eller nekas åtkomst till appen.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> förutsatt att resursägaren beviljar åtkomst, omdirigerar auktoriseringsservern användaragenten tillbaka till klienten med hjälp av omdirigeringen-URI som angetts tidigare (i begäran eller under klientregistreringen).  Omdirigerings-URI som innehåller en auktoriseringskod och lokala tillstånd tillhandahålls av klienten tidigare. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> klienten begär en åtkomsttoken från auktoriseringsservern '' s tokenslutpunkten genom att inkludera den auktoriseringskod som togs emot i föregående steg.  När du gör begäran autentiseras klienten med auktoriseringsservern.  Klienten lägger till omdirigerings-URI som används för att hämta auktoriseringskod för verifiering. < /p\> < p\> auktoriseringsservern autentiserar klienten, verifierar Auktoriseringskoden och säkerställer att omdirigeringen-URI som togs emot matchar den URI som används för att dirigera om klienten i steg (C).  Om det är giltigt svarar auktoriseringsservern tillbaka med en åtkomsttoken och eventuellt en uppdateringstoken. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> om begäran om klientautentisering misslyckades eller är ogiltig, auktoriseringsservern svarar med statuskoden HTTP 400 (felaktig begäran) (om inget annat uppges) och lägger till följande parametrar med svaret. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> klienten skickar en begäran till tokenslutpunkten genom att skicka huvuddelen för följande parametrar i formatet ”application/x--www-form-urlencoded” med teckenkodning UTF-8 i HTTP-begäran. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> auktoriseringsservern utfärdar en åtkomsttoken och valfri uppdateringstoken och konstruerar svaret genom att lägga till följande parametrar i entitetstexten i HTTP-svar med 200 (OK) statuskoden. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> klienten autentiseras med auktoriseringsservern och begär en åtkomsttoken från tokenslutpunkten. < /p\> < p\> auktoriseringsservern autentiserar klienten och utfärdar en åtkomsttoken om det är giltigt. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> om begäran inte godkänns eller är ogiltig auktoriseringsservern svarar med statuskoden HTTP 400 (felaktig begäran) (om inget annat uppges) och lägger till följande parametrar med svaret. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klienten skickar en begäran till tokenslutpunkten genom att lägga till följande parametrar i formatet ”application/x--www-form-urlencoded” med teckenkodning UTF-8 i HTTP-begäran huvuddelen. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> om begäran om åtkomsttoken är giltig och godkänd, auktoriseringsservern utfärdar en åtkomsttoken och valfri uppdateringstoken och konstruerar svaret genom att lägga till följande parametrar i entitetstexten i HTTP-svar med 200  Statuskoden för (OK). < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> klienten startar flödet genom att dirigera resursägaren '' s användar-agent till auktoriseringsslutpunkten.  Klienten lägger till dess klientidentifierare, begärda omfång, lokala tillstånd och en omdirigerings-URI som skickar auktoriseringsservern användaragenten tillbaka när åtkomst beviljas (eller nekas). < /p\> < p\> auktoriseringsservern autentiserar resursägaren (via användar-agent) och upprättar om resursägaren beviljar eller nekar klienten '' s åtkomstbegäran. < /p\> < p\> förutsatt att resursägaren beviljar åtkomst, omdirigerar auktoriseringsservern användaragenten tillbaka till klienten med hjälp av omdirigerings-URI som angavs tidigare.  Omdirigerings-URI innehåller åtkomsttoken i URI-fragment. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> om resursägaren avvisar åtkomstbegäran eller om begäran inte av andra orsaker än en saknad eller ogiltig omdirigerings-URI, meddelar auktoriseringsservern klienten genom att lägga till följande parametrar fragment komponenten o f omdirigeringen-URI i formatet ”application/x--www-form-urlencoded”. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> klientappen måste skicka användaren till auktoriseringsslutpunkten för att initiera OAuth-processen.      Vid auktoriseringsslutpunkten autentiseras användaren och beviljas eller nekas åtkomst till appen. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> om resursägaren beviljar åtkomstbegäran, auktoriseringsservern utfärdar en åtkomsttoken och levererar den till klienten genom att lägga till följande parametrar i fragmentkomponenten för omdirigerings-URI i formatet ”application/x-www – utgör - URL-kodade ”-format. < /p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Auktoriseringskodflödet är optimerat för klienter som kan behandla autentiseringsuppgifterna konfidentiellt (t.ex. webbserverprogram som implementerats via PHP, Java, Python, Ruby, ASP.NET och så vidare).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Beviljande via auktoriseringskod|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Flödet är lämpligt i fall där klienten (ditt program) begär åtkomst till skyddade resurser under dess kontroll. Klienten betraktas som resursägare, så inga slutanvändaråtgärder krävs.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Beviljande via klientautentiseringsuppgifter|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicit flöde är optimerat för klienter som inte klarar att hålla sina autentiseringsuppgifter kända för att driva en viss omdirigerings-URI. De här klienterna är vanligtvis implementerade i en webbläsare med ett skriptspråk som till exempel JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit beviljande|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Resursägarens lösenord autentiseringsuppgifter flöde är lämpligt i fall där det finns en förtroenderelation med klienten (ditt program), till exempel enhetens operativsystem eller ett mycket privilegierat program. Det här flödet är lämplig för klienter som har tillgång till resursägarens autentiseringsuppgifter (användarnamn och lösenord, vanligtvis via ett interaktivt formulär).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Beviljande via resursägarens lösenordsautentiseringsuppgifter|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> resursägaren tillhandahåller till klienten med sitt användarnamn och lösenord. < /p\> < p\> klienten begär en åtkomsttoken från auktoriseringsservern '' s tokenslutpunkten genom att inkludera de autentiseringsuppgifter som togs emot från resursägaren.  När du gör begäran autentiseras klienten med auktoriseringsservern. < /p\> < p\> auktoriseringsservern autentiserar klienten och validerar autentiseringsuppgifter för resurs-ägare och utfärdar en åtkomsttoken om det är giltigt. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> om begäran inte godkänns eller är ogiltig auktoriseringsservern svarar med statuskoden HTTP 400 (felaktig begäran) (om inget annat uppges) och lägger till följande parametrar med svaret. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klienten skickar en begäran till tokenslutpunkten genom att lägga till följande parametrar i formatet ”application/x--www-form-urlencoded” med teckenkodning UTF-8 i HTTP-begäran huvuddelen. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> om begäran om åtkomsttoken är giltig och godkänd, auktoriseringsservern utfärdar en åtkomsttoken och valfri uppdateringstoken och konstruerar svaret genom att lägga till följande parametrar i entitetstexten i HTTP-svaret med 20 Statuskoden för 0 (OK). < /p\>|  
|OAuth2Step_AccessTokenRequest_Name|Begäran om åtkomsttoken|  
|OAuth2Step_AuthorizationRequest_Name|Begäran om auktorisering|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|KRÄVS. Åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|KRÄVS. Åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|KRÄVS. Åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|KRÄVS. Åtkomsttoken som utfärdats av auktoriseringsservern.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|KRÄVS. Klient-ID.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|OBLIGATORISKT om klienten inte autentiseras med auktoriseringsservern.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|KRÄVS. Klient-ID.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|KRÄVS. Auktoriseringskoden som skapas av auktoriseringsservern.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|KRÄVS. Auktoriseringskod som togs emot från auktoriseringsservern.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|VALFRITT. Läsbara ASCII-text med ytterligare information.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|VALFRITT. Läsbara ASCII-text med ytterligare information.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|VALFRITT. Läsbara ASCII-text med ytterligare information.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|VALFRITT. Läsbara ASCII-text med ytterligare information.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|VALFRITT. Läsbara ASCII-text med ytterligare information.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|VALFRITT. En URI som identifierar en läsbar webbsida med information om felet.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|VALFRITT. En URI som identifierar en läsbar webbsida med information om felet.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|VALFRITT. En URI som identifierar en läsbar webbsida med information om felet.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|VALFRITT. En URI som identifierar en läsbar webbsida med information om felet.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|VALFRITT. En URI som identifierar en läsbar webbsida med information om felet.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|KRÄVS. En enda ASCII-felkod från följande: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|KRÄVS. En enda ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|KRÄVS. En enda ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|KRÄVS. En enda ASCII-felkod från följande: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|KRÄVS. En enda ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|VI REKOMMENDERAR. Livstid i sekunder för åtkomsttoken.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|VI REKOMMENDERAR. Livstid i sekunder för åtkomsttoken.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|VI REKOMMENDERAR. Livstid i sekunder för åtkomsttoken.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VI REKOMMENDERAR. Livstid i sekunder för åtkomsttoken.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|KRÄVS. Värdet måste anges till ”authorization_code”.|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|KRÄVS. Värdet måste anges till ”client_credentials”.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|KRÄVS. Värdet måste vara ”Password”.|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|KRÄVS. Resursägarens lösenord.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|VALFRITT. Slutpunktens omdirigerings-URI måste vara en absolut URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|OBLIGATORISKT om parametern "redirect_uri" ingår i auktoriseringsbegäran, och värdena MÅSTE vara identiska.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|VALFRITT. Slutpunktens omdirigerings-URI måste vara en absolut URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|VALFRITT. Uppdateringstoken som kan användas för att hämta nya åtkomsttoken.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|VALFRITT. Uppdateringstoken som kan användas för att hämta nya åtkomsttoken.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VALFRITT. Uppdateringstoken som kan användas för att hämta nya åtkomsttoken.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|KRÄVS. Värdet måste vara ”Code”.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|KRÄVS. Värdet måste anges till ”token”.|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|VALFRITT. Omfånget för förfrågan.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|VALFRITT om identiskt med klientens begärda omfång, i annat fall OBLIGATORISKT.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|VALFRITT. Omfånget för förfrågan.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|VALFRITT, om identiskt med klientens begärda omfång, i annat fall OBLIGATORISKT.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|VALFRITT. Omfånget för förfrågan.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|VALFRITT om identiskt med klientens begärda omfång, i annat fall OBLIGATORISKT.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|VALFRITT. Omfånget för förfrågan.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VALFRITT, om identiskt med klientens begärda omfång, i annat fall OBLIGATORISKT.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|KRÄVS om parametern ”state” hittades i klientbegäran för auktorisering.  Den exakta värde som mottogs från klienten.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|VI REKOMMENDERAR. Ett täckande värde som används av klienten för att upprätthålla tillståndet mellan begäran och återanrop.  Auktoriseringsservern inkluderar det här värdet när du omdirigerar användaragenten tillbaka till klienten.  Parametern bör användas för att förhindra förfalskning av begäran mellan webbplatser.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|KRÄVS om parametern ”state” hittades i klientbegäran för auktorisering.  Den exakta värde som mottogs från klienten.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|KRÄVS om parametern ”state” hittades i klientbegäran för auktorisering.  Den exakta värde som mottogs från klienten.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|VI REKOMMENDERAR. Ett täckande värde som används av klienten för att upprätthålla tillståndet mellan begäran och återanrop.  Auktoriseringsservern inkluderar det här värdet när du omdirigerar användaragenten tillbaka till klienten.  Parametern bör användas för att förhindra förfalskning av begäran mellan webbplatser.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|KRÄVS om parametern ”state” hittades i klientbegäran för auktorisering.  Den exakta värde som mottogs från klienten.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|KRÄVS. Typ av Utfärdad token.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|KRÄVS. Typ av Utfärdad token.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|KRÄVS. Typ av Utfärdad token.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|KRÄVS. Typ av Utfärdad token.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|KRÄVS. Resursägarens användarnamn.|  
|OAuth2UnsupportedTokenType|Token typ '{0}' är inte tokentypen.|  
|OAuth2InvalidState|Ogiltigt svar från auktoriseringsservern|  
|OAuth2GrantType_AuthorizationCode|Auktoriseringskod|  
|OAuth2GrantType_Implicit|Implicit|  
|OAuth2GrantType_ClientCredentials|Klientautentiseringsuppgifter|  
|OAuth2GrantType_ResourceOwnerPassword|Lösenord för resursägare|  
|WebDocumentation302Code|302 hittades|  
|WebDocumentation400Code|400 (felaktig begäran)|  
|OAuth2SendingMethod_AuthHeader|Auktoriseringsrubrik|  
|OAuth2SendingMethod_QueryParam|Frågeparameter|  
|OAuth2AuthorizationServerGeneralException|Ett fel uppstod vid auktorisering av åtkomst via {0}|  
|OAuth2AuthorizationServerCommunicationException|Det gick inte att upprätta en HTTP-anslutning till auktoriseringsservern eller också har den oväntat stängts.|  
|WebDocumentationOAuth2GeneralErrorMessage|Det har uppstått ett oväntat fel.|  
|AuthorizationServerCommunicationException|Auktorisering server kommunikation undantagsfel har uppstått. Kontakta administratören.|  
|TextblockSubscriptionKeyHeaderDescription|Prenumerationsnyckel som ger åtkomst till den här API: et. Hittades i din < en href ='/ developer'\>profil < /a\>.|  
|TextblockOAuthHeaderDescription|OAuth 2.0-åtkomsttoken har erhållits från < jag\>{0}< /i\>. Beviljandetyper som stöds: < jag\>{1}< /i\>.|  
|TextblockContentTypeHeaderDescription|Medietyp i texten som skickas till API:et.|  
|ErrorMessageApiNotAccessible|Du försöker anropa API: et är inte tillgänglig just nu. Kontakta API-utgivaren < en href = ”/ issues”\>här < /a\>.|  
|ErrorMessageApiTimedout|API: et som du försöker anropa tar längre tid än normalt att få svar. Kontakta API-utgivaren < en href = ”/ issues”\>här < /a\>.|  
|BadRequestParameterExpected|””{0}”-parametern förväntas”|  
|TooltipTextDoubleClickToSelectAll|Dubbelklicka om du vill markera alla.|  
|TooltipTextHideRevealSecret|Visa/dölj|  
|ButtonLinkOpenConsole|Prova|  
|SectionHeadingRequestBody|Begärandetext|  
|SectionHeadingRequestParameters|Begäranparametrar|  
|SectionHeadingRequestUrl|URL för begäran|  
|SectionHeadingResponse|Svar|  
|SectionHeadingRequestHeaders|Begärandehuvud|  
|FormLabelSubtextOptional|valfri|  
|SectionHeadingCodeSamples|Kodexempel|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect id-token som erhållits från < jag\>{0}< /i\>. Beviljandetyper som stöds: < jag\>{1}< /i\>.|  
  
###  <a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Namn|Text|  
|----------|----------|  
|LinkLabelBack|föregående|  
|LinkLabelHomePage|startsidan|  
|LinkLabelSendUsEmail|Skicka ett e-postmeddelande till oss|  
|PageTitleError|Ett problem uppstod när den begärda sidan skulle hanteras|  
|TextblockPotentialCauseIntermittentIssue|Detta kan vara ett tillfälligt Dataåtkomstfel som redan är borta.|  
|TextblockPotentialCauseOldLink|Länken du har klickat på kan vara gammal och leder kanske inte längre till rätt plats.|  
|TextblockPotentialCauseTechnicalProblem|Det kan finnas ett tekniskt problem hos oss.|  
|TextblockPotentialSolutionRefresh|Försök att uppdatera sidan.|  
|TextblockPotentialSolutionStartOver|Börja om från vår {0}.|  
|TextblockPotentialSolutionTryAgain|Gå {0} och försök igen utföra åtgärden.|  
|TextReportProblem|{0} som beskriver vad som gått fel och vi ska titta på det så snart vi kan.|  
|TitlePotentialCause|Möjlig orsak|  
|TitlePotentialSolution|Det kan vara ett tillfälligt fel, prova det här|  
  
###  <a name="IssuesStrings"></a> IssuesStrings  
  
|Namn|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Problem|  
|WebIssuesNoActiveSubscriptions|Du har inga aktiva prenumerationer. Du måste prenumerera på en produkt för att rapportera ett problem.|  
|WebIssuesNotSignin|Du har inte loggat in. Du {0} att rapportera ett problem eller skicka en kommentar.|  
|WebIssuesReportIssueButton|Rapportera fel|  
|WebIssuesSignIn|logga in|  
|WebIssuesStatusReportedBy|Status: {0} &#124; rapporteras av {1}|  
  
###  <a name="NotFoundStrings"></a> NotFoundStrings  
  
|Namn|Text|  
|----------|----------|  
|LinkLabelHomePage|startsidan|  
|LinkLabelSendUsEmail|mejla oss|  
|PageTitleNotFound|Vi kan inte hitta sidan du letar efter|  
|TextblockPotentialCauseMisspelledUrl|Du kan vara felstavat URL: en om du angav.|  
|TextblockPotentialCauseOldLink|Länken du har klickat på kan vara gammal och leder kanske inte längre till rätt plats.|  
|TextblockPotentialSolutionRetype|Försök att ange webbadressen igen.|  
|TextblockPotentialSolutionStartOver|Börja om från vår {0}.|  
|TextReportProblem|{0} som beskriver vad som gått fel och vi ska titta på det så snart vi kan.|  
|TitlePotentialCause|Möjlig orsak|  
|TitlePotentialSolution|Möjlig lösning|  
  
###  <a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Namn|Text|  
|----------|----------|  
|WebProductsAgreement|Genom att prenumerera på {0} produkt, jag godkänner den `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Användningsvillkor|  
|WebProductsSubscribeButton|Prenumerera|  
|WebProductsUsageLimitsHeader|Användningsgränser|  
|WebProductsYouAreNotSubscribed|Du prenumererar på den här produkten.|  
|WebProductsYouRequestedSubscription|Du har begärt en prenumeration på den här produkten.|  
|ErrorYouNeedtoAgreeWithLegalTerms|Du måste godkänna användningsvillkoren innan du kan fortsätta.|  
|ButtonLabelAddSubscription|Lägg till en prenumeration|  
|LinkLabelChangeSubscriptionName|ändra|  
|ButtonLabelConfirm|Bekräfta|  
|TextblockMultipleSubscriptionsCount|Du har {0} prenumerationer på den här produkten:|  
|TextblockSingleSubscriptionsCount|Du har {0} prenumerationen på den här produkten:|  
|TextblockSingleApisCount|Den här produkten innehåller {0} API:|  
|TextblockMultipleApisCount|Den här produkten innehåller {0} API: er:|  
|TextblockHeaderSubscribe|Prenumerera på produkten|  
|TextblockSubscriptionDescription|En ny prenumeration skapas enligt följande:|  
|TextblockSubscriptionLimitReached|Prenumerationsgränsen har nåtts.|  
  
###  <a name="ProductsStrings"></a> ProductsStrings  
  
|Namn|Text|  
|----------|----------|  
|PageTitleProducts|Produkter|  
  
###  <a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Namn|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Inloggningen är just nu inaktiverad av administratörerna.|  
|TextboxExternalIdentitiesSigninInvitation|Du kan också logga in med|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Logga in med:|  
  
###  <a name="SigninResources"></a> SigninResources  
  
|Namn|Text|  
|----------|----------|  
|PrincipalNotFound|Huvudnamnet hittades inte eller också är signaturen ogiltig|  
|ErrorSsoAuthenticationFailed|SSO-autentiseringen misslyckades|  
|ErrorSsoAuthenticationFailedDetailed|En ogiltig token har angetts eller också kan signaturen inte kontrolleras.|  
|ErrorSsoTokenInvalid|SSO-token är ogiltig|  
|ValidationErrorSpecificEmailAlreadyExists|E-post ”{0}har redan registrerats|  
|ValidationErrorSpecificEmailInvalid|E-post ”{0}' är ogiltig|  
|ValidationErrorPasswordInvalid|Lösenordet är ogiltigt. Åtgärda felen och försök igen.|  
|PropertyTooShort|{0} är för kort|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Ogiltig e-postadress.|  
|ValidationMessageNewPasswordConfirmationRequired|Bekräfta nytt lösenord|  
|ValidationErrorPasswordConfirmationRequired|Bekräfta lösenord är tomt|  
|WebAuthenticationEmailChangeNotice|Ändra e-postbekräftelsen som är på väg att {0}. Följ anvisningarna i den för att bekräfta din nya e-postadress. Om e-postmeddelandet inte har kommit till Inkorgen inom några minuter, ska du kontrollera skräppostmappen.|  
|WebAuthenticationEmailChangeNoticeHeader|Din begäran om ändring av e-postadress har bearbetats|  
|WebAuthenticationEmailChangeNoticeTitle|E-postadressändring har begärts|  
|WebAuthenticationEmailHasBeenRevertedNotice|Din e-postadress finns redan. Begäran har återställts|  
|ValidationErrorEmailAlreadyExists|E-postadressen finns redan|  
|ValidationErrorEmailInvalid|Ogiltig e-postadress|  
|TextboxLabelEmail|E-post|  
|ValidationErrorEmailRequired|E-postadress måste anges.|  
|WebAuthenticationErrorNoticeHeader|Fel|  
|WebAuthenticationFieldLengthErrorMessage|{0} måste vara en maximal längd på {1}|  
|TextboxLabelEmailFirstName|Förnamn|  
|ValidationErrorFirstNameRequired|Förnamn måste anges.|  
|ValidationErrorFirstNameInvalid|Ogiltigt förnamn|  
|NoticeInvalidInvitationToken|Observera att bekräftelselänkarna är giltiga för endast 48 timmar. Om du är den tidsramen, kontrollera att länken är korrekt. Om länken har gått ut, sedan upprepar du den åtgärd du försöker bekräfta.|  
|NoticeHeaderInvalidInvitationToken|Ogiltig inbjudningstoken|  
|NoticeTitleInvalidInvitationToken|Bekräftelsefel|  
|WebAuthenticationLastNameInvalidErrorMessage|Ogiltigt efternamn|  
|TextboxLabelEmailLastName|Efternamn|  
|ValidationErrorLastNameRequired|Efternamn måste anges.|  
|WebAuthenticationLinkExpiredNotice|Bekräftelselänken som skickades till dig har upphört att gälla. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Länken för lösenordsåterställning är ogiltig eller har upphört att gälla.|  
|WebAuthenticationLinkExpiredNoticeTitle|Länken har skickats|  
|WebAuthenticationNewPasswordLabel|Nytt lösenord|  
|ValidationMessageNewPasswordRequired|Ett nytt lösenord krävs.|  
|TextboxLabelNotificationsSenderEmail|Avsändare för e-postaviseringar|  
|TextboxLabelOrganizationName|Organisationens namn|  
|WebAuthenticationOrganizationRequiredErrorMessage|Organisationsnamnet är tomt|  
|WebAuthenticationPasswordChangedNotice|Ditt lösenord har uppdaterats|  
|WebAuthenticationPasswordChangedNoticeTitle|Lösenordet har uppdaterats|  
|WebAuthenticationPasswordCompareErrorMessage|Lösenorden är inte identiska|  
|WebAuthenticationPasswordConfirmLabel|Bekräfta lösenord|  
|ValidationErrorPasswordInvalidDetailed|Lösenordet är för svagt.|  
|WebAuthenticationPasswordLabel|Lösenord|  
|ValidationErrorPasswordRequired|Lösenordet är obligatoriskt.|  
|WebAuthenticationPasswordResetSendNotice|Ändra lösenord bekräftelsemeddelandet är på väg att {0}. Följ anvisningarna i e-postmeddelandet för att fortsätta din lösenordsändringsprocessen.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Din begäran om ändring av lösenord har bearbetats|  
|WebAuthenticationPasswordResetSendNoticeTitle|Lösenordsåterställning har begärts|  
|WebAuthenticationRequestNotFoundNotice|Förfrågan hittades inte|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Aviseringsavsändarens e-postadress är tom|  
|WebAuthenticationSigninPasswordLabel|Bekräfta ändringen genom att ange ett lösenord|  
|WebAuthenticationSignupConfirmNotice|Registreringsbekräftelsen är på väg att {0}. < br /\> följer du anvisningarna i e-postmeddelandet för att aktivera kontot. < br /\> om e-postmeddelandet inte har kommit till Inkorgen inom några minuter, Kontrollera skräppostmappen.|  
|WebAuthenticationSignupConfirmNoticeHeader|Ditt konto har skapats|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Registreringsbekräftelsen har skickats igen|  
|WebAuthenticationSignupConfirmNoticeTitle|Konto har skapats|  
|WebAuthenticationTokenRequiredErrorMessage|Token är tom|  
|WebAuthenticationUserAlreadyRegisteredNotice|Det verkar vara en användare med den här e-postadressen är redan registrerad i systemet. Om du glömmer ditt lösenord försök att återställa den eller kontakta Vårt supportteam.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Användaren är redan registrerad|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Redan registrerat|  
|ButtonLabelChangePassword|Ändra lösenord|  
|ButtonLabelChangeAccountInfo|Ändra kontoinformation|  
|ButtonLabelCloseAccount|Stäng konto|  
|WebAuthenticationInvalidCaptchaErrorMessage|Texten som angetts matchar inte texten i bilden. Försök igen.|  
|ValidationErrorCredentialsInvalid|E-post eller lösenordet är ogiltigt. Åtgärda felen och försök igen.|  
|WebAuthenticationRequestIsNotValid|Begäran är ogiltig|  
|WebAuthenticationUserIsNotConfirm|Bekräfta registreringen innan du försöker logga in.|  
|WebAuthenticationInvalidEmailFormated|E-post är ogiltig: {0}|  
|WebAuthenticationUserNotFound|Användaren hittades inte|  
|WebAuthenticationTenantNotRegistered|Kontot tillhör en Azure Active Directory-klientorganisation som inte har behörighet till den här portalen.|  
|WebAuthenticationAuthenticationFailed|Autentiseringen misslyckades.|  
|WebAuthenticationGooglePlusNotEnabled|Autentiseringen misslyckades. Om du har autentiserat programmet är sedan du kontaktar du administratören för att se till att Google-autentiseringen korrekt konfigurerad.|  
|ValidationErrorAllowedTenantIsRequired|Tillåten klientorganisation måste anges|  
|ValidationErrorTenantIsNotValid|Azure Active Directory-klient '{0}' är inte giltig.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Logga in med ditt {0} konto|  
|WebAuthenticationUserLimitNotice|Den här tjänsten har nått det maximala antalet tillåtna användare. Du `<a href="mailto:{0}"\>contact the administrator</a\>` att uppgradera tjänsten och återaktivera användarregistreringen.|  
|WebAuthenticationUserLimitNoticeHeader|Användarregistrering har inaktiverats|  
|WebAuthenticationUserLimitNoticeTitle|Användarregistrering har inaktiverats|  
|WebAuthenticationUserRegistrationDisabledNotice|Registrering av användare har inaktiverats av administratören. Logga in med extern identitetsprovider.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Användarregistrering har inaktiverats|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Användarregistrering har inaktiverats|  
|WebAuthenticationSignupPendingConfirmationNotice|Innan vi kan skapa ditt konto som vi behöver verifiera din e-postadress. Vi har skickat ett e-postmeddelande till {0}. Följ anvisningarna i e-postmeddelandet för att aktivera ditt konto. Om e-postmeddelandet inte har kommit inom några minuter, ska du kontrollera skräppostmappen.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Vi hittade ett obekräftat konto för e-postadressen {0}. Vi behöver verifiera din e-postadress för att slutföra skapandet av ditt konto. Vi har skickat ett e-postmeddelande till {0}. Följ anvisningarna i e-postmeddelandet för att aktivera ditt konto. Om e-postmeddelandet inte har kommit inom några minuter, så kontrollera skräppostmappen|  
|WebAuthenticationSignupConfirmationAlmostDone|Nästan klart|  
|WebAuthenticationSignupConfirmationEmailSent|Vi har skickat ett e-postmeddelande till {0}. Följ anvisningarna i e-postmeddelandet för att aktivera ditt konto. Om e-postmeddelandet inte har kommit inom några minuter, ska du kontrollera skräppostmappen.|  
|WebAuthenticationEmailSentNotificationMessage|E-postmeddelande skickats har till {0}|  
|WebAuthenticationNoAadTenantConfigured|Ingen Azure Active Directory-klientorganisation har konfigurerats för tjänsten.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Jag godkänner den `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Granska `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Användningsvillkor|  
|ValidationMessageConsentNotAccepted|Du måste godkänna användningsvillkoren innan du kan fortsätta.|  
  
###  <a name="SigninStrings"></a> SigninStrings  
  
|Namn|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Har du glömt lösenordet?|  
|WebAuthenticationIfAdministrator|Om du är administratör måste du logga in `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Inte medlem ännu? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Kom ihåg mig på den här datorn|  
|WebAuthenticationSigininWithPassword|Logga in med ditt användarnamn och lösenord|  
|WebAuthenticationSigninTitle|Logga in|  
|WebAuthenticationSignUpNow|Registrera dig nu|  
  
###  <a name="SignupStrings"></a> SignupStrings  
  
|Namn|Text|  
|----------|----------|  
|PageTitleSignup|Registrera dig|  
|WebAuthenticationAlreadyAMember|Är du redan medlem?|  
|WebAuthenticationCreateNewAccount|Skapa ett nytt API Management-konto|  
|WebAuthenticationSigninNow|Logga in nu|  
|ButtonLabelSignup|Registrera dig|  
  
###  <a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Namn|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Vill du avbryta den här prenumerationen?|  
|SubscriptionRenewConfirmation|Vill du förnya den här prenumerationen?|  
|WebDevelopersManageSubscriptions|Hantera prenumerationer|  
|WebDevelopersPrimaryKey|Primär nyckel|  
|WebDevelopersRegenerateLink|Återskapa|  
|WebDevelopersSecondaryKey|Sekundär nyckel|  
|ButtonLabelShowKey|Visa|  
|ButtonLabelRenewSubscription|Förnya|  
|WebDevelopersSubscriptionReqested|Begärt {0}|  
|WebDevelopersSubscriptionRequestedState|Begärd|  
|WebDevelopersSubscriptionTableNameHeader|Namn|  
|WebDevelopersSubscriptionTableStateHeader|Status|  
|WebDevelopersUsageStatisticsLink|Analytics-rapporter|  
|WebDevelopersYourSubscriptions|Dina prenumerationer|  
|SubscriptionPropertyLabelRequestedDate|Begärt|  
|SubscriptionPropertyLabelStartedDate|Startade|  
|PageTitleRenameSubscription|Byt namn på prenumeration|  
|SubscriptionPropertyLabelName|Prenumerationsnamn|  
  
###  <a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Namn|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Vill du stänga kontot?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Dölj|  
|ButtonLabelRegenerateKey|Återskapa|  
|InformationMessageKeyWasRegenerated|Vill du återskapa den här nyckeln?|  
|ButtonLabelShowKey|Visa|  
  
###  <a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Namn|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Uppdatera profilen|  
|PageTitleUpdateProfile|Uppdatera kontoinformation|  
  
###  <a name="UserProfile"></a> Användarprofil  
  
|Namn|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Ändra kontoinformation|  
|ButtonLabelChangePassword|Ändra lösenord|  
|ButtonLabelCloseAccount|Stäng konto|  
|TextboxLabelEmail|E-post|  
|TextboxLabelEmailFirstName|Förnamn|  
|TextboxLabelEmailLastName|Efternamn|  
|TextboxLabelNotificationsSenderEmail|Avsändare för e-postaviseringar|  
|TextboxLabelOrganizationName|Organisationens namn|  
|SubscriptionStateActive|Active|  
|SubscriptionStateCancelled|Annullerad|  
|SubscriptionStateExpired|Har upphört att gälla|  
|SubscriptionStateRejected|Avvisad|  
|SubscriptionStateRequested|Begärd|  
|SubscriptionStateSuspended|Tillfälligt avbruten|  
|DefaultSubscriptionNameTemplate|{0}  (standard)|  
|SubscriptionNameTemplate|Utvecklare få åtkomst till #{0}|  
|TextboxLabelSubscriptionName|Prenumerationsnamn|  
|ValidationMessageSubscriptionNameRequired|Namn på prenumeration måste anges.|  
|ApiManagementUserLimitReached|Den här tjänsten har nått det maximala antalet tillåtna användare. Uppgradera till en högre prisnivå.|  
  
##  <a name="glyphs"></a> Tecken resurser  
 API Management utvecklarportalsmallar kan använda tecknen från [Glyphicons från Bootstrap](https://getbootstrap.com/components/#glyphicons). Den här uppsättningen tecknen innehåller över 250 tecken i teckensnittsformat från den [Glyphicon](https://glyphicons.com/) Halflings ange. Använd följande syntax om du vill använda ett tecken från den här uppsättningen.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Läs den fullständiga listan med tecken [Glyphicons från Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](api-management-developer-portal-templates.md).
