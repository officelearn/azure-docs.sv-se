---
title: Resurser med Azure API Management-mall | Microsoft Docs
description: "Lär dig mer om vilka typer av resurser som är tillgängliga för användning i developer portal mallar i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 212e7ea7bb2ffea63c7ba210195df0da38aa8f0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-template-resources"></a>Resurser med Azure API Management-mall
Azure API Management ger följande typer av resurser för användning i developer portal mallar.  
  
-   [Strängresurser](#strings)  
  
-   [Glyf resurser](#glyphs)  
  
##  <a name="strings"></a>Strängresurser  
 API Management ger en omfattande uppsättning strängresurserna för användning i developer-portalen. Dessa resurser är lokaliserade till alla språk som stöds av API-hantering. En standarduppsättning av mallar använder dessa resurser för sidhuvuden, etiketter och alla fasta strängar som visas i developer-portalen. Om du vill använda en strängresurs i mallarna, anger du resursen sträng prefixet följt av namnet strängen som visas i följande exempel.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 I följande exempel är från mallen produkten listan och visar **produkter** överst på sidan.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
 Referera till följande tabeller för strängresurserna tillgängligt för användning i mallarna developer portal. Använd tabellnamnet som prefix för strängresurserna i tabellen.  
  
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
  
###  <a name="ApisStrings"></a>ApisStrings  
  
|Namn|Text|  
|----------|----------|  
|PageTitleApis|API:er|  
  
###  <a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Namn|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Programmet preview|  
|WebApplicationsRequirementsHeader|Krav|  
|WebApplicationsScreenshotAlt|Skärmbild|  
|WebApplicationsScreenshotsHeader|Skärmdumpar|  
  
###  <a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Namn|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Är du säker på att du vill ta bort programmet?|  
|WebDevelopersAppNotPublished|Inte publiceras|  
|WebDevelopersAppNotSubminted|Inte skicka|  
|WebDevelopersAppTableCategoryHeader|Kategori|  
|WebDevelopersAppTableNameHeader|Namn|  
|WebDevelopersAppTableStateHeader|Status|  
|WebDevelopersEditLink|Redigera|  
|WebDevelopersRegisterAppLink|Registrera program|  
|WebDevelopersRemoveLink|Ta bort|  
|WebDevelopersSubmitLink|Skicka|  
|WebDevelopersYourApplicationsHeader|Dina program|  
  
###  <a name="AppStrings"></a>AppStrings  
  
|Namn|Text|  
|----------|----------|  
|WebApplicationsHeader|Program|  
  
###  <a name="CommonResources"></a>CommonResources  
  
|Namn|Text|  
|----------|----------|  
|NoItemsToDisplay|Hittade inga resultat.|  
|GeneralExceptionMessage|Något är inte rätt. Det kan vara ett tillfälligt fel eller ett programfel. Försök igen.|  
|GeneralJsonExceptionMessage|Något är inte rätt. Det kan vara ett tillfälligt fel eller ett programfel. Gör att läsa in sidan och försök igen.|  
|ConfirmationMessageUnsavedChanges|Det finns vissa ändringar. Är du säker på att du vill avbryta och ta bort ändringarna?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|HTTP-brödtext i begäran för stor.|  
  
###  <a name="CommonStrings"></a>CommonStrings  
  
|Namn|Text|  
|----------|----------|  
|ButtonLabelCancel|Avbryt|  
|ButtonLabelSave|Spara|  
|GeneralExceptionMessage|Något är inte rätt. Det kan vara ett tillfälligt fel eller ett programfel. Försök igen.|  
|NoItemsToDisplay|Det finns inga objekt att visa.|  
|PagerButtonLabelFirst|Första|  
|PagerButtonLabelLast|senaste|  
|PagerButtonLabelNext|Nästa|  
|PagerButtonLabelPrevious|Föregående|  
|PagerLabelPageNOfM|Sidan {0} för {1}|  
|PasswordTooShort|Lösenordet är för kort|  
|EmailAsPassword|Använd inte din e-postadress som ditt lösenord|  
|PasswordSameAsUserName|Lösenordet får inte innehålla ditt användarnamn|  
|PasswordTwoCharacterClasses|Använd olika teckenuppsättningar klasser|  
|PasswordTooManyRepetitions|För många upprepningar|  
|PasswordSequenceFound|Lösenordet innehåller sekvenser|  
|PagerLabelPageSize|Sidstorleken|  
|CurtainLabelLoading|Läser in...|  
|TablePlaceholderNothingToDisplay|Det finns inga data för den valda perioden och omfång|  
|ButtonLabelClose|Stäng|  
  
###  <a name="Documentation"></a>Dokumentation  
  
|Namn|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Ogiltigt huvud: {0}|  
|WebDocumentationInvalidRequestErrorMessage|Ogiltig begärande-URL|  
|TextboxLabelAccessToken|Åtkomsttoken *|  
|DropdownOptionPrimaryKeyFormat|Primär-{0}|  
|DropdownOptionSecondaryKeyFormat|Sekundär-{0}|  
|WebDocumentationSubscriptionKeyText|Din prenumeration nyckel|  
|WebDocumentationTemplatesAddHeaders|Lägg till nödvändiga HTTP-huvuden|  
|WebDocumentationTemplatesBasicAuthSample|Grundläggande auktorisering exempel|  
|WebDocumentationTemplatesCurlForBasicAuth|för grundläggande auktorisering:--användaren {username}: {lösenord}|  
|WebDocumentationTemplatesCurlValuesForPath|Ange värden för parametrarna (visas som {...}), din prenumeration nyckel och värden för frågeparametrar|  
|WebDocumentationTemplatesDeveloperKey|Ange nyckel för din prenumeration|  
|WebDocumentationTemplatesJavaApache|Det här exemplet använder Apache HTTP-klienten från http-komponenter (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Ange värden för valfria parametrar, vid behov|  
|WebDocumentationTemplatesPhpPackage|Det här exemplet använder HTTP_Request2-paket. (Mer information: http://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Ange värden för parametrarna (visas som {...}) och begäran om det behövs|  
|WebDocumentationTemplatesRequestBody|Ange text för begäran|  
|WebDocumentationTemplatesRequiredParams|Ange värden för följande obligatoriska parametrar|  
|WebDocumentationTemplatesValuesForPath|Ange värden för parametrarna (visas som {...})|  
|OAuth2AuthorizationEndpointDescription|Auktorisering slutpunkten används för att interagera med resursägare och få ett godkännande bevilja.|  
|OAuth2AuthorizationEndpointName|autentiseringsslutpunkt|  
|OAuth2TokenEndpointDescription|Tokenslutpunkten används av klienten för att få en åtkomsttoken genom att presentera auktorisering beviljas eller uppdatera token.|  
|OAuth2TokenEndpointName|token för slutpunkt|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> klienten initierar flödet genom att dirigera resursägare användaragent till slutpunkten för auktorisering.  Klienten lägger till dess klient-ID, begärda omfånget, lokala tillstånd och en omdirigerings-URI som auktorisering servern skickar användaragenten tillbaka när åtkomst beviljas (eller nekas).     < /p\> < p\> auktorisering servern autentiserar resursägaren (via användaragent) och upprättar om resursägaren beviljar eller nekar åtkomst klientbegäran.     < /p\> < p\> förutsatt att resursägaren beviljar åtkomst, auktorisering servern omdirigerar användaragenten tillbaka till klienten med hjälp av omdirigerings-URI som angavs tidigare (i begäran eller när klienten registrati på).  Omdirigerings-URI som innehåller en Auktoriseringskoden och några lokala tillstånd som tillhandahölls av klienten tidigare.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> om användaren nekar åtkomstbegäran om begäran är ogiltig, klienten ett meddelande med följande parametrar till omdirigeringen: < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Begäran om godkännande|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> klientappen måste skicka användaren till slutpunkten för auktorisering för att påbörja OAuth-processen.          Vid slutpunkten för auktorisering, autentiserar användaren och sedan beviljar eller nekar åtkomst till appen.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> förutsatt att resursägaren beviljar åtkomst, auktorisering servern omdirigerar användaragenten tillbaka till klienten med hjälp av omdirigerings-URI som angavs tidigare (på begäran eller under registreringen).  Omdirigerings-URI som innehåller en Auktoriseringskoden och några lokala tillstånd som tillhandahölls av klienten tidigare. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> klienten begär en åtkomst-token från servern auktorisering '' s tokenslutpunkten genom att inkludera Auktoriseringskoden togs emot i föregående steg.  När du gör begäran autentiserar klienten med servern auktorisering.  Klienten innehåller omdirigeringen URI för att hämta Auktoriseringskoden för verifiering. < /p\> < p\> auktorisering servern autentiserar klienten validerar Auktoriseringskoden och garanterar att omdirigerings-URI som togs emot matchar den URI som används för att dirigera om klienten i steg C.  Om det är giltigt svarar auktorisering servern igen med en åtkomst-token och eventuellt en uppdateringstoken. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> om klientautentisering begäran inte kunde eller är ogiltig, auktorisering servern svarar med en statuskod HTTP 400 (felaktig begäran) (om inget annat anges) och innehåller följande parametrar med svaret. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> klienten skickar en begäran till tokenslutpunkten genom att skicka följande parametrar i formatet ”application/x-www-formuläret-urlencoded” med en teckenkodning UTF-8 i en HTTP-begäran huvuddelen. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> auktorisering servern utfärdar en åtkomst-token och valfritt uppdateringstoken och konstruktioner svaret genom att lägga till följande parametrar huvuddelen av HTTP-svar med en kod 200 (OK) status. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> klienten autentiserar med auktorisering servern och begär en åtkomst-token från token slutpunkten. < /p\> < p\> auktorisering servern autentiserar klienten och om det är giltigt utfärdar en åtkomst-token. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> om begäran misslyckades klientautentisering eller är ogiltig auktorisering servern svarar med en statuskod HTTP 400 (felaktig begäran) (om inget annat anges) och innehåller följande parametrar med svaret. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klienten skickar en begäran till tokenslutpunkten genom att lägga till följande parametrar i formatet ”application/x-www-formuläret-urlencoded” med en teckenkodning UTF-8 i en HTTP-begäran huvuddelen. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> om token åtkomstbegäran är giltiga och godkända, auktorisering servern utfärdar en åtkomst-token och valfritt uppdateringstoken och konstruktioner svaret genom att lägga till följande parametrar huvuddelen av HTTP-svar med en 200  Statuskoden (OK). < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> klienten initierar flödet genom att dirigera resursägaren '' s användaragent till slutpunkten för auktorisering.  Klienten lägger till dess klient-ID, begärda omfånget, lokala tillstånd och en omdirigerings-URI som auktorisering servern skickar användaragenten tillbaka när åtkomst beviljas (eller nekas). < /p\> < p\> auktorisering servern autentiserar resursägaren (via användaragent) och upprättar om resursägaren beviljar eller nekar klienten '' s åtkomstbegäran. < /p\> < p\> förutsatt att resursägaren beviljar åtkomst, auktorisering servern omdirigerar användaragenten tillbaka till klienten med hjälp av omdirigering URI som angetts tidigare.  Omdirigerings-URI innehåller den åtkomst-token i URI-fragment. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> om resursägaren nekar åtkomst-begäran eller om begäran misslyckas av andra orsaker än en saknad eller ogiltig omdirigerings-URI, auktorisering servern informerar klienten genom att lägga till följande parametrar o för fragment-komponent f omdirigerings-URI som i formatet ”application/x-www-formuläret-urlencoded”. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> klientappen måste skicka användaren till slutpunkten för auktorisering för att påbörja OAuth-processen.      Vid slutpunkten för auktorisering, autentiserar användaren och sedan beviljar eller nekar åtkomst till appen. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> om resursägaren ger åtkomstbegäran, auktorisering servern utfärdar en åtkomst-token och ger den till klienten genom att lägga till följande parametrar att omdirigerings-URI som använder den ”application/x-www fragment-komponent -formulär - urlencoded ”format. < /p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Auktoriseringskodflödet är optimerad för klienter som klarar av att bibehålla sekretessen för sina autentiseringsuppgifter (t.ex. program för webbservrar implementeras med hjälp av PHP, Java, Python, Ruby, ASP.NET, etc.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Auktorisering kod bevilja|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Klientautentiseringsuppgifter är lämpligt i fall där klienten (programmet) begär åtkomst till skyddade resurser under dess kontroll. Klienten anses som en resursägare, så inga användarinteraktion krävs.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Klientens autentiseringsuppgifter bevilja|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicita flödet är optimerad för klienter som inte klarar av att bibehålla sekretessen för sina autentiseringsuppgifter känt att använda en viss omdirigerings-URI. Dessa klienter implementeras normalt i en webbläsare som använder ett skriptspråk som JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit bevilja|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Resurs ägare lösenord autentiseringsuppgifter flödet är lämpligt i fall där resursägaren har en förtroenderelation med klienten (programmet), till exempel enhetens operativsystem eller ett mycket Privilegierade program. Detta flöde är lämplig för klienter som klarar för att hämta resursen ägarens autentiseringsuppgifter (användarnamn och lösenord, vanligtvis med ett interaktivt formulär).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Bevilja lösenordsinformation för resurs-ägare|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> resursägaren ger klienten med sitt användarnamn och lösenord. < /p\> < p\> klienten begär en åtkomst-token från servern auktorisering '' s tokenslutpunkten genom att inkludera de autentiseringsuppgifter som tagits emot från resursägare.  När du gör begäran autentiserar klienten med servern auktorisering. < /p\> < p\> auktorisering servern autentiserar klienten verifierar autentiseringsuppgifter för resurs-ägare och utfärdar en åtkomst-token om det är giltigt. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> om begäran misslyckades klientautentisering eller är ogiltig auktorisering servern svarar med en statuskod HTTP 400 (felaktig begäran) (om inget annat anges) och innehåller följande parametrar med svaret. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klienten skickar en begäran till tokenslutpunkten genom att lägga till följande parametrar i formatet ”application/x-www-formuläret-urlencoded” med en teckenkodning UTF-8 i en HTTP-begäran huvuddelen. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> om token åtkomstbegäran är giltiga och godkända, auktorisering servern utfärdar en åtkomst-token och valfritt uppdateringstoken och konstruktioner svaret genom att lägga till följande parametrar huvuddelen av HTTP-svar med ett 20 0 (OK) statuskoden. < /p\>|  
|OAuth2Step_AccessTokenRequest_Name|Begäran för åtkomst-token|  
|OAuth2Step_AuthorizationRequest_Name|Begäran om godkännande|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|KRÄVS. Den åtkomst-token som utfärdats av servern för auktorisering.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|KRÄVS. Den åtkomst-token som utfärdats av servern för auktorisering.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|KRÄVS. Den åtkomst-token som utfärdats av servern för auktorisering.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|KRÄVS. Den åtkomst-token som utfärdats av servern för auktorisering.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|KRÄVS. Klient-ID.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|KRÄVS om klienten inte autentiseras med auktorisering-servern.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|KRÄVS. Klient-ID.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|KRÄVS. Auktoriseringskoden som genererats av servern för auktorisering.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|KRÄVS. Auktoriseringskoden togs emot från servern för auktorisering.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|VALFRITT. Läsbart ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|VALFRITT. Läsbart ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|VALFRITT. Läsbart ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|VALFRITT. Läsbart ASCII-text som ger ytterligare information.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|VALFRITT. Läsbart ASCII-text som ger ytterligare information.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|VALFRITT. En URI som identifierar en läsbar sida med information om felet.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|VALFRITT. En URI som identifierar en läsbar sida med information om felet.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|VALFRITT. En URI som identifierar en läsbar sida med information om felet.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|VALFRITT. En URI som identifierar en läsbar sida med information om felet.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|VALFRITT. En URI som identifierar en läsbar sida med information om felet.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|KRÄVS. En enda ASCII-felkod från följande: invalid_request, unauthorized_client, om access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|KRÄVS. En enda ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|KRÄVS. En enda ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|KRÄVS. En enda ASCII-felkod från följande: invalid_request, unauthorized_client, om access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|KRÄVS. En enda ASCII-felkod från följande: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|REKOMMENDERAS. Livstid i sekunder för den åtkomst-token.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|REKOMMENDERAS. Livstid i sekunder för den åtkomst-token.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|REKOMMENDERAS. Livstid i sekunder för den åtkomst-token.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|REKOMMENDERAS. Livstid i sekunder för den åtkomst-token.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|KRÄVS. Värdet måste anges till ”authorization_code”.|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|KRÄVS. Värdet måste anges till ”client_credentials”.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|KRÄVS. Värdet måste anges till ”password”.|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|KRÄVS. Resurs-ägare lösenord.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|VALFRITT. Omdirigering av slutpunkten URI måste vara en absolut URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|KRÄVS om parametern ”redirect_uri” ingick i dess tillstånd och deras värden måste vara identiska.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|VALFRITT. Omdirigering av slutpunkten URI måste vara en absolut URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|VALFRITT. Den uppdateringstoken som kan användas för att hämta ny åtkomsttoken.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|VALFRITT. Den uppdateringstoken som kan användas för att hämta ny åtkomsttoken.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VALFRITT. Den uppdateringstoken som kan användas för att hämta ny åtkomsttoken.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|KRÄVS. Värdet måste anges till ”kod”.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|KRÄVS. Värdet måste anges till ”token”.|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|VALFRITT. Omfattningen för förfrågan.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|Om det är identisk med omfattningen som begärs av klienten. i annat fall krävs.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|VALFRITT. Omfattningen för förfrågan.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|VALFRITT, om det är identisk med omfattningen som begärs av klienten. i annat fall krävs.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|VALFRITT. Omfattningen för förfrågan.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|Om det är identisk med omfattningen som begärs av klienten. i annat fall krävs.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|VALFRITT. Omfattningen för förfrågan.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VALFRITT, om det är identisk med omfattningen som begärs av klienten. i annat fall krävs.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|KRÄVS om parametern ”tillstånd” fanns i klientbegäran för auktorisering.  Det exakta värdet som togs emot från klienten.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|REKOMMENDERAS. En täckande värde som används av klienten för att upprätthålla tillstånd mellan begäran och återanrop.  Auktorisering server inkluderar det här värdet när omdirigera användaragenten tillbaka till klienten.  Parametern bör användas för att förhindra förfalskning av begäran.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|KRÄVS om parametern ”tillstånd” fanns i klientbegäran för auktorisering.  Det exakta värdet som togs emot från klienten.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|KRÄVS om parametern ”tillstånd” fanns i klientbegäran för auktorisering.  Det exakta värdet som togs emot från klienten.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|REKOMMENDERAS. En täckande värde som används av klienten för att upprätthålla tillstånd mellan begäran och återanrop.  Auktorisering server inkluderar det här värdet när omdirigera användaragenten tillbaka till klienten.  Parametern bör användas för att förhindra förfalskning av begäran.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|KRÄVS om parametern ”tillstånd” fanns i klientbegäran för auktorisering.  Det exakta värdet som togs emot från klienten.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|KRÄVS. Typ av token som utfärdas.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|KRÄVS. Typ av token som utfärdas.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|KRÄVS. Typ av token som utfärdas.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|KRÄVS. Typ av token som utfärdas.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|KRÄVS. Resurs-ägare användarnamnet.|  
|OAuth2UnsupportedTokenType|Tokentyp: {0} är inte supporetd.|  
|OAuth2InvalidState|Ogiltigt svar från servern för auktorisering|  
|OAuth2GrantType_AuthorizationCode|Auktoriseringskoden|  
|OAuth2GrantType_Implicit|Implicit|  
|OAuth2GrantType_ClientCredentials|Klientens autentiseringsuppgifter|  
|OAuth2GrantType_ResourceOwnerPassword|Resurs-ägarlösenord|  
|WebDocumentation302Code|302 hittades|  
|WebDocumentation400Code|400 (felaktig begäran)|  
|OAuth2SendingMethod_AuthHeader|Authorization-huvud|  
|OAuth2SendingMethod_QueryParam|Frågeparametern|  
|OAuth2AuthorizationServerGeneralException|Ett fel uppstod när auktorisera åtkomst via {0}|  
|OAuth2AuthorizationServerCommunicationException|Det gick inte att upprätta en HTTP-anslutning till servern för auktorisering eller den har stängts oväntat.|  
|WebDocumentationOAuth2GeneralErrorMessage|Oväntat fel uppstod.|  
|AuthorizationServerCommunicationException|Auktorisering server kommunikation undantag har inträffat. Kontakta administratören.|  
|TextblockSubscriptionKeyHeaderDescription|Prenumerationen nyckel som ger åtkomst till detta API. I din < en href ='/ utvecklare '\>profil < /a\>.|  
|TextblockOAuthHeaderDescription|OAuth 2.0-åtkomsttoken har hämtats från < jag\>{0} < /i\>. Bevilja typer som stöds: < jag\>{1} < /i\>.|  
|TextblockContentTypeHeaderDescription|Medietyp för brödtext som skickas till API: et.|  
|ErrorMessageApiNotAccessible|Du försöker anropa API: et är inte tillgänglig just nu. Kontakta utgivaren API < en href = ”/ utfärdar”\>här < /a\>.|  
|ErrorMessageApiTimedout|Du försöker anropa API: et tar längre tid än normalt att få svar tillbaka. Kontakta utgivaren API < en href = ”/ utfärdar”\>här < /a\>.|  
|BadRequestParameterExpected|”{0}-parametern för startdiskvolymen”|  
|TooltipTextDoubleClickToSelectAll|Dubbelklicka om du vill markera alla.|  
|TooltipTextHideRevealSecret|Visa/Dölj|  
|ButtonLinkOpenConsole|Prova|  
|SectionHeadingRequestBody|Begärandetexten|  
|SectionHeadingRequestParameters|Parametrar för begäran|  
|SectionHeadingRequestUrl|URL-begäran|  
|SectionHeadingResponse|Svar|  
|SectionHeadingRequestHeaders|Huvuden för begäran|  
|FormLabelSubtextOptional|Valfria|  
|SectionHeadingCodeSamples|Kodexempel|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect-ID-token hämtades från < jag\>{0} < /i\>. Bevilja typer som stöds: < jag\>{1} < /i\>.|  
  
###  <a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Namn|Text|  
|----------|----------|  
|LinkLabelBack|Säkerhetskopiera|  
|LinkLabelHomePage|Startsida|  
|LinkLabelSendUsEmail|Skicka ett e-postmeddelande|  
|PageTitleError|Uppstod ett problem som betjänar den begärda sidan|  
|TextblockPotentialCauseIntermittentIssue|Detta kan vara ett tillfälligt data access-problem som redan är borta.|  
|TextblockPotentialCauseOldLink|Du har klickat på länken kan gamla och pekar inte på rätt plats längre.|  
|TextblockPotentialCauseTechnicalProblem|Det kan finnas ett tekniskt problem hos oss.|  
|TextblockPotentialSolutionRefresh|Försök att uppdatera sidan.|  
|TextblockPotentialSolutionStartOver|Börja om från vår {0}.|  
|TextblockPotentialSolutionTryAgain|Gå {0} och försök den instruktionen igen.|  
|TextReportProblem|{0} som beskriver vad som gick fel och vi titta på det som vi kan.|  
|TitlePotentialCause|Möjlig orsak|  
|TitlePotentialSolution|Det är eventuellt ett tillfälligt problem, några saker du kan prova att|  
  
###  <a name="IssuesStrings"></a>IssuesStrings  
  
|Namn|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Problem|  
|WebIssuesNoActiveSubscriptions|Du har inga aktiva prenumerationer. Du måste registrera för en produkt ska rapportera ett problem.|  
|WebIssuesNotSignin|Du är inte inloggad. Ta {0} om du vill rapportera ett problem eller efter en kommentar.|  
|WebIssuesReportIssueButton|Rapporten problemet|  
|WebIssuesSignIn|logga in|  
|WebIssuesStatusReportedBy|Status: {0} &#124; Rapporteras av {1}|  
  
###  <a name="NotFoundStrings"></a>NotFoundStrings  
  
|Namn|Text|  
|----------|----------|  
|LinkLabelHomePage|Startsida|  
|LinkLabelSendUsEmail|Skicka ett e-postmeddelande|  
|PageTitleNotFound|Det går inte att hitta sidan du söker efter|  
|TextblockPotentialCauseMisspelledUrl|Du kan vara felstavat URL: en om du har angett den i.|  
|TextblockPotentialCauseOldLink|Du har klickat på länken kan gamla och pekar inte på rätt plats längre.|  
|TextblockPotentialSolutionRetype|Försök att skriva in URL: en.|  
|TextblockPotentialSolutionStartOver|Börja om från vår {0}.|  
|TextReportProblem|{0} som beskriver vad som gick fel och vi titta på det som vi kan.|  
|TitlePotentialCause|Möjlig orsak|  
|TitlePotentialSolution|Möjlig lösning|  
  
###  <a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Namn|Text|  
|----------|----------|  
|WebProductsAgreement|Genom att prenumerera på {0} produkten jag godkänner den `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Användningsvillkor|  
|WebProductsSubscribeButton|Prenumerera|  
|WebProductsUsageLimitsHeader|Gränserna för Resursanvändning|  
|WebProductsYouAreNotSubscribed|Du prenumererar på den här produkten.|  
|WebProductsYouRequestedSubscription|Du har begärt prenumeration på den här produkten.|  
|ErrorYouNeedtoAgreeWithLegalTerms|Du måste godkänna användningsvillkoren innan du kan fortsätta.|  
|ButtonLabelAddSubscription|Lägg till en prenumeration|  
|LinkLabelChangeSubscriptionName|Ändra|  
|ButtonLabelConfirm|Bekräfta|  
|TextblockMultipleSubscriptionsCount|Du har {0} prenumerationer för den här produkten:|  
|TextblockSingleSubscriptionsCount|Du har {0} prenumeration på den här produkten:|  
|TextblockSingleApisCount|Den här produkten innehåller {0}-API:|  
|TextblockMultipleApisCount|Den här produkten innehåller {0} API: er:|  
|TextblockHeaderSubscribe|Prenumerera på produkten|  
|TextblockSubscriptionDescription|En ny prenumeration skapas på följande sätt:|  
|TextblockSubscriptionLimitReached|Prenumerationer har nåtts.|  
  
###  <a name="ProductsStrings"></a>ProductsStrings  
  
|Namn|Text|  
|----------|----------|  
|PageTitleProducts|Produkter|  
  
###  <a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Namn|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Logga in inaktiveras administratörer för tillfället.|  
|TextboxExternalIdentitiesSigninInvitation|Du kan också logga in med|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Logga in med:|  
  
###  <a name="SigninResources"></a>SigninResources  
  
|Namn|Text|  
|----------|----------|  
|PrincipalNotFound|Det gick inte att hitta Principal eller signaturen är ogiltig|  
|ErrorSsoAuthenticationFailed|SSO-autentiseringen misslyckades|  
|ErrorSsoAuthenticationFailedDetailed|Ogiltig token som tillhandahölls eller en signatur kan inte verifieras.|  
|ErrorSsoTokenInvalid|SSO-token är ogiltig|  
|ValidationErrorSpecificEmailAlreadyExists|E-post: {0} har redan registrerats|  
|ValidationErrorSpecificEmailInvalid|E-post: {0} är ogiltig|  
|ValidationErrorPasswordInvalid|Lösenordet är ogiltigt. Korrigera felen och försök igen.|  
|PropertyTooShort|{0} är för kort|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Ogiltig e-postadress.|  
|ValidationMessageNewPasswordConfirmationRequired|Bekräfta nytt lösenord|  
|ValidationErrorPasswordConfirmationRequired|Bekräfta lösenordet är tomt|  
|WebAuthenticationEmailChangeNotice|Ändra bekräftelse e-post är på väg till {0}. Följ instruktionerna i den för att bekräfta din nya e-postadress. Om e-postmeddelandet inte anländer till din inkorg inom några minuter, kontrollera mappen för skräppost.|  
|WebAuthenticationEmailChangeNoticeHeader|Din e-begäran bearbetades|  
|WebAuthenticationEmailChangeNoticeTitle|E-ändring har begärts|  
|WebAuthenticationEmailHasBeenRevertedNotice|Det finns redan en du e-post. Begäran har återställts|  
|ValidationErrorEmailAlreadyExists|Det finns redan en e-post|  
|ValidationErrorEmailInvalid|Ogiltig e-postadress|  
|TextboxLabelEmail|E-post|  
|ValidationErrorEmailRequired|E-post måste anges.|  
|WebAuthenticationErrorNoticeHeader|Fel|  
|WebAuthenticationFieldLengthErrorMessage|{0} måste vara en maximal längd på {1}|  
|TextboxLabelEmailFirstName|Förnamn|  
|ValidationErrorFirstNameRequired|Förnamn måste anges.|  
|ValidationErrorFirstNameInvalid|Ogiltigt förnamn|  
|NoticeInvalidInvitationToken|Observera att bekräftelse länkar är giltiga för endast 48 timmar. Om du fortfarande inom denna period, kontrollera att länken är korrekt. Om länken har upphört du upprepar du försöker att bekräfta åtgärden.|  
|NoticeHeaderInvalidInvitationToken|Ogiltig inbjudan token|  
|NoticeTitleInvalidInvitationToken|Bekräftelse-fel|  
|WebAuthenticationLastNameInvalidErrorMessage|Ogiltigt efternamn|  
|TextboxLabelEmailLastName|Efternamn|  
|ValidationErrorLastNameRequired|Efternamn måste anges.|  
|WebAuthenticationLinkExpiredNotice|Bekräftelse länk som har skickats till dig har gått ut. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Din länk för lösenordsåterställning är ogiltigt eller har upphört att gälla.|  
|WebAuthenticationLinkExpiredNoticeTitle|Länk som har skickats|  
|WebAuthenticationNewPasswordLabel|Nytt lösenord|  
|ValidationMessageNewPasswordRequired|Nytt lösenord krävs.|  
|TextboxLabelNotificationsSenderEmail|Meddelanden avsändarens e-post|  
|TextboxLabelOrganizationName|Organisationens namn|  
|WebAuthenticationOrganizationRequiredErrorMessage|Organisationens namn är tomt|  
|WebAuthenticationPasswordChangedNotice|Ditt lösenord har uppdaterats|  
|WebAuthenticationPasswordChangedNoticeTitle|Lösenordet uppdateras|  
|WebAuthenticationPasswordCompareErrorMessage|Lösenorden stämmer inte överens|  
|WebAuthenticationPasswordConfirmLabel|Bekräfta lösenord|  
|ValidationErrorPasswordInvalidDetailed|Lösenordet är för svag.|  
|WebAuthenticationPasswordLabel|Lösenord|  
|ValidationErrorPasswordRequired|Lösenord krävs.|  
|WebAuthenticationPasswordResetSendNotice|Ändra lösenordsbekräftelsen e-post är på väg till {0}. Följ anvisningarna i e-postmeddelandet för att fortsätta processen för ändring av lösenord.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Din begäran om lösenordsåterställning bearbetat har|  
|WebAuthenticationPasswordResetSendNoticeTitle|Lösenordsåterställning begärdes|  
|WebAuthenticationRequestNotFoundNotice|Begäran hittades inte|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Meddelanden avsändarens e-post är tom|  
|WebAuthenticationSigninPasswordLabel|Bekräfta ändringen genom att ange ett lösenord|  
|WebAuthenticationSignupConfirmNotice|Registrering av e-postbekräftelse är på väg till {0}. < br /\> följer du anvisningarna i e-postmeddelandet för att aktivera ditt konto. < br /\> om e-postmeddelandet inte anländer i Inkorgen i nästa några minuter, kontrollera din skräppost e-mappen.|  
|WebAuthenticationSignupConfirmNoticeHeader|Ditt konto har skapats|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Registrering bekräftelse e-postmeddelandet skickades igen|  
|WebAuthenticationSignupConfirmNoticeTitle|Konto som har skapats|  
|WebAuthenticationTokenRequiredErrorMessage|Token är tom|  
|WebAuthenticationUserAlreadyRegisteredNotice|Det verkar vara en användare med den här e-post är redan registrerad i systemet. Försök att återställa den eller Kontakta supportteamet om du har glömt ditt lösenord.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Användaren har redan registrerats|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Redan registrerats|  
|ButtonLabelChangePassword|Ändra lösenord|  
|ButtonLabelChangeAccountInfo|Ändra kontoinformation|  
|ButtonLabelCloseAccount|Avsluta konto|  
|WebAuthenticationInvalidCaptchaErrorMessage|Text på bilden matchar inte det angivna texten. Försök igen.|  
|ValidationErrorCredentialsInvalid|E-post eller lösenordet är ogiltigt. Korrigera felen och försök igen.|  
|WebAuthenticationRequestIsNotValid|Begäran är inte giltig|  
|WebAuthenticationUserIsNotConfirm|Bekräfta att registreringen innan du försöker logga in.|  
|WebAuthenticationInvalidEmailFormated|E-post är ogiltig: {0}|  
|WebAuthenticationUserNotFound|Användaren hittades inte|  
|WebAuthenticationTenantNotRegistered|Ditt konto hör till en Azure Active Directory-klient som inte har behörighet att komma åt den här portalen.|  
|WebAuthenticationAuthenticationFailed|Autentiseringen misslyckades.|  
|WebAuthenticationGooglePlusNotEnabled|Autentiseringen misslyckades. Om du har behörighet programmet är sedan du kontakta administratören att se till att Google-autentisering korrekt konfigurerad.|  
|ValidationErrorAllowedTenantIsRequired|Tillåtna klient krävs|  
|ValidationErrorTenantIsNotValid|Azure Active Directory-klient: {0} är inte giltig.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Logga in med ditt {0}|  
|WebAuthenticationUserLimitNotice|Den här tjänsten har nått det maximala antalet tillåtna användare. Kontrollera `<a href="mailto:{0}"\>contact the administrator</a\>` uppgradera tjänsten och återaktivera användarregistrering.|  
|WebAuthenticationUserLimitNoticeHeader|Användarregistrering inaktiverad|  
|WebAuthenticationUserLimitNoticeTitle|Användarregistrering inaktiverad|  
|WebAuthenticationUserRegistrationDisabledNotice|Registrering av användare har inaktiverats av administratören. Logga in med externa identitetsprovider.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Användarregistrering inaktiverad|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Användarregistrering inaktiverad|  
|WebAuthenticationSignupPendingConfirmationNotice|Innan vi kan slutföra skapandet av ditt konto behöver vi verifiera din e-postadress. Vi har skickat ett e-postmeddelande till {0}. Följ anvisningarna i e-postmeddelandet för att aktivera ditt konto. Om meddelandet inte kommer till inom nästa några minuter, kontrollera mappen för skräppost.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Ett obekräftat konto påträffades för e-postadress {0}. Vi behöver verifiera din e-postadress för att slutföra skapandet av ditt konto. Vi har skickat ett e-postmeddelande till {0}. Följ anvisningarna i e-postmeddelandet för att aktivera ditt konto. Om meddelandet inte kommer till inom nästa några minuter, kontrollera mappen för skräppost|  
|WebAuthenticationSignupConfirmationAlmostDone|Nästan klar|  
|WebAuthenticationSignupConfirmationEmailSent|Vi har skickat ett e-postmeddelande till {0}. Följ anvisningarna i e-postmeddelandet för att aktivera ditt konto. Om meddelandet inte kommer till inom nästa några minuter, kontrollera mappen för skräppost.|  
|WebAuthenticationEmailSentNotificationMessage|E-post som skickats till {0}|  
|WebAuthenticationNoAadTenantConfigured|Ingen Azure Active Directory-klient som konfigurerats för tjänsten.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Jag godkänner den `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Granska`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Användningsvillkor|  
|ValidationMessageConsentNotAccepted|Du måste godkänna användningsvillkoren innan du kan fortsätta.|  
  
###  <a name="SigninStrings"></a>SigninStrings  
  
|Namn|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Glömt ditt lösenord?|  
|WebAuthenticationIfAdministrator|Om du är administratör måste du logga in `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Inte en medlem ännu? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Kom ihåg mig på den här datorn|  
|WebAuthenticationSigininWithPassword|Logga in med ditt användarnamn och lösenord|  
|WebAuthenticationSigninTitle|Logga in|  
|WebAuthenticationSignUpNow|Registrera dig nu|  
  
###  <a name="SignupStrings"></a>SignupStrings  
  
|Namn|Text|  
|----------|----------|  
|PageTitleSignup|Registrera dig|  
|WebAuthenticationAlreadyAMember|Är du redan medlem?|  
|WebAuthenticationCreateNewAccount|Skapa ett nytt konto för API Management|  
|WebAuthenticationSigninNow|Logga in nu|  
|ButtonLabelSignup|Registrera dig|  
  
###  <a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Namn|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Är du säker på att du vill avbryta den här prenumerationen?|  
|SubscriptionRenewConfirmation|Är du säker på att du vill förnya den här prenumerationen?|  
|WebDevelopersManageSubscriptions|Hantera prenumerationer|  
|WebDevelopersPrimaryKey|Primär nyckel|  
|WebDevelopersRegenerateLink|Återskapa|  
|WebDevelopersSecondaryKey|Sekundärnyckeln|  
|ButtonLabelShowKey|Visa|  
|ButtonLabelRenewSubscription|Förnya|  
|WebDevelopersSubscriptionReqested|Begärs på {0}|  
|WebDevelopersSubscriptionRequestedState|Begärt|  
|WebDevelopersSubscriptionTableNameHeader|Namn|  
|WebDevelopersSubscriptionTableStateHeader|Status|  
|WebDevelopersUsageStatisticsLink|Analytics-rapporter|  
|WebDevelopersYourSubscriptions|Dina prenumerationer|  
|SubscriptionPropertyLabelRequestedDate|Begärs på|  
|SubscriptionPropertyLabelStartedDate|Startats på|  
|PageTitleRenameSubscription|Byt namn på prenumerationen|  
|SubscriptionPropertyLabelName|Prenumerationsnamn|  
  
###  <a name="SubscriptionStrings"></a>SubscriptionStrings  
  
|Namn|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Vill du avsluta ditt konto?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Dölj|  
|ButtonLabelRegenerateKey|Återskapa|  
|InformationMessageKeyWasRegenerated|Är du säker på att du vill återskapa den här nyckeln?|  
|ButtonLabelShowKey|Visa|  
  
###  <a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Namn|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Uppdatera profilen|  
|PageTitleUpdateProfile|Uppdatera kontoinformation|  
  
###  <a name="UserProfile"></a>Användarprofil  
  
|Namn|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Ändra kontoinformation|  
|ButtonLabelChangePassword|Ändra lösenord|  
|ButtonLabelCloseAccount|Avsluta konto|  
|TextboxLabelEmail|E-post|  
|TextboxLabelEmailFirstName|Förnamn|  
|TextboxLabelEmailLastName|Efternamn|  
|TextboxLabelNotificationsSenderEmail|Meddelanden avsändarens e-post|  
|TextboxLabelOrganizationName|Organisationens namn|  
|SubscriptionStateActive|Active|  
|SubscriptionStateCancelled|Avbröts|  
|SubscriptionStateExpired|Upphört att gälla|  
|SubscriptionStateRejected|Avvisade|  
|SubscriptionStateRequested|Begärt|  
|SubscriptionStateSuspended|avbruten|  
|DefaultSubscriptionNameTemplate|{0} (standard)|  
|SubscriptionNameTemplate|Utvecklare åtkomst #{0}|  
|TextboxLabelSubscriptionName|Prenumerationsnamn|  
|ValidationMessageSubscriptionNameRequired|Namn får inte vara tomt.|  
|ApiManagementUserLimitReached|Den här tjänsten har nått det maximala antalet tillåtna användare. Uppgradera till en högre prisnivå.|  
  
##  <a name="glyphs"></a>Glyf resurser  
 API Management developer portal mallar kan använda specialtecken från [Glyphicons från Bootstrap](http://getbootstrap.com/components/#glyphicons). Den här specialtecken innehåller över 250 tecken i teckensnittsformat från den [Glyphicon](http://glyphicons.com/) Halflings ange. Använd följande syntax om du vill använda ett tecken från den här uppsättningen.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 En fullständig lista över specialtecken finns [Glyphicons från Bootstrap](http://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns [hur du anpassar API Management developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).
