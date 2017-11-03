---
title: Azure API Management-mall datamodell referens | Microsoft Docs
description: "Läs mer om entiteten och Skriv garantier för vanliga objekt som används i datamodeller för developer portal mallar i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 72936a4d38f809934ddea74e5ae4a6029450a97c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-template-data-model-reference"></a>Azure API Management-mall datamodell referens
Det här avsnittet beskriver entitet och typ garantier för vanliga objekt som används i datamodeller för developer portal mallar i Azure API Management.  
  
 Mer information om hur du arbetar med mallar finns [hur du anpassar API Management developer-portalen med hjälp av mallar](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
-   [API](#API)  
-   [API-översikten](#APISummary)  
-   [Programmet](#Application)  
-   [Bifogad fil](#Attachment)  
-   [Kodexempel](#Sample)  
-   [Kommentar](#Comment)  
-   [Filtrering](#Filtering)  
-   [Huvudet](#Header)  
-   [HTTP-begäran](#HTTPRequest)  
-   [HTTP-svar](#HTTPResponse)  
-   [Problemet](#Issue)  
-   [Åtgärd](#Operation)  
-   [Åtgärd-menyn](#Menu)  
-   [Åtgärden menyobjekt](#MenuItem)  
-   [Växling](#Paging)  
-   [Parametern](#Parameter)  
-   [Produkten](#Product)  
-   [Leverantör](#Provider)  
-   [Representation](#Representation)  
-   [Prenumeration](#Subscription)  
-   [Prenumerationen sammanfattning](#SubscriptionSummary)  
-   [Information om användarkonto](#UserAccountInfo)  
-   [Användaren logga in](#UseSignIn)  
-   [Användaren loggar in](#UserSignUp)  
  
##  <a name="API"></a>API  
 Den `API` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|id|Sträng|Resursidentifieraren. Identifierar API inom den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `apis/{id}` där `{id}` är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|namn|Sträng|Namnet på API: et. Får inte vara tomt. Maximal längd är 100 tecken.|  
|description|Sträng|Beskrivning av API: et. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|serviceUrl|Sträng|Absolut URL för backend-tjänsten implementerar detta API.|  
|Sökväg|Sträng|Relativ URL som unikt identifierar denna API och alla dess resurs sökvägar i API Management service-instans. Den läggs till API-slutpunkten bas-URL anges när tjänsten instans skapades för att bilda en offentlig URL för detta API.|  
|Protokoll|matris med tal|Beskriver på vilket protokoll åtgärder i detta API kan anropas. Tillåtna värden är `1 - http` och `2 - https`, eller båda.|  
|authenticationSettings|[Auktoriseringsinställningar server-autentisering](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Samling autentiseringsinställningar som ingår i detta API.|  
|subscriptionKeyParameterNames|Objektet|Egenskapen som kan användas för att ange anpassade namn för frågan och/eller huvudet parametrar som innehåller nyckeln för prenumerationen. När den här egenskapen är finns måste det innehålla minst en av följande två egenskaper.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>API-översikten  
 Den `API summary` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|id|Sträng|Resursidentifieraren. Identifierar API inom den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `apis/{id}` där `{id}` är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|namn|Sträng|Namnet på API: et. Får inte vara tomt. Maximal längd är 100 tecken.|  
|description|Sträng|Beskrivning av API: et. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
  
##  <a name="Application"></a>Programmet  
 Den `application` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|Sträng|Unik identifierare för programmet.|  
|Rubrik|Sträng|Titeln på programmet.|  
|Beskrivning|Sträng|Beskrivning av programmet.|  
|URL|URI: N|URI för programmet.|  
|Version|Sträng|Versionsinformation för programmet.|  
|Krav|Sträng|En beskrivning av kraven för programmet.|  
|Status|Antal|Det aktuella tillståndet för programmet.<br /><br /> -0 - registrerad<br /><br /> -1 - har skickats<br /><br /> -2 - publicerade<br /><br /> -3 - avvisade<br /><br /> -4 - Opublicerat|  
|RegistrationDate|Datum och tid|Datum och tid som programmet har registrerats.|  
|CategoryId|Antal|Kategori för programmet (ekonomi, underhållning osv.)|  
|DeveloperId|Sträng|Unik identifierare för utvecklare som skickats av programmet.|  
|Bifogade filer|Samling av [bilaga](#Attachment) entiteter.|Eventuella bifogade filer för programmet, till exempel skärmdumpar eller ikoner.|  
|Ikon|[Bifogad fil](#Attachment)|Ikonen för den för programmet.|  
  
##  <a name="Attachment"></a>Bifogad fil  
 Den `attachment` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Unikt ID|Sträng|Den unika identifieraren för den bifogade filen.|  
|URL|Sträng|URL till resursen.|  
|Typ|Sträng|Typ av bifogad fil.|  
|ContentType|Sträng|Medietyp för den bifogade filen.|  
  
##  <a name="Sample"></a>Kodexempel  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Rubrik|Sträng|Namnet på åtgärden.|  
|fragment|Sträng|Den här egenskapen är inaktuell och bör inte användas.|  
|pensel|Sträng|Vilka kodsyntax färgläggning mall som ska användas när du visar kodexemplet. Tillåtna värden är `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, och `csharp`.|  
|mall|Sträng|Namnet på den här koden exempelmall.|  
|Brödtext|Sträng|En platshållare för koden exempel del av kodavsnittet.|  
|Metoden|Sträng|HTTP-metoden för åtgärden.|  
|schemat|Sträng|Protokollet som ska användas för begäran igen.|  
|Sökväg|Sträng|Sökvägen till åtgärden.|  
|DocumentDB|Sträng|Exempel på frågan sträng med definierade parametrar.|  
|värden|Sträng|URL för API Management-tjänsten gateway-API: t som innehåller den här åtgärden.|  
|Rubriker|Samling av [huvud](#Header) entiteter.|Huvuden för den här åtgärden.|  
|parameters|Samling av [parametern](#Parameter) entiteter.|Parametrar som definierats för den här åtgärden.|  
  
##  <a name="Comment"></a>Kommentar  
 Den `API` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|Antal|Id för kommentaren.|  
|CommentText|Sträng|Innehållet i kommentaren. Kan innehålla HTML.|  
|DeveloperCompany|Sträng|Företagsnamn som utvecklare.|  
|PostedOn|Datum och tid|Datum och tid som kommentaren anslogs.|  
  
##  <a name="Issue"></a>Problemet  
 Den `issue` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|Sträng|Den unika identifieraren för problemet.|  
|ApiID|Sträng|Id för API: et för vilken det här problemet har rapporterats.|  
|Rubrik|Sträng|Titel på problemet.|  
|Beskrivning|Sträng|Beskrivning av problemet.|  
|SubscriptionDeveloperName|Sträng|Förnamn som utvecklare som rapporterats av problemet.|  
|IssueState|Sträng|Det aktuella tillståndet för problemet. Möjliga värden är Proposed, öppnade, stängd.|  
|ReportedOn|Datum och tid|Datum och tid problemet har rapporterats.|  
|Kommentarer|Samling av [kommentar](#Comment) entiteter.|Kommentarer om det här problemet.|  
|Bifogade filer|Samling av [bilaga](api-management-template-data-model-reference.md#Attachment) entiteter.|Bifogade till problemet.|  
|Tjänster|Samling av [API](#API) entiteter.|API: erna prenumererar på användaren som skapade problemet.|  
  
##  <a name="Filtering"></a>Filtrering  
 Den `filtering` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Mönstret|Sträng|Aktuella söktermen; eller `null` om det finns ingen sökterm.|  
|Platshållare|Sträng|Texten som visas i sökrutan när det finns ingen sökterm som angetts.|  
  
##  <a name="Header"></a>Huvudet  
 Det här avsnittet beskrivs de `parameter` representation.  
  
|Egenskap|Beskrivning|Typ|  
|--------------|-----------------|----------|  
|namn|Sträng|Parameternamnet.|  
|description|Sträng|Parameterbeskrivning.|  
|värde|Sträng|Huvudets värde.|  
|TypeName|Sträng|Datatypen för huvudvärde.|  
|alternativ|Sträng|Alternativ.|  
|Krävs|Booleskt värde|Anger om sidhuvudet krävs.|  
|Skrivskyddad|Booleskt värde|Anger om sidhuvudet är skrivskyddad.|  
  
##  <a name="HTTPRequest"></a>HTTP-begäran  
 Det här avsnittet beskrivs de `request` representation.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|description|Sträng|Beskrivning av åtgärden begäran.|  
|Rubriker|matris med [huvud](#Header) entiteter.|Huvuden för begäran.|  
|parameters|matris med [Parameter](#Parameter)|De parametrar som begäran igen.|  
|garantier|matris med [Representation](#Representation)|Åtgärden begäran garantier samling.|  
  
##  <a name="HTTPResponse"></a>HTTP-svar  
 Det här avsnittet beskrivs de `response` representation.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|statusCode|Positivt heltal|Åtgärden Svarets statuskod.|  
|description|Sträng|Beskrivning av åtgärden svar.|  
|garantier|matris med [Representation](#Representation)|Åtgärden svar garantier samling.|  
  
##  <a name="Operation"></a>Åtgärden  
 Den `operation` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|id|Sträng|Resursidentifieraren. Identifierar åtgärden inom den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `apis/{aid}/operations/{id}` där `{aid}` är en API-identifierare och `{id}` är en identifierare för åtgärden. Den här egenskapen är skrivskyddad.|  
|namn|Sträng|Namnet på åtgärden. Får inte vara tomt. Maximal längd är 100 tecken.|  
|description|Sträng|Beskrivning av åtgärden. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|schemat|Sträng|Beskriver på vilket protokoll åtgärder i detta API kan anropas. Tillåtna värden är `http`, `https`, eller båda `http` och `https`.|  
|uriTemplate|Sträng|Relativa URL: en mall identifierar målresursen för den här åtgärden. Kan innehålla parametrar. Exempel:`customers/{cid}/orders/{oid}/?date={date}`|  
|värden|Sträng|URL för API Management-gateway som är värd för API: et.|  
|HttpMethod|Sträng|HTTP-åtgärdsmetod.|  
|Begäran|[HTTP-begäran](#HTTPRequest)|En entitet som innehåller detaljer om förfrågan.|  
|svar|matris med [HTTP-svar](#HTTPResponse)|Matris med åtgärden [HTTP-svar](#HTTPResponse) entiteter.|  
  
##  <a name="Menu"></a>Åtgärd-menyn  
 Den `operation menu` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|ApiId|Sträng|Id för den aktuella API.|  
|CurrentOperationId|Sträng|Id för den aktuella åtgärden.|  
|Åtgärd|Sträng|Menytyp.|  
|MenuItems|Samling av [åtgärden menyalternativet](#MenuItem) entiteter.|Åtgärder för den aktuella API.|  
  
##  <a name="MenuItem"></a>Åtgärden menyobjekt  
 Den `operation menu item` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|Sträng|Id för åtgärden.|  
|Rubrik|Sträng|Beskrivning av åtgärden.|  
|HttpMethod|Sträng|Http-metoden för åtgärden.|  
  
##  <a name="Paging"></a>Växling  
 Den `paging` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Sida|Antal|Det aktuella sidnumret.|  
|PageSize|Antal|De maximala resultat som ska visas på en sida.|  
|TotalItemCount|Antal|Antal objekt för visning.|  
|{ShowAll|Booleskt värde|Om du vill visa alla resultat på en sida.|  
|PageCount|Antal|Antalet sidor i resultaten.|  
  
##  <a name="Parameter"></a>Parametern  
 Det här avsnittet beskrivs de `parameter` representation.  
  
|Egenskap|Beskrivning|Typ|  
|--------------|-----------------|----------|  
|namn|Sträng|Parameternamnet.|  
|description|Sträng|Parameterbeskrivning.|  
|värde|Sträng|Parametervärdet.|  
|alternativ|Strängmatris|Värden som definierats för frågeparametervärden.|  
|Krävs|Booleskt värde|Anger om parametern är obligatorisk.|  
|typ|Antal|Om den här parametern är en sökvägsparameter (1) eller en querystring-parameter (2).|  
|TypeName|Sträng|Parametertypen.|  
  
##  <a name="Product"></a>Produkten  
 Den `product` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|Sträng|Resursidentifieraren. Identifierar produkten i den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `products/{pid}` där `{pid}` är en produktidentifierare. Den här egenskapen är skrivskyddad.|  
|Rubrik|Sträng|Namnet på produkten. Får inte vara tomt. Maximal längd är 100 tecken.|  
|Beskrivning|Sträng|Beskrivning av produkten. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|Villkor|Sträng|Produkten villkor för användning. Utvecklare som försöker prenumerera produkten ska visas och krävs för att acceptera villkoren innan de kan slutföras prenumeration.|  
|ProductState|Antal|Anger om produkten är publicerad eller inte. Publicerade produkter är kan upptäckas av utvecklare på developer-portalen. Icke-publicerade produkter visas endast till administratörer.<br /><br /> Tillåtna värden för Produkttillstånd är:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|Booleskt värde|Anger om en användare kan ha flera prenumerationer för den här produkten på samma gång.|  
|MultipleSubscriptionsCount|Antal|Antalet prenumerationer på den här produkten av den aktuella användaren.|  
  
##  <a name="Provider"></a>Providern  
 Den `provider` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Egenskaper|sträng ordlista|Egenskaper för den här autentiseringsprovider.|  
|AuthenticationType|Sträng|Providertyp. (Azure Active Directory, Facebook-inloggningar, Google-konto, Account, Twitter).|  
|Beskrivning|Sträng|Visar namnet på leverantören.|  
  
##  <a name="Representation"></a>Representation  
 Det här avsnittet beskrivs en `representation`.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|ContentType|Sträng|Anger en registrerad eller anpassat innehåll representation, t.ex. `application/xml`.|  
|exempel|Sträng|Ett exempel på representationen.|  
  
##  <a name="Subscription"></a>Prenumerationen  
 Den `subscription` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|Sträng|Resursidentifieraren. Identifierar prenumeration inom den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `subscriptions/{sid}` där `{sid}` är ett prenumerations-ID. Den här egenskapen är skrivskyddad.|  
|ProductId|Sträng|Produkten Resursidentifieraren produktens prenumererar på. Värdet är en giltig relativ URL i formatet `products/{pid}` där `{pid}` är en produktidentifierare.|  
|ProductTitle|Sträng|Namnet på produkten. Får inte vara tomt. Maximal längd är 100 tecken.|  
|Produktbeskrivning|Sträng|Beskrivning av produkten. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|ProductDetailsUrl|Sträng|Relativ URL till produktinformation.|  
|state|Sträng|Tillståndet för prenumerationen. Möjliga tillstånd är:<br /><br /> - `0 - suspended`– prenumerationen blockeras och prenumeranten kan inte anropa alla API: er av produkten.<br /><br /> - `1 - active`– prenumerationen är aktiv.<br /><br /> - `2 - expired`– prenumerationen uppnåtts dess förfallodatum och har inaktiverats.<br /><br /> - `3 - submitted`– prenumerationsbegäran har gjorts av utvecklaren, men har ännu inte godkänts eller avvisats.<br /><br /> - `4 - rejected`– prenumerationsbegäran har nekats av en administratör.<br /><br /> - `5 - cancelled`– prenumerationen har avbrutits av utvecklare eller administratör.|  
|Visningsnamn|Sträng|Visningsnamnet för prenumerationen.|  
|CreatedDate|Datum och tid|Det datum då prenumerationen skapades, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|Booleskt värde|Om prenumerationen kan avbrytas av den aktuella användaren.|  
|IsAwaitingApproval|Booleskt värde|Om prenumerationen väntar på godkännande.|  
|Startdatum|Datum och tid|Startdatum för prenumerationen i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|Datum och tid|Utgångsdatumet för prenumerationen i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|NotificationDate|Datum och tid|Meddelande-datum för prenumerationen i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|primaryKey|Sträng|Nyckeln primära prenumeration. Maximal längd är 256 tecken.|  
|sekundär nyckel|Sträng|Sekundär prenumeration nyckeln. Maximal längd är 256 tecken.|  
|CanBeRenewed|Booleskt värde|Om prenumerationen förnyas av den aktuella användaren.|  
|HasExpired|Booleskt värde|Om prenumerationen har gått ut.|  
|IsRejected|Booleskt värde|Om prenumerationsbegäran nekades.|  
|CancelUrl|Sträng|Relativ Url som avbryter prenumerationen.|  
|RenewUrl|Sträng|Relativ Url att förnya prenumerationen.|  
  
##  <a name="SubscriptionSummary"></a>Prenumerationen sammanfattning  
 Den `subscription summary` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|Sträng|Resursidentifieraren. Identifierar prenumeration inom den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `subscriptions/{sid}` där `{sid}` är ett prenumerations-ID. Den här egenskapen är skrivskyddad.|  
|Visningsnamn|Sträng|Visningsnamnet för prenumerationen|  
  
##  <a name="UserAccountInfo"></a>Information om användarkonto  
 Den `user account info` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Förnamn|Sträng|Förnamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|Efternamn|Sträng|Efternamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|E-post|Sträng|E-postadress. Får inte vara tomt och måste vara unika inom tjänstinstansen. Maximal längd är 254 tecken.|  
|Lösenord|Sträng|Lösenordet för användarkontot.|  
|NameIdentifier|Sträng|Konto-ID detsamma som användarens e-postadress.|  
|ProviderName|Sträng|Providernamn för autentisering.|  
|IsBasicAccount|Booleskt värde|TRUE om det här kontot har registrerats med hjälp av e-post och lösenord. FALSKT om kontot har registrerats med hjälp av en provider.|  
  
##  <a name="UseSignIn"></a>Användaren logga in  
 Den `user sign in` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|E-post|Sträng|E-postadress. Får inte vara tomt och måste vara unika inom tjänstinstansen. Maximal längd är 254 tecken.|  
|Lösenord|Sträng|Lösenordet för användarkontot.|  
|ReturnUrl|Sträng|URL till sidan där användaren har klickat på Logga in.|  
|RememberMe|Booleskt värde|Om du vill spara den aktuella användarens information.|  
|RegistrationEnabled|Booleskt värde|Om registrering är aktiverat.|  
|DelegationEnabled|Booleskt värde|Om delegerade inloggning är aktiverat.|  
|DelegationUrl|Sträng|Delegerad tecken i URL: en, om aktiverat.|  
|SsoSignUpUrl|Sträng|URL: en enkel inloggning för användaren, om sådan finns.|  
|AuxServiceUrl|Sträng|Om den aktuella användaren är administratör är en länk till instansen för tjänsten i den klassiska Azure-portalen.|  
|Leverantörer|Samling av [Provider](#Provider) entiteter|Autentiseringsproviders för den här användaren.|  
|UserRegistrationTerms|Sträng|Villkoren som en användare måste acceptera att innan du loggar in.|  
|UserRegistrationTermsEnabled|Booleskt värde|Om villkoren är aktiverade.|  
  
##  <a name="UserSignUp"></a>Användaren loggar in  
 Den `user sign up` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|LösenordBekräfta|Booleskt värde|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up)anmälan kontroll.|  
|Lösenord|Sträng|Lösenordet för användarkontot.|  
|PasswordVerdictLevel|Antal|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up)anmälan kontroll.|  
|UserRegistrationTerms|Sträng|Villkoren som en användare måste acceptera att innan du loggar in.|  
|UserRegistrationTermsOptions|Antal|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up)anmälan kontroll.|  
|ConsentAccepted|Booleskt värde|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up)anmälan kontroll.|  
|E-post|Sträng|E-postadress. Får inte vara tomt och måste vara unika inom tjänstinstansen. Maximal längd är 254 tecken.|  
|Förnamn|Sträng|Förnamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|Efternamn|Sträng|Efternamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|UserData|Sträng|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up) kontroll.|  
|NameIdentifier|Sträng|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up)anmälan kontroll.|  
|ProviderName|Sträng|Providernamn för autentisering.|

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns [hur du anpassar API Management developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).
