---
title: Azure API Management-mall datamodelldata referens | Microsoft Docs
description: Lär dig om garantier för entiteten och typ för vanliga objekt som används i datamodellerna för utvecklarportalsmallar i Azure API Management.
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
ms.openlocfilehash: 8c21ed737cab98c9136e1c1991997ff3931a4c9d
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447205"
---
# <a name="azure-api-management-template-data-model-reference"></a>Azure API Management-mall referens för datamodell
Det här avsnittet beskriver entiteten och Skriv garantier för vanliga objekt som används i datamodellerna för utvecklarportalsmallar i Azure API Management.  
  
 Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

Developer-portalen är inte tillgänglig på nivån för förbrukning.

## <a name="reference"></a>Referens

-   [API](#API)  
-   [API-sammanfattning](#APISummary)  
-   [Programmet](#Application)  
-   [Bifogad fil](#Attachment)  
-   [Kodexempel](#Sample)  
-   [Kommentar](#Comment)  
-   [Filtrering](#Filtering)  
-   [Rubrik](#Header)  
-   [HTTP-begäran](#HTTPRequest)  
-   [HTTP-svar](#HTTPResponse)  
-   [Problemet](#Issue)  
-   [Åtgärd](#Operation)  
-   [Åtgärd-menyn](#Menu)  
-   [Menyalternativet för åtgärden](#MenuItem)  
-   [Växling](#Paging)  
-   [Parametern](#Parameter)  
-   [Produkten](#Product)  
-   [Leverantör](#Provider)  
-   [Representation](#Representation)  
-   [Prenumeration](#Subscription)  
-   [Prenumerationsöversikt](#SubscriptionSummary)  
-   [Information om användarkonto](#UserAccountInfo)  
-   [Användarinloggning](#UseSignIn)  
-   [Användarregistrering](#UserSignUp)  
  
##  <a name="API"></a> API: ET  
 Den `API` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|id|sträng|Resurs-ID. Identifierar API: et i den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `apis/{id}` där `{id}` är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|namn|sträng|Namnet på API: et. Får inte vara tomt. Maximal längd är 100 tecken.|  
|beskrivning|sträng|Beskrivning av API: et. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|ServiceUrl|sträng|Absolut URL: en för backend-tjänsten som implementerar det här API: et.|  
|sökväg|sträng|Relativ URL som unikt identifierar den här API: et och alla dess resurssökvägar i API Management-tjänstinstans. Det läggs till API-slutpunkt bas-URL har angetts under tjänstgenereringen för instansen för att skapa en offentlig URL för detta API.|  
|Protokoll|matris med tal|Beskriver på vilket protokoll åtgärder i detta API kan anropas. Tillåtna värden är `1 - http` och `2 - https`, eller båda.|  
|authenticationSettings|[Autentiseringsinställningar för auktorisering server](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Samling av autentiseringsinställningar som ingår i den här API: et.|  
|subscriptionKeyParameterNames|objekt|Egenskapen som kan användas för att ange anpassade namn för frågan och/eller rubrik parametrar som innehåller prenumerationsnyckeln. Om den här egenskapen finns måste det innehålla minst en av följande två egenskaper.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> API-sammanfattning  
 Den `API summary` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|id|sträng|Resurs-ID. Identifierar API: et i den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `apis/{id}` där `{id}` är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|namn|sträng|Namnet på API: et. Får inte vara tomt. Maximal längd är 100 tecken.|  
|beskrivning|sträng|Beskrivning av API: et. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
  
##  <a name="Application"></a> Programmet  
 Den `application` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Den unika identifieraren för programmet.|  
|Titel|sträng|Rubrik för programmet.|  
|Beskrivning|sträng|Beskrivning av programmet.|  
|URL|URI|URI för programmet.|  
|Version|sträng|Versionsinformation för programmet.|  
|Krav|sträng|En beskrivning av kraven för programmet.|  
|Status|nummer|Det aktuella tillståndet för programmet.<br /><br /> -0 - registrerad<br /><br /> -1 - har skickats<br /><br /> -2 - publicerade<br /><br /> -3 - avvisades<br /><br /> -4 - opublicerade|  
|RegistrationDate|DateTime|Datum och tid som programmet har registrerats.|  
|CategoryId|nummer|Kategorin för programmet (ekonomi, underhållning, osv.)|  
|DeveloperId|sträng|Den unika identifieraren för utvecklare som skickade programmet.|  
|Bilagor|Insamling av [bilaga](#Attachment) entiteter.|Eventuella bifogade filer för programmet, till exempel skärmbilder och ikoner.|  
|Ikon|[Bifogad fil](#Attachment)|Ikonen för den för programmet.|  
  
##  <a name="Attachment"></a> Bifogad fil  
 Den `attachment` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Unikt ID|sträng|Den unika identifieraren för den bifogade filen.|  
|URL|sträng|URL till resursen.|  
|Typ|sträng|Typ av bifogad fil.|  
|ContentType|sträng|Medietyp för den bifogade filen.|  
  
##  <a name="Sample"></a> Kodexempel  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|rubrik|sträng|Namnet på åtgärden.|  
|kodfragment|sträng|Den här egenskapen är inaktuell och ska inte användas.|  
|Pensel|sträng|Vilken kod syntaxfärgning mall som ska användas vid visning av kodexemplet. Tillåtna värden är `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, och `csharp`.|  
|mall|sträng|Namnet på den här koden exempelmallen.|  
|brödtext|sträng|En platshållare för den kod exempel delen av kodfragmentet.|  
|metod|sträng|HTTP-metod för åtgärden.|  
|Schema|sträng|Protokollet som ska användas för begäran igen.|  
|sökväg|sträng|Sökvägen för åtgärden.|  
|DocumentDB|sträng|Exempel på sökfråga sträng med definierade parametrar.|  
|värd|sträng|URL för API Management service-gatewayen för det API som innehåller den här åtgärden.|  
|Rubriker|Insamling av [rubrik](#Header) entiteter.|Huvuden för den här åtgärden.|  
|parameters|Insamling av [parametern](#Parameter) entiteter.|Parametrar som har definierats för den här åtgärden.|  
  
##  <a name="Comment"></a> Kommentar  
 Den `API` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|nummer|Id för kommentaren.|  
|CommentText|sträng|Kommentarens text. Kan omfatta HTML.|  
|DeveloperCompany|sträng|Företagsnamnet av utvecklaren.|  
|PostedOn|DateTime|Datum och tidpunkt kommentaren publicerades.|  
  
##  <a name="Issue"></a> Problemet  
 Den `issue` entitet har följande egenskaper.  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Den unika identifieraren för problemet.|  
|ApiID|sträng|Id för API: et för vilken det här problemet har rapporterats.|  
|Titel|sträng|Rubrik för problemet.|  
|Beskrivning|sträng|Beskrivning av problemet.|  
|SubscriptionDeveloperName|sträng|Förnamn i utvecklarens som rapporterade problemet.|  
|IssueState|sträng|Det aktuella tillståndet för problemet. Möjliga värden är föreslagen, öppnat, stängd.|  
|ReportedOn|DateTime|Datum och tid som problemet har rapporterats.|  
|Kommentarer|Insamling av [kommentar](#Comment) entiteter.|Kommentarer om det här problemet.|  
|Bilagor|Insamling av [bilaga](api-management-template-data-model-reference.md#Attachment) entiteter.|Eventuella bifogade filer på problemet.|  
|Tjänster|Insamling av [API](#API) entiteter.|API: erna som prenumererar av användaren som skapade problemet.|  
  
##  <a name="Filtering"></a> Filtrering  
 Den `filtering` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Mönster|sträng|Den aktuella söktermen; eller `null` om det finns inga sökord.|  
|Platshållare|sträng|Texten som visas i sökrutan när det finns inga sökord som angetts.|  
  
##  <a name="Header"></a> Rubrik  
 Det här avsnittet beskrivs de `parameter` representation.  
  
|Egenskap |Beskrivning|Typ|  
|--------------|-----------------|----------|  
|namn|sträng|Parameternamnet.|  
|beskrivning|sträng|Beskrivning av parameter.|  
|värde|sträng|Huvudets värde.|  
|typeName|sträng|Datatypen för huvudets värde.|  
|alternativ|sträng|Alternativ.|  
|obligatorisk|boolesk|Om rubriken är nödvändig.|  
|Skrivskyddad|boolesk|Om rubriken är skrivskyddad.|  
  
##  <a name="HTTPRequest"></a> HTTP-begäran  
 Det här avsnittet beskrivs de `request` representation.  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|beskrivning|sträng|Beskrivning för begäran för åtgärden.|  
|Rubriker|matris med [rubrik](#Header) entiteter.|Rubriker i begäran.|  
|parameters|matris med [Parameter](#Parameter)|Samling av parametrarna som åtgärden.|  
|garantier|matris med [Representation](#Representation)|Samling av åtgärden begäran garantier.|  
  
##  <a name="HTTPResponse"></a> HTTP-svar  
 Det här avsnittet beskrivs de `response` representation.  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|statusCode|positivt heltal|Åtgärden Svarets statuskod.|  
|beskrivning|sträng|Beskrivning av åtgärden svar.|  
|garantier|matris med [Representation](#Representation)|Samling av åtgärden svar garantier.|  
  
##  <a name="Operation"></a> Åtgärden  
 Den `operation` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|id|sträng|Resurs-ID. Identifierar åtgärden inom den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `apis/{aid}/operations/{id}` där `{aid}` är en API-identifierare och `{id}` är en identifierare för åtgärden. Den här egenskapen är skrivskyddad.|  
|namn|sträng|Åtgärdens namn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|beskrivning|sträng|Beskrivning av åtgärden. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|Schema|sträng|Beskriver på vilket protokoll åtgärder i detta API kan anropas. Tillåtna värden är `http`, `https`, eller båda `http` och `https`.|  
|uriTemplate|sträng|Relativ URL: en mall identifierar målresursen för den här åtgärden. Kan innehålla parametrar. Exempel: `customers/{cid}/orders/{oid}/?date={date}`|  
|värd|sträng|URL för API Management-gateway som är värd för API: et.|  
|HttpMethod|sträng|HTTP-åtgärdsmetod.|  
|begäran|[HTTP-begäran](#HTTPRequest)|En entitet som innehåller information om begäran.|  
|Svar|matris med [HTTP-svar](#HTTPResponse)|Matris med åtgärden [HTTP-svar](#HTTPResponse) entiteter.|  
  
##  <a name="Menu"></a> Åtgärd-menyn  
 Den `operation menu` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|apiId|sträng|Id för den aktuella API: T.|  
|CurrentOperationId|sträng|Id för den aktuella åtgärden.|  
|Åtgärd|sträng|Menytyp.|  
|MenuItems|Insamling av [åtgärden menyalternativet](#MenuItem) entiteter.|Åtgärder för den aktuella API: T.|  
  
##  <a name="MenuItem"></a> Menyalternativet för åtgärden  
 Den `operation menu item` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Id för åtgärden.|  
|Titel|sträng|Beskrivning av åtgärden.|  
|HttpMethod|sträng|Http-metod för åtgärden.|  
  
##  <a name="Paging"></a> Växling  
 Den `paging` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Sida|nummer|Det aktuella sidnumret.|  
|PageSize|nummer|Maximalt antal resultat som ska visas på en enda sida.|  
|TotalItemCount|nummer|Antal objekt för visning.|  
|ShowAll|boolesk|Om du vill visa alla resultat på en enda sida.|  
|PageCount|nummer|Antalet sidor i resultaten.|  
  
##  <a name="Parameter"></a> Parametern  
 Det här avsnittet beskrivs de `parameter` representation.  
  
|Egenskap |Beskrivning|Typ|  
|--------------|-----------------|----------|  
|namn|sträng|Parameternamnet.|  
|beskrivning|sträng|Beskrivning av parameter.|  
|värde|sträng|Parametervärdet.|  
|alternativ|strängmatris|Värden som definierats för frågeparametervärden.|  
|obligatorisk|boolesk|Anger om parametern är nödvändig eller inte.|  
|typ|nummer|Om den här parametern är en sökvägsparameter (1) eller en frågesträngsparameter (2).|  
|typeName|sträng|Parametertyp.|  
  
##  <a name="Product"></a> Produkten  
 Den `product` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Resurs-ID. Identifierar produkten inom den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `products/{pid}` där `{pid}` är en produktidentifierare. Den här egenskapen är skrivskyddad.|  
|Titel|sträng|Namnet på produkten. Får inte vara tomt. Maximal längd är 100 tecken.|  
|Beskrivning|sträng|Beskrivning av produkten. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|Villkor|sträng|Produkten villkor för användning. Utvecklare som försöker prenumerera på produkten kommer visas och krävs för att acceptera villkoren innan de kan slutföra prenumerationsprocessen.|  
|ProductState|nummer|Anger om produkten har publicerats eller inte. Publicerade produkter kan upptäckas av utvecklare på developer-portalen. Icke-publicerade produkter visas bara för administratörer.<br /><br /> Tillåtna värden för Produkttillstånd är:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|boolesk|Anger om en användare kan ha flera prenumerationer för den här produkten på samma gång.|  
|MultipleSubscriptionsCount|nummer|Maximalt antal prenumerationer för produkten som en användare får ha på samma gång.|  
  
##  <a name="Provider"></a> Providern  
 Den `provider` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Egenskaper|sträng-ordlista|Egenskaper för den här autentiseringsprovider.|  
|AuthenticationType|sträng|Providertyp. (Azure Active Directory, Facebook-inloggning, Google-konto, Account, Twitter).|  
|Undertext|sträng|Visningsnamnet för providern.|  
  
##  <a name="Representation"></a> Representation  
 Det här avsnittet beskrivs en `representation`.  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|contentType|sträng|Anger en registrerad eller anpassade innehållstyp för den här representation, till exempel `application/xml`.|  
|Exemplet|sträng|Ett exempel på en representation.|  
  
##  <a name="Subscription"></a> Prenumeration  
 Den `subscription` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Resurs-ID. Unikt ID för prenumeration inom den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `subscriptions/{sid}` där `{sid}` är ett prenumerations-ID. Den här egenskapen är skrivskyddad.|  
|Produkt-ID|sträng|Resurs-ID för produkt för prenumererar på produkten. Värdet är en giltig relativ URL i formatet `products/{pid}` där `{pid}` är en produktidentifierare.|  
|ProductTitle|sträng|Namnet på produkten. Får inte vara tomt. Maximal längd är 100 tecken.|  
|ProductDescription|sträng|Beskrivning av produkten. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|ProductDetailsUrl|sträng|Relativ URL till produktinformation.|  
|state|sträng|Tillståndet för prenumerationen. Möjliga tillstånd är:<br /><br /> - `0 - suspended` – prenumerationen blockeras och prenumeranten kan inte anropa alla API: er i produkten.<br /><br /> - `1 - active` – prenumerationen är aktiv.<br /><br /> - `2 - expired` – prenumerationen har nått dess förfallodatum och inaktiverades.<br /><br /> - `3 - submitted` – prenumerationsbegäran har gjorts av utvecklaren, men har ännu inte har godkänts eller avvisats.<br /><br /> - `4 - rejected` – prenumerationsbegäran har nekats av en administratör.<br /><br /> - `5 - cancelled` – prenumerationen har avbrutits av utvecklare eller administratör.|  
|DisplayName|sträng|Visningsnamnet för prenumerationen.|  
|CreatedDate|Datum/tid|Det datum då prenumerationen skapades, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|boolesk|Om prenumerationen kan avbrytas av den aktuella användaren.|  
|IsAwaitingApproval|boolesk|Om prenumerationen väntar på godkännande.|  
|Startdatum|Datum/tid|Startdatum för prenumerationen, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|Datum/tid|Utgångsdatumet för prenumerationen, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|NotificationDate|Datum/tid|Meddelande-datum för prenumerationen i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|primaryKey|sträng|Primär prenumerationsnyckeln. Maximal längd är 256 tecken.|  
|sekundär nyckel|sträng|Sekundär prenumerationsnyckeln. Maximal längd är 256 tecken.|  
|CanBeRenewed|boolesk|Om prenumerationen kan förnyas av den aktuella användaren.|  
|HasExpired|boolesk|Om prenumerationen har gått ut.|  
|IsRejected|boolesk|Om prenumerationsbegäran nekades.|  
|cancelUrl|sträng|Relativ Url som du säga upp prenumerationen.|  
|RenewUrl|sträng|Relativ Url att förnya prenumerationen.|  
  
##  <a name="SubscriptionSummary"></a> Prenumerationsöversikt  
 Den `subscription summary` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Id|sträng|Resurs-ID. Unikt ID för prenumeration inom den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `subscriptions/{sid}` där `{sid}` är ett prenumerations-ID. Den här egenskapen är skrivskyddad.|  
|DisplayName|sträng|Visningsnamnet för prenumerationen|  
  
##  <a name="UserAccountInfo"></a> Information om användarkonto  
 Den `user account info` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|FirstName|sträng|Förnamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|LastName|sträng|Efternamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|E-post|sträng|E-postadress. Får inte vara tomt och måste vara unikt inom tjänstinstansen. Maximal längd är 254 tecken.|  
|Lösenord|sträng|Lösenordet för användarkontot.|  
|NameIdentifier|sträng|Konto-ID detsamma som användarens e-postadress.|  
|ProviderName|sträng|Providernamn för autentisering.|  
|IsBasicAccount|boolesk|TRUE om det här kontot har registrerats med hjälp av e-post och lösenord. FALSKT om konton som har registrerats med hjälp av en provider.|  
  
##  <a name="UseSignIn"></a> Användarinloggning  
 Den `user sign in` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|E-post|sträng|E-postadress. Får inte vara tomt och måste vara unikt inom tjänstinstansen. Maximal längd är 254 tecken.|  
|Lösenord|sträng|Lösenordet för användarkontot.|  
|ReturnUrl|sträng|Webbadressen till sidan där användaren har klickat på Logga in.|  
|RememberMe|boolesk|Om du vill spara den aktuella användarens information.|  
|RegistrationEnabled|boolesk|Om registrering är aktiverat.|  
|DelegationEnabled|boolesk|Om delegerade inloggning är aktiverat.|  
|DelegationUrl|sträng|Delegerad inloggningen url, om aktiverad.|  
|SsoSignUpUrl|sträng|URL: en enkel inloggning för användaren, om sådan finns.|  
|AuxServiceUrl|sträng|Om den aktuella användaren är administratör, är detta en länk till tjänstinstansen i Azure-portalen.|  
|Leverantörer|Insamling av [Provider](#Provider) entiteter|Autentiseringsproviders för den här användaren.|  
|UserRegistrationTerms|sträng|Villkor som en användare måste godkänna innan du loggar in.|  
|UserRegistrationTermsEnabled|boolesk|Om villkoren är aktiverade.|  
  
##  <a name="UserSignUp"></a> Användarregistrering  
 Den `user sign up` entitet har följande egenskaper:  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|LösenordBekräfta|boolesk|Värdet som används av den [registrering](api-management-page-controls.md#sign-up)registrering kontroll.|  
|Lösenord|sträng|Lösenordet för användarkontot.|  
|PasswordVerdictLevel|nummer|Värdet som används av den [registrering](api-management-page-controls.md#sign-up)registrering kontroll.|  
|UserRegistrationTerms|sträng|Villkor som en användare måste godkänna innan du loggar in.|  
|UserRegistrationTermsOptions|nummer|Värdet som används av den [registrering](api-management-page-controls.md#sign-up)registrering kontroll.|  
|ConsentAccepted|boolesk|Värdet som används av den [registrering](api-management-page-controls.md#sign-up)registrering kontroll.|  
|E-post|sträng|E-postadress. Får inte vara tomt och måste vara unikt inom tjänstinstansen. Maximal längd är 254 tecken.|  
|FirstName|sträng|Förnamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|LastName|sträng|Efternamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|UserData|sträng|Värdet som används av den [registrering](api-management-page-controls.md#sign-up) kontroll.|  
|NameIdentifier|sträng|Värdet som används av den [registrering](api-management-page-controls.md#sign-up)registrering kontroll.|  
|ProviderName|sträng|Providernamn för autentisering.|

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](api-management-developer-portal-templates.md).
