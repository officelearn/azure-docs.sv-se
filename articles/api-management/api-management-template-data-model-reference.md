---
title: Azure API Management-mall datamodell referens | Microsoft Docs
description: Läs mer om entiteten och Skriv garantier för vanliga objekt som används i datamodeller för developer portal mallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 0f27b6b529c2591e37d48e3386190077fc8efc32
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="azure-api-management-template-data-model-reference"></a>Azure API Management-mall datamodell referens
Det här avsnittet beskriver entitet och typ garantier för vanliga objekt som används i datamodeller för developer portal mallar i Azure API Management.  
  
 Mer information om hur du arbetar med mallar finns [hur du anpassar API Management developer-portalen med hjälp av mallar](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
-   [API](#API)  
-   [API-översikten](#APISummary)  
-   [Programmet](#Application)  
-   [Bifogad fil](#Attachment)  
-   [kodexempel](#Sample)  
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
-   [Användarinloggning](#UseSignIn)  
-   [Användaren registrering](#UserSignUp)  
  
##  <a name="API"></a> API  
 Den `API` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|id|sträng|Resursidentifieraren. Identifierar API inom den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `apis/{id}` där `{id}` är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|namn|sträng|Namnet på API: et. Får inte vara tomt. Maximal längd är 100 tecken.|  
|description|sträng|Beskrivning av API: et. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|serviceUrl|sträng|Absolut URL för backend-tjänsten implementerar detta API.|  
|sökväg|sträng|Relativ URL som unikt identifierar denna API och alla dess resurs sökvägar i API Management service-instans. Den läggs till API-slutpunkten bas-URL anges när tjänsten instans skapades för att bilda en offentlig URL för detta API.|  
|Protokoll|matris med tal|Beskriver på vilket protokoll åtgärder i detta API kan anropas. Tillåtna värden är `1 - http` och `2 - https`, eller båda.|  
|authenticationSettings|[Auktoriseringsinställningar server-autentisering](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Samling autentiseringsinställningar som ingår i detta API.|  
|subscriptionKeyParameterNames|objekt|Egenskapen som kan användas för att ange anpassade namn för frågan och/eller huvudet parametrar som innehåller nyckeln för prenumerationen. Om den här egenskapen finns måste det innehålla minst en av följande två egenskaper.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> API-översikten  
 Den `API summary` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|id|sträng|Resursidentifieraren. Identifierar API inom den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `apis/{id}` där `{id}` är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|namn|sträng|Namnet på API: et. Får inte vara tomt. Maximal längd är 100 tecken.|  
|description|sträng|Beskrivning av API: et. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
  
##  <a name="Application"></a> Programmet  
 Den `application` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Unik identifierare för programmet.|  
|Namn|sträng|Titeln på programmet.|  
|Beskrivning|sträng|Beskrivning av programmet.|  
|URL|URI|URI för programmet.|  
|Version|sträng|Versionsinformation för programmet.|  
|Krav|sträng|En beskrivning av kraven för programmet.|  
|Status|nummer|Det aktuella tillståndet för programmet.<br /><br /> -0 - registrerad<br /><br /> -1 - har skickats<br /><br /> -2 - publicerade<br /><br /> -3 - avvisade<br /><br /> -4 - Opublicerat|  
|RegistrationDate|DateTime|Datum och tid som programmet har registrerats.|  
|CategoryId|nummer|Kategori för programmet (ekonomi, underhållning osv.)|  
|DeveloperId|sträng|Unik identifierare för utvecklare som skickats av programmet.|  
|Bifogade filer|Samling av [bilaga](#Attachment) entiteter.|Eventuella bifogade filer för programmet, till exempel skärmdumpar eller ikoner.|  
|Ikon|[Bifogad fil](#Attachment)|Ikonen för den för programmet.|  
  
##  <a name="Attachment"></a> Bifogad fil  
 Den `attachment` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Unikt ID|sträng|Den unika identifieraren för den bifogade filen.|  
|URL|sträng|URL till resursen.|  
|Typ|sträng|Typ av bifogad fil.|  
|ContentType|sträng|Medietyp för den bifogade filen.|  
  
##  <a name="Sample"></a> kodexempel  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|rubrik|sträng|Namnet på åtgärden.|  
|fragment|sträng|Den här egenskapen är inaktuell och bör inte användas.|  
|pensel|sträng|Vilka kodsyntax färgläggning mall som ska användas när du visar kodexemplet. Tillåtna värden är `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, och `csharp`.|  
|mall|sträng|Namnet på den här koden exempelmall.|  
|brödtext|sträng|En platshållare för koden exempel del av kodavsnittet.|  
|metod|sträng|HTTP-metoden för åtgärden.|  
|schemat|sträng|Protokollet som ska användas för begäran igen.|  
|sökväg|sträng|Sökvägen till åtgärden.|  
|DocumentDB|sträng|Exempel på frågan sträng med definierade parametrar.|  
|värd|sträng|URL för API Management-tjänsten gateway-API: t som innehåller den här åtgärden.|  
|rubriker|Samling av [huvud](#Header) entiteter.|Huvuden för den här åtgärden.|  
|parameters|Samling av [parametern](#Parameter) entiteter.|Parametrar som definierats för den här åtgärden.|  
  
##  <a name="Comment"></a> Kommentar  
 Den `API` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|nummer|Id för kommentaren.|  
|CommentText|sträng|Innehållet i kommentaren. Kan innehålla HTML.|  
|DeveloperCompany|sträng|Företagsnamn som utvecklare.|  
|PostedOn|DateTime|Datum och tid som kommentaren anslogs.|  
  
##  <a name="Issue"></a> Problemet  
 Den `issue` entiteten har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Den unika identifieraren för problemet.|  
|ApiID|sträng|Id för API: et för vilken det här problemet har rapporterats.|  
|Namn|sträng|Titel på problemet.|  
|Beskrivning|sträng|Beskrivning av problemet.|  
|SubscriptionDeveloperName|sträng|Förnamn som utvecklare som rapporterats av problemet.|  
|IssueState|sträng|Det aktuella tillståndet för problemet. Möjliga värden är Proposed, öppnade, stängd.|  
|ReportedOn|DateTime|Datum och tid problemet har rapporterats.|  
|Kommentarer|Samling av [kommentar](#Comment) entiteter.|Kommentarer om det här problemet.|  
|Bifogade filer|Samling av [bilaga](api-management-template-data-model-reference.md#Attachment) entiteter.|Bifogade till problemet.|  
|Tjänster|Samling av [API](#API) entiteter.|API: erna prenumererar på användaren som skapade problemet.|  
  
##  <a name="Filtering"></a> Filtrering  
 Den `filtering` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Mönster|sträng|Aktuella söktermen; eller `null` om det finns ingen sökterm.|  
|Platshållare|sträng|Texten som visas i sökrutan när det finns ingen sökterm som angetts.|  
  
##  <a name="Header"></a> Huvudet  
 Det här avsnittet beskrivs de `parameter` representation.  
  
|Egenskap|Beskrivning|Typ|  
|--------------|-----------------|----------|  
|namn|sträng|Parameternamnet.|  
|description|sträng|Parameterbeskrivning.|  
|värde|sträng|Huvudets värde.|  
|TypeName|sträng|Datatypen för huvudvärde.|  
|alternativ|sträng|Alternativ.|  
|Krävs|boolesk|Anger om sidhuvudet krävs.|  
|Skrivskyddad|boolesk|Anger om sidhuvudet är skrivskyddad.|  
  
##  <a name="HTTPRequest"></a> HTTP-begäran  
 Det här avsnittet beskrivs de `request` representation.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|description|sträng|Beskrivning av åtgärden begäran.|  
|rubriker|matris med [huvud](#Header) entiteter.|Huvuden för begäran.|  
|parameters|matris med [Parameter](#Parameter)|De parametrar som begäran igen.|  
|garantier|matris med [Representation](#Representation)|Åtgärden begäran garantier samling.|  
  
##  <a name="HTTPResponse"></a> HTTP-svar  
 Det här avsnittet beskrivs de `response` representation.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|statuskod|Positivt heltal|Åtgärden Svarets statuskod.|  
|description|sträng|Beskrivning av åtgärden svar.|  
|garantier|matris med [Representation](#Representation)|Åtgärden svar garantier samling.|  
  
##  <a name="Operation"></a> åtgärden  
 Den `operation` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|id|sträng|Resursidentifieraren. Identifierar åtgärden inom den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `apis/{aid}/operations/{id}` där `{aid}` är en API-identifierare och `{id}` är en identifierare för åtgärden. Den här egenskapen är skrivskyddad.|  
|namn|sträng|Namnet på åtgärden. Får inte vara tomt. Maximal längd är 100 tecken.|  
|description|sträng|Beskrivning av åtgärden. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|schemat|sträng|Beskriver på vilket protokoll åtgärder i detta API kan anropas. Tillåtna värden är `http`, `https`, eller båda `http` och `https`.|  
|uriTemplate|sträng|Relativa URL: en mall identifierar målresursen för den här åtgärden. Kan innehålla parametrar. Exempel: `customers/{cid}/orders/{oid}/?date={date}`|  
|värd|sträng|URL för API Management-gateway som är värd för API: et.|  
|HttpMethod|sträng|HTTP-åtgärdsmetod.|  
|Begäran|[HTTP-begäran](#HTTPRequest)|En entitet som innehåller detaljer om förfrågan.|  
|svar|matris med [HTTP-svar](#HTTPResponse)|Matris med åtgärden [HTTP-svar](#HTTPResponse) entiteter.|  
  
##  <a name="Menu"></a> Åtgärd-menyn  
 Den `operation menu` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|ApiId|sträng|Id för den aktuella API.|  
|CurrentOperationId|sträng|Id för den aktuella åtgärden.|  
|Åtgärd|sträng|Menytyp.|  
|MenuItems|Samling av [åtgärden menyalternativet](#MenuItem) entiteter.|Åtgärder för den aktuella API.|  
  
##  <a name="MenuItem"></a> Åtgärden menyobjekt  
 Den `operation menu item` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Id för åtgärden.|  
|Namn|sträng|Beskrivning av åtgärden.|  
|HttpMethod|sträng|Http-metoden för åtgärden.|  
  
##  <a name="Paging"></a> Växling  
 Den `paging` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Sida|nummer|Det aktuella sidnumret.|  
|PageSize|nummer|De maximala resultat som ska visas på en sida.|  
|TotalItemCount|nummer|Antal objekt för visning.|  
|{ShowAll|boolesk|Om du vill visa alla resultat på en sida.|  
|PageCount|nummer|Antalet sidor i resultaten.|  
  
##  <a name="Parameter"></a> Parametern  
 Det här avsnittet beskrivs de `parameter` representation.  
  
|Egenskap|Beskrivning|Typ|  
|--------------|-----------------|----------|  
|namn|sträng|Parameternamnet.|  
|description|sträng|Parameterbeskrivning.|  
|värde|sträng|Parametervärdet.|  
|alternativ|Strängmatris|Värden som definierats för frågeparametervärden.|  
|Krävs|boolesk|Anger om parametern är obligatorisk.|  
|typ|nummer|Om den här parametern är en sökvägsparameter (1) eller en querystring-parameter (2).|  
|TypeName|sträng|Parametertypen.|  
  
##  <a name="Product"></a> Produkten  
 Den `product` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Resursidentifieraren. Identifierar produkten i den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `products/{pid}` där `{pid}` är en produktidentifierare. Den här egenskapen är skrivskyddad.|  
|Namn|sträng|Namnet på produkten. Får inte vara tomt. Maximal längd är 100 tecken.|  
|Beskrivning|sträng|Beskrivning av produkten. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|Villkor|sträng|Produkten villkor för användning. Utvecklare som försöker prenumerera produkten ska visas och krävs för att acceptera villkoren innan de kan slutföras prenumeration.|  
|ProductState|nummer|Anger om produkten är publicerad eller inte. Publicerade produkter är kan upptäckas av utvecklare på developer-portalen. Icke-publicerade produkter visas endast till administratörer.<br /><br /> Tillåtna värden för Produkttillstånd är:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|boolesk|Anger om en användare kan ha flera prenumerationer för den här produkten på samma gång.|  
|MultipleSubscriptionsCount|nummer|Maximalt antal prenumerationer på den här produkten en användare får ha på samma gång.|  
  
##  <a name="Provider"></a> Providern  
 Den `provider` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Egenskaper|sträng ordlista|Egenskaper för den här autentiseringsprovider.|  
|AuthenticationType|sträng|Providertyp. (Azure Active Directory, Facebook-inloggningar, Google-konto, Account, Twitter).|  
|Beskrivning|sträng|Visar namnet på leverantören.|  
  
##  <a name="Representation"></a> Representation  
 Det här avsnittet beskrivs en `representation`.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|ContentType|sträng|Anger en registrerad eller anpassat innehåll representation, till exempel `application/xml`.|  
|exempel|sträng|Ett exempel på representationen.|  
  
##  <a name="Subscription"></a> Prenumerationen  
 Den `subscription` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Resursidentifieraren. Identifierar prenumeration inom den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `subscriptions/{sid}` där `{sid}` är ett prenumerations-ID. Den här egenskapen är skrivskyddad.|  
|Produkt-ID|sträng|Produkten Resursidentifieraren produktens prenumererar på. Värdet är en giltig relativ URL i formatet `products/{pid}` där `{pid}` är en produktidentifierare.|  
|ProductTitle|sträng|Namnet på produkten. Får inte vara tomt. Maximal längd är 100 tecken.|  
|Produktbeskrivning|sträng|Beskrivning av produkten. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|ProductDetailsUrl|sträng|Relativ URL till produktinformation.|  
|state|sträng|Tillståndet för prenumerationen. Möjliga tillstånd är:<br /><br /> - `0 - suspended` – prenumerationen blockeras och prenumeranten kan inte anropa alla API: er av produkten.<br /><br /> - `1 - active` – prenumerationen är aktiv.<br /><br /> - `2 - expired` – prenumerationen uppnåtts dess förfallodatum och har inaktiverats.<br /><br /> - `3 - submitted` – prenumerationsbegäran har gjorts av utvecklaren, men har ännu inte godkänts eller avvisats.<br /><br /> - `4 - rejected` – prenumerationsbegäran har nekats av en administratör.<br /><br /> - `5 - cancelled` – prenumerationen har avbrutits av utvecklare eller administratör.|  
|Visningsnamn|sträng|Visningsnamnet för prenumerationen.|  
|CreatedDate|Datum och tid|Det datum då prenumerationen skapades, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|boolesk|Om prenumerationen kan avbrytas av den aktuella användaren.|  
|IsAwaitingApproval|boolesk|Om prenumerationen väntar på godkännande.|  
|Startdatum|Datum och tid|Startdatum för prenumerationen i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|Datum och tid|Utgångsdatumet för prenumerationen i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|NotificationDate|Datum och tid|Meddelande-datum för prenumerationen i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|primaryKey|sträng|Nyckeln primära prenumeration. Maximal längd är 256 tecken.|  
|sekundär nyckel|sträng|Sekundär prenumeration nyckeln. Maximal längd är 256 tecken.|  
|CanBeRenewed|boolesk|Om prenumerationen förnyas av den aktuella användaren.|  
|HasExpired|boolesk|Om prenumerationen har gått ut.|  
|IsRejected|boolesk|Om prenumerationsbegäran nekades.|  
|CancelUrl|sträng|Relativ Url som avbryter prenumerationen.|  
|RenewUrl|sträng|Relativ Url att förnya prenumerationen.|  
  
##  <a name="SubscriptionSummary"></a> Prenumerationen sammanfattning  
 Den `subscription summary` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Resursidentifieraren. Identifierar prenumeration inom den aktuella API Management service-instansen. Värdet är en giltig relativ URL i formatet `subscriptions/{sid}` där `{sid}` är ett prenumerations-ID. Den här egenskapen är skrivskyddad.|  
|Visningsnamn|sträng|Visningsnamnet för prenumerationen|  
  
##  <a name="UserAccountInfo"></a> Information om användarkonto  
 Den `user account info` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Förnamn|sträng|Förnamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|Efternamn|sträng|Efternamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|E-post|sträng|E-postadress. Får inte vara tomt och måste vara unika inom tjänstinstansen. Maximal längd är 254 tecken.|  
|Lösenord|sträng|Lösenordet för användarkontot.|  
|NameIdentifier|sträng|Konto-ID detsamma som användarens e-postadress.|  
|ProviderName|sträng|Providernamn för autentisering.|  
|IsBasicAccount|boolesk|TRUE om det här kontot har registrerats med hjälp av e-post och lösenord. FALSKT om kontot har registrerats med hjälp av en provider.|  
  
##  <a name="UseSignIn"></a> Användaren logga in  
 Den `user sign in` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|E-post|sträng|E-postadress. Får inte vara tomt och måste vara unika inom tjänstinstansen. Maximal längd är 254 tecken.|  
|Lösenord|sträng|Lösenordet för användarkontot.|  
|ReturnUrl|sträng|URL till sidan där användaren har klickat på Logga in.|  
|RememberMe|boolesk|Om du vill spara den aktuella användarens information.|  
|RegistrationEnabled|boolesk|Om registrering är aktiverat.|  
|DelegationEnabled|boolesk|Om delegerade inloggning är aktiverat.|  
|DelegationUrl|sträng|Delegerad tecken i URL: en, om aktiverat.|  
|SsoSignUpUrl|sträng|URL: en enkel inloggning för användaren, om sådan finns.|  
|AuxServiceUrl|sträng|Om den aktuella användaren är administratör är en länk till instansen för tjänsten i Azure-portalen.|  
|Leverantörer|Samling av [Provider](#Provider) entiteter|Autentiseringsproviders för den här användaren.|  
|UserRegistrationTerms|sträng|Villkoren som en användare måste acceptera att innan du loggar in.|  
|UserRegistrationTermsEnabled|boolesk|Om villkoren är aktiverade.|  
  
##  <a name="UserSignUp"></a> Användaren loggar in  
 Den `user sign up` entiteten har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|LösenordBekräfta|boolesk|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up)anmälan kontroll.|  
|Lösenord|sträng|Lösenordet för användarkontot.|  
|PasswordVerdictLevel|nummer|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up)anmälan kontroll.|  
|UserRegistrationTerms|sträng|Villkoren som en användare måste acceptera att innan du loggar in.|  
|UserRegistrationTermsOptions|nummer|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up)anmälan kontroll.|  
|ConsentAccepted|boolesk|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up)anmälan kontroll.|  
|E-post|sträng|E-postadress. Får inte vara tomt och måste vara unika inom tjänstinstansen. Maximal längd är 254 tecken.|  
|Förnamn|sträng|Förnamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|Efternamn|sträng|Efternamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|UserData|sträng|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up) kontroll.|  
|NameIdentifier|sträng|Värdet som används av den [anmälan](api-management-page-controls.md#sign-up)anmälan kontroll.|  
|ProviderName|sträng|Providernamn för autentisering.|

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns [hur du anpassar API Management developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).
