---
title: Azure API Management-malldatamodellreferens | Microsoft-dokument
description: Lär dig mer om entitets- och typrepresentationer för vanliga objekt som används i datamodellerna för utvecklarportalmallarna i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 5625ff7e4fc51b9b6b894698719247902a480f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243944"
---
# <a name="azure-api-management-template-data-model-reference"></a>Azure API Management-malldatamodellreferens
I det här avsnittet beskrivs entitets- och typrepresentationer för vanliga objekt som används i datamodellerna för utvecklarportalmallarna i Azure API Management.  
  
 Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)för API Management med hjälp av mallar .  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Referens

-   [Api](#API)  
-   [API-sammanfattning](#APISummary)  
-   [Program](#Application)  
-   [Bilaga](#Attachment)  
-   [Kodexempel](#Sample)  
-   [Kommentar](#Comment)  
-   [Filtrering](#Filtering)  
-   [Huvudet](#Header)  
-   [HTTP-begäran](#HTTPRequest)  
-   [HTTP-svar](#HTTPResponse)  
-   [Problem](#Issue)  
-   [Drift](#Operation)  
-   [Menyn Åtgärd](#Menu)  
-   [Menyalternativ för Operation](#MenuItem)  
-   [Sidindelning](#Paging)  
-   [Parametern](#Parameter)  
-   [Produkt](#Product)  
-   [Leverantör](#Provider)  
-   [Representation](#Representation)  
-   [Prenumeration](#Subscription)  
-   [Sammanfattning av prenumeration](#SubscriptionSummary)  
-   [Information om användarkonto](#UserAccountInfo)  
-   [Inloggning för användare](#UseSignIn)  
-   [Registrering av användare](#UserSignUp)  
  
##  <a name="api"></a><a name="API"></a>Api  
 Entiteten `API` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`id`|sträng|Resursidentifierare. Identifierar API:et unikt i den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL `apis/{id}` `{id}` i formatet där är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|`name`|sträng|Api:ets namn. Får inte vara tom. Maximal längd är 100 tecken.|  
|`description`|sträng|Beskrivning av API:et. Får inte vara tom. Kan innehålla HTML-formateringstaggar. Maximal längd är 1000 tecken.|  
|`serviceUrl`|sträng|Absolut URL för serverdtjänsten som implementerar det här API:et.|  
|`path`|sträng|Relativ URL unikt identifiera detta API och alla dess resurssökvägar i API Management tjänstinstansen. Den läggs till i API-slutpunktsbas-URL:en som anges när tjänstinstansen skapades för att skapa en offentlig URL för det här API:et.|  
|`protocols`|matris med tal|Beskriver vilka protokoll åtgärderna i det här API:et kan anropas. Tillåtna `1 - http` värden `2 - https`är och , eller båda.|  
|`authenticationSettings`|[Autentiseringsinställningar för auktoriseringsserver](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Insamling av autentiseringsinställningar som ingår i det här API:et.|  
|`subscriptionKeyParameterNames`|objekt|Valfri egenskap som kan användas för att ange anpassade namn för fråge- och/eller huvudparametrar som innehåller prenumerationsnyckeln. När den här egenskapen finns måste den innehålla minst en av följande två egenskaper.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="api-summary"></a><a name="APISummary"></a>API-sammanfattning  
 Entiteten `API summary` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`id`|sträng|Resursidentifierare. Identifierar API:et unikt i den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL `apis/{id}` `{id}` i formatet där är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|`name`|sträng|Api:ets namn. Får inte vara tom. Maximal längd är 100 tecken.|  
|`description`|sträng|Beskrivning av API:et. Får inte vara tom. Kan innehålla HTML-formateringstaggar. Maximal längd är 1000 tecken.|  
  
##  <a name="application"></a><a name="Application"></a>Program  
 Entiteten `application` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|sträng|Den unika identifieraren för programmet.|  
|`Title`|sträng|Titeln på ansökan.|  
|`Description`|sträng|Beskrivningen av ansökan.|  
|`Url`|URI|Uri för programmet.|  
|`Version`|sträng|Versionsinformation för programmet.|  
|`Requirements`|sträng|En beskrivning av kraven för ansökan.|  
|`State`|nummer|Det aktuella tillståndet för programmet.<br /><br /> - 0 - Registrerad<br /><br /> - 1 - Inlämnad<br /><br /> - 2 - Publicerad<br /><br /> - 3 - Avvisad<br /><br /> - 4 - Opublicerad|  
|`RegistrationDate`|DateTime|Datum och tid då ansökan registrerades.|  
|`CategoryId`|nummer|Kategorin för ansökan (Ekonomi, underhållning, etc.)|  
|`DeveloperId`|sträng|Den unika identifieraren för utvecklaren som skickade in programmet.|  
|`Attachments`|Insamling av [bifogade filer.](#Attachment)|Eventuella bilagor för programmet, till exempel skärmdumpar eller ikoner.|  
|`Icon`|[Bilaga](#Attachment)|Ikonen för programmet.|  
  
##  <a name="attachment"></a><a name="Attachment"></a>Bifogad fil  
 Entiteten `attachment` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`UniqueId`|sträng|Den unika identifieraren för den bifogade filen.|  
|`Url`|sträng|URL:en för resursen.|  
|`Type`|sträng|Typ av bilaga.|  
|`ContentType`|sträng|Den bifogadens medietyp.|  
  
##  <a name="code-sample"></a><a name="Sample"></a>Exempel på kod  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`title`|sträng|Namnet på operationen.|  
|`snippet`|sträng|Den här egenskapen är inaktuell och bör inte användas.|  
|`brush`|sträng|Vilken kodsyntaxfärgmall som ska användas när kodexemplet visas. Tillåtna `plain`värden `php` `java`är `xml` `objc`, `python` `ruby`, `csharp`, , , , och .|  
|`template`|sträng|Namnet på den här kodexemplmallen.|  
|`body`|sträng|En platshållare för kodexemplet av kodavsnittet.|  
|`method`|sträng|HTTP-metoden för åtgärden.|  
|`scheme`|sträng|Protokollet som ska användas för åtgärdsbegäran.|  
|`path`|sträng|Sökvägen till operationen.|  
|`query`|sträng|Exempel på frågesträng med definierade parametrar.|  
|`host`|sträng|URL:en för API Management-tjänstgatewayen för API:et som innehåller den här åtgärden.|  
|`headers`|Insamling [Header](#Header) av rubrikentiteter.|Rubriker för den här åtgärden.|  
|`parameters`|Insamling av [parameterentiteter.](#Parameter)|Parametrar som har definierats för den här åtgärden.|  
  
##  <a name="comment"></a><a name="Comment"></a>Kommentar  
 Entiteten `API` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|nummer|ID för kommentaren.|  
|`CommentText`|sträng|Kommentarens brödtext. Kan innehålla HTML.|  
|`DeveloperCompany`|sträng|Utvecklarens företagsnamn.|  
|`PostedOn`|DateTime|Datum och tid då kommentaren bokfördes.|  
  
##  <a name="issue"></a><a name="Issue"></a>Frågan  
 Entiteten `issue` har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|sträng|Den unika identifieraren för problemet.|  
|`ApiID`|sträng|ID:et för API:et som det här problemet rapporterades för.|  
|`Title`|sträng|Frågans namn.|  
|`Description`|sträng|Beskrivning av problemet.|  
|`SubscriptionDeveloperName`|sträng|Förnamn på utvecklaren som rapporterade problemet.|  
|`IssueState`|sträng|Det aktuella läget för problemet. Möjliga värden är Föreslagna, Öppnade, Stängda.|  
|`ReportedOn`|DateTime|Datum och tid då problemet rapporterades.|  
|`Comments`|Samling [Comment](#Comment) av kommentarentiteter.|Kommentarer till denna fråga.|  
|`Attachments`|Insamling av [bifogade filer.](api-management-template-data-model-reference.md#Attachment)|Eventuella bilagor till problemet.|  
|`Services`|Insamling [API](#API) av API-entiteter.|API:erna som prenumererar på av användaren som har lämnat in problemet.|  
  
##  <a name="filtering"></a><a name="Filtering"></a>Filtrering  
 Entiteten `filtering` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Pattern`|sträng|Den aktuella söktermen; eller `null` om det inte finns någon sökterm.|  
|`Placeholder`|sträng|Texten som ska visas i sökrutan när inget söktermen har angetts.|  
  
##  <a name="header"></a><a name="Header"></a>Huvudet  
 I det här `parameter` avsnittet beskrivs representationen.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|-----------------|----------|  
|`name`|sträng|Parameternamn.|  
|`description`|sträng|Parameterbeskrivning.|  
|`value`|sträng|Huvudvärde.|  
|`typeName`|sträng|Datatyp av huvudvärde.|  
|`options`|sträng|Alternativ.|  
|`required`|boolean|Om huvudet krävs.|  
|`readOnly`|boolean|Om rubriken är skrivskyddad.|  
  
##  <a name="http-request"></a><a name="HTTPRequest"></a>HTTP-begäran  
 I det här `request` avsnittet beskrivs representationen.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`description`|sträng|Beskrivning av åtgärdsbegäran.|  
|`headers`|matris [med](#Header) rubrikentiteter.|Begär rubriker.|  
|`parameters`|matris med [parameter](#Parameter)|Parametrar för insamling av åtgärdsbegäran.|  
|`representations`|matris av [Framställning](#Representation)|Insamling av utfästelser om verksamhetsbegäran.|  
  
##  <a name="http-response"></a><a name="HTTPResponse"></a>HTTP-svar  
 I det här `response` avsnittet beskrivs representationen.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`statusCode`|positivt heltal|Statuskod för åtgärdssvar.|  
|`description`|sträng|Beskrivning av åtgärdssvar.|  
|`representations`|matris av [Framställning](#Representation)|Insamling av insatssvarsrepresentationer.|  
  
##  <a name="operation"></a><a name="Operation"></a>Drift  
 Entiteten `operation` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`id`|sträng|Resursidentifierare. Identifierar åtgärden unikt i den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL `apis/{aid}/operations/{id}` `{aid}` i formatet där `{id}` är en API-identifierare och är en åtgärdsidentifierare. Den här egenskapen är skrivskyddad.|  
|`name`|sträng|Operationens namn. Får inte vara tom. Maximal längd är 100 tecken.|  
|`description`|sträng|Beskrivning av operationen. Får inte vara tom. Kan innehålla HTML-formateringstaggar. Maximal längd är 1000 tecken.|  
|`scheme`|sträng|Beskriver vilka protokoll åtgärderna i det här API:et kan anropas. Tillåtna `http`värden `https`är `http` , `https`, eller både och .|  
|`uriTemplate`|sträng|Relativ URL-mall som identifierar målresursen för den här åtgärden. Kan innehålla parametrar. Exempel: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|sträng|API Management gateway URL som är värd för API.|  
|`httpMethod`|sträng|Fungera HTTP-metoden.|  
|`request`|[HTTP-begäran](#HTTPRequest)|En entitet som innehåller information om begäran.|  
|`responses`|matris med [HTTP-svar](#HTTPResponse)|Matris för http-svarsentiteter. [HTTP Response](#HTTPResponse)|  
  
##  <a name="operation-menu"></a><a name="Menu"></a>Menyn Åtgärd  
 Entiteten `operation menu` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`ApiId`|sträng|ID:et för det aktuella API:et.|  
|`CurrentOperationId`|sträng|ID:et för den aktuella åtgärden.|  
|`Action`|sträng|Menytypen.|  
|`MenuItems`|Samling av [menyobjektenheter](#MenuItem) för funktion.|Åtgärderna för det aktuella API:et.|  
  
##  <a name="operation-menu-item"></a><a name="MenuItem"></a>Menyalternativ för Operation  
 Entiteten `operation menu item` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|sträng|ID för åtgärden.|  
|`Title`|sträng|Beskrivning av operationen.|  
|`HttpMethod`|sträng|Http-metoden för operationen.|  
  
##  <a name="paging"></a><a name="Paging"></a>Personsökning  
 Entiteten `paging` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Page`|nummer|Det aktuella sidnumret.|  
|`PageSize`|nummer|Det maximala resultatet som ska visas på en enda sida.|  
|`TotalItemCount`|nummer|Antalet objekt som ska visas.|  
|`ShowAll`|boolean|Om du vill göra alla resultat på en enda sida.|  
|`PageCount`|nummer|Antalet resultatsidor.|  
  
##  <a name="parameter"></a><a name="Parameter"></a>Parametern  
 I det här `parameter` avsnittet beskrivs representationen.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|-----------------|----------|  
|`name`|sträng|Parameternamn.|  
|`description`|sträng|Parameterbeskrivning.|  
|`value`|sträng|Parametervärde.|  
|`options`|strängmatris|Värden som definierats för frågeparametervärden.|  
|`required`|boolean|Anger om parameter krävs eller inte.|  
|`kind`|nummer|Om den här parametern är en sökvägsparameter (1) eller en frågestringsparameter (2).|  
|`typeName`|sträng|Parametertyp.|  
  
##  <a name="product"></a><a name="Product"></a>Produkt  
 Entiteten `product` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|sträng|Resursidentifierare. Identifierar produkten unikt i den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL `products/{pid}` `{pid}` i formatet där är en produktidentifierare. Den här egenskapen är skrivskyddad.|  
|`Title`|sträng|Namn på produkten. Får inte vara tom. Maximal längd är 100 tecken.|  
|`Description`|sträng|Beskrivning av produkten. Får inte vara tom. Kan innehålla HTML-formateringstaggar. Maximal längd är 1000 tecken.|  
|`Terms`|sträng|Produktens användarvillkor. Utvecklare som försöker prenumerera på produkten kommer att presenteras och måste acceptera dessa villkor innan de kan slutföra prenumerationsprocessen.|  
|`ProductState`|nummer|Anger om produkten ska publiceras eller inte. Publicerade produkter kan identifieras av utvecklare på utvecklarportalen. Icke-publicerade produkter är bara synliga för administratörer.<br /><br /> De tillåtna värdena för produkttillstånd är:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Anger om en användare kan ha flera prenumerationer på den här produkten samtidigt.|  
|`MultipleSubscriptionsCount`|nummer|Maximalt antal prenumerationer på den här produkten som en användare får ha samtidigt.|  
  
##  <a name="provider"></a><a name="Provider"></a>Leverantör  
 Entiteten `provider` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Properties`|strängordlista|Egenskaper för den här autentiseringsprovidern.|  
|`AuthenticationType`|sträng|Providertypen. (Azure Active Directory, Facebook-inloggning, Google-konto, Microsoft-konto, Twitter).|  
|`Caption`|sträng|Visa leverantörens namn.|  
  
##  <a name="representation"></a><a name="Representation"></a>Representation  
 I det här `representation`avsnittet beskrivs en .  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`contentType`|sträng|Anger en registrerad eller anpassad innehållstyp för `application/xml`den här representationen, till exempel .|  
|`sample`|sträng|Ett exempel på representationen.|  
  
##  <a name="subscription"></a><a name="Subscription"></a>Prenumeration  
 Entiteten `subscription` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|sträng|Resursidentifierare. Identifierar prenumerationen unikt i den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL `subscriptions/{sid}` `{sid}` i formatet där är en prenumerationsidentifierare. Den här egenskapen är skrivskyddad.|  
|`ProductId`|sträng|Produktresursidentifieraren för den tecknade produkten. Värdet är en giltig relativ URL `products/{pid}` `{pid}` i formatet där är en produktidentifierare.|  
|`ProductTitle`|sträng|Namn på produkten. Får inte vara tom. Maximal längd är 100 tecken.|  
|`ProductDescription`|sträng|Beskrivning av produkten. Får inte vara tom. Kan innehålla HTML-formateringstaggar. Maximal längd är 1000 tecken.|  
|`ProductDetailsUrl`|sträng|Relativ URL till produktinformationen.|  
|`state`|sträng|Tillståndet för prenumerationen. Möjliga tillstånd är:<br /><br /> - `0 - suspended`– prenumerationen är blockerad och abonnenten kan inte anropa några API:er för produkten.<br /><br /> - `1 - active`– prenumerationen är aktiv.<br /><br /> - `2 - expired`– prenumerationen nådde sitt utgångsdatum och inaktiverades.<br /><br /> - `3 - submitted`– prenumerationsbegäran har gjorts av utvecklaren, men har ännu inte godkänts eller avvisats.<br /><br /> - `4 - rejected`– prenumerationsbegäran har nekats av en administratör.<br /><br /> - `5 - cancelled`– prenumerationen har avbrutits av utvecklaren eller administratören.|  
|`DisplayName`|sträng|Visa prenumerationens namn.|  
|`CreatedDate`|Datetime|Det datum då prenumerationen skapades i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|boolean|Om prenumerationen kan avbrytas av den aktuella användaren.|  
|`IsAwaitingApproval`|boolean|Om prenumerationen väntar på godkännande.|  
|`StartDate`|Datetime|Startdatum för prenumerationen, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Datetime|Utgångsdatum för prenumerationen, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Datetime|Meddelandedatum för prenumerationen, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|sträng|Den primära prenumerationsnyckeln. Maximal längd är 256 tecken.|  
|`secondaryKey`|sträng|Den sekundära prenumerationsnyckeln. Maximal längd är 256 tecken.|  
|`CanBeRenewed`|boolean|Om prenumerationen kan förnyas av den aktuella användaren.|  
|`HasExpired`|boolean|Om prenumerationen har upphört att gälla.|  
|`IsRejected`|boolean|Om prenumerationsbegäran nekades.|  
|`CancelUrl`|sträng|Den relativa url:en för att avbryta prenumerationen.|  
|`RenewUrl`|sträng|Den relativa url:en för att förnya prenumerationen.|  
  
##  <a name="subscription-summary"></a><a name="SubscriptionSummary"></a>Sammanfattning av prenumeration  
 Entiteten `subscription summary` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|sträng|Resursidentifierare. Identifierar prenumerationen unikt i den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL `subscriptions/{sid}` `{sid}` i formatet där är en prenumerationsidentifierare. Den här egenskapen är skrivskyddad.|  
|`DisplayName`|sträng|Prenumerationens visningsnamn|  
  
##  <a name="user-account-info"></a><a name="UserAccountInfo"></a>Information om användarkonto  
 Entiteten `user account info` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`FirstName`|sträng|Förnamn. Får inte vara tom. Maximal längd är 100 tecken.|  
|`LastName`|sträng|Efternamn. Får inte vara tom. Maximal längd är 100 tecken.|  
|`Email`|sträng|E-postadress. Får inte vara tomt och måste vara unikt i tjänstinstansen. Maximal längd är 254 tecken.|  
|`Password`|sträng|Lösenord för användarkonto.|  
|`NameIdentifier`|sträng|Kontoidentifierare, samma som användarens e-post.|  
|`ProviderName`|sträng|Namn på autentiseringsprovidern.|  
|`IsBasicAccount`|boolean|Sant om det här kontot har registrerats med e-post och lösenord. falskt om kontot registrerades med hjälp av en leverantör.|  
  
##  <a name="user-sign-in"></a><a name="UseSignIn"></a>Logga in för användare  
 Entiteten `user sign in` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Email`|sträng|E-postadress. Får inte vara tomt och måste vara unikt i tjänstinstansen. Maximal längd är 254 tecken.|  
|`Password`|sträng|Lösenord för användarkonto.|  
|`ReturnUrl`|sträng|Url:en till den sida där användaren klickade logga in.|  
|`RememberMe`|boolean|Om den aktuella användarens information ska sparas.|  
|`RegistrationEnabled`|boolean|Om registrering är aktiverat.|  
|`DelegationEnabled`|boolean|Om delegerad inloggning är aktiverat.|  
|`DelegationUrl`|sträng|Den delegerade inloggningsadressen, om den är aktiverad.|  
|`SsoSignUpUrl`|sträng|Den enda inloggningsadressen för användaren, om sådan finns.|  
|`AuxServiceUrl`|sträng|Om den aktuella användaren är administratör är detta en länk till tjänstinstansen i Azure-portalen.|  
|`Providers`|Insamling av [providerenheter](#Provider)|Autentiseringsleverantörerna för den här användaren.|  
|`UserRegistrationTerms`|sträng|Villkor som en användare måste godkänna innan han eller hon loggar in.|  
|`UserRegistrationTermsEnabled`|boolean|Om villkoren är aktiverade.|  
  
##  <a name="user-sign-up"></a><a name="UserSignUp"></a>Registrera dig för användare  
 Entiteten `user sign up` har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Värde som [sign-up](api-management-page-controls.md#sign-up)används av registreringsanmälningskontrollen.|  
|`Password`|sträng|Lösenord för användarkonto.|  
|`PasswordVerdictLevel`|nummer|Värde som [sign-up](api-management-page-controls.md#sign-up)används av registreringsanmälningskontrollen.|  
|`UserRegistrationTerms`|sträng|Villkor som en användare måste godkänna innan han eller hon loggar in.|  
|`UserRegistrationTermsOptions`|nummer|Värde som [sign-up](api-management-page-controls.md#sign-up)används av registreringsanmälningskontrollen.|  
|`ConsentAccepted`|boolean|Värde som [sign-up](api-management-page-controls.md#sign-up)används av registreringsanmälningskontrollen.|  
|`Email`|sträng|E-postadress. Får inte vara tomt och måste vara unikt i tjänstinstansen. Maximal längd är 254 tecken.|  
|`FirstName`|sträng|Förnamn. Får inte vara tom. Maximal längd är 100 tecken.|  
|`LastName`|sträng|Efternamn. Får inte vara tom. Maximal längd är 100 tecken.|  
|`UserData`|sträng|Värde som [sign-up](api-management-page-controls.md#sign-up) används av registreringskontrollen.|  
|`NameIdentifier`|sträng|Värde som [sign-up](api-management-page-controls.md#sign-up)används av registreringsanmälningskontrollen.|  
|`ProviderName`|sträng|Namn på autentiseringsprovidern.|

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](api-management-developer-portal-templates.md)för API Management med hjälp av mallar .
