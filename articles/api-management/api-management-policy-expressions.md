---
title: Azure API Management-principuttrycken | Microsoft Docs
description: "Lär dig mer om principen uttryck i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: a5bcd03e71a69928fa1e02a5286801c4933d17ef
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="api-management-policy-expressions"></a>API Management principuttrycken
Den här artikeln beskrivs princip uttryck syntax är C# 6.0. Varje uttryck har åtkomst till den angivna implicit [kontexten](api-management-policy-expressions.md#ContextVariables) variabel och ett tillåtna [delmängd](api-management-policy-expressions.md#CLRTypes) av .NET Framework-typer.  

Mer information:

- Lär dig mer om att leverera sammanhangsinformation till backend-tjänst. Använd den [ange frågesträngparametern](api-management-transformation-policies.md#SetQueryStringParameter) och [ange HTTP-huvudet](api-management-transformation-policies.md#SetHTTPheader) principer för att ange den här informationen.
- Se hur du använder den [Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) för att bevilja åtkomst till åtgärder före baserat på token anspråk.   
- Se hur du använder en [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) spåra för att se hur principer utvärderas och resultatet av dessa utvärderingar.  
- Se hur du använder med den [hämta från cache](api-management-caching-policies.md#GetFromCache) och [Store cacheminne](api-management-caching-policies.md#StoreToCache) principer för att konfigurera cachelagring av API Management-svar. Ange hur länge som matchar svar cachelagring av serverdelstjänsten som anges av tjänsten säkerhetskopierade `Cache-Control` direktiv.  
- Se hur du utför innehållsfiltrering. Ta bort dataelement från svar togs emot från en serverdel med hjälp av den [Åtkomstkontrollflödet](api-management-advanced-policies.md#choose) och [konfigurera brödtext](api-management-transformation-policies.md#SetBody) principer. 
- För att ladda ned principen instruktioner finns i [api-hantering-exempel principer](https://github.com/Azure/api-management-samples/tree/master/policies) github-lagringsplatsen.  
  
  
##  <a name="Syntax"></a> Syntax  
 Uttryck för enskild instruktion innefattas i `@(expression)`, där `expression` är en giltig C#-uttrycket.  
  
 Flera instruktioner uttryck innefattas i `@{expression}`. Alla kodsökvägar inom flera instruktioner uttryck måste avslutas med en `return` instruktion.  
  
##  <a name="PolicyExpressionsExamples"></a> Exempel  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a> Användning  
 Uttryck kan användas som attributvärden eller textvärden i API-hantering [principer](api-management-policies.md) (såvida principreferens anger något annat).  
  
> [!IMPORTANT]
>  När du använder principuttrycken har endast begränsad verifiering av principen uttryck när principen har definierats. Uttryck utförs av gateway vid körning, eventuella undantag som genereras av principen uttryck resultatet i ett körningsfel.  
  
##  <a name="CLRTypes"></a> .NET framework-typer som är tillåtna i uttryck för principen  
 I följande tabell visas .NET Framework-typer och deras medlemmar som tillåts i princip uttryck.  
  
|CLR-typ|Metoder som stöds|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|Alla metoder som stöds|  
|Newtonsoft.Json.Linq.JArray|Alla metoder som stöds|  
|Newtonsoft.Json.Linq.JConstructor|Alla metoder som stöds|  
|Newtonsoft.Json.Linq.JContainer|Alla metoder som stöds|  
|Newtonsoft.Json.Linq.JObject|Alla metoder som stöds|  
|Newtonsoft.Json.Linq.JProperty|Alla metoder som stöds|  
|Newtonsoft.Json.Linq.JRaw|Alla metoder som stöds|  
|Newtonsoft.Json.Linq.JToken|Alla metoder som stöds|  
|Newtonsoft.Json.Linq.JTokenType|Alla metoder som stöds|  
|Newtonsoft.Json.Linq.JValue|Alla metoder som stöds|  
|System.Collections.Generic.IReadOnlyCollection<T\>|Alla|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|Alla|  
|System.Collections.Generic.ISet<TKey, TValue>|Alla|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Nyckel/värde|  
|System.Collections.Generic.List<TKey, TValue>|Alla|  
|System.Collections.Generic.Queue<TKey, TValue>|Alla|  
|System.Collections.Generic.Stack<TKey, TValue>|Alla|  
|System.Convert|Alla|  
|System.DateTime|Alla|  
|System.DateTimeKind|UTC|  
|System.DateTimeOffset|Alla|  
|System.Decimal|Alla|  
|System.Double|Alla|  
|System.Guid|Alla|  
|System.IEnumerable<T\>|Alla|  
|System.IEnumerator<T\>|Alla|  
|System.Int16|Alla|  
|System.Int32|Alla|  
|System.Int64|Alla|  
|System.Linq.Enumerable<T\>|Alla metoder som stöds|  
|System.Math|Alla|  
|System.MidpointRounding|Alla|  
|System.Nullable<T\>|Alla|  
|System.Random|Alla|  
|System.SByte|Alla|  
|System.Security.Cryptography. HMACSHA384|Alla|  
|System.Security.Cryptography. HMACSHA512|Alla|  
|System.Security.Cryptography.HashAlgorithm|Alla|  
|System.Security.Cryptography.HMAC|Alla|  
|System.Security.Cryptography.HMACMD5|Alla|  
|System.Security.Cryptography.HMACSHA1|Alla|  
|System.Security.Cryptography.HMACSHA256|Alla|  
|System.Security.Cryptography.KeyedHashAlgorithm|Alla|  
|System.Security.Cryptography.MD5|Alla|  
|System.Security.Cryptography.RNGCryptoServiceProvider|Alla|  
|System.Security.Cryptography.SHA1|Alla|  
|System.Security.Cryptography.SHA1Managed|Alla|  
|System.Security.Cryptography.SHA256|Alla|  
|System.Security.Cryptography.SHA256Managed|Alla|  
|System.Security.Cryptography.SHA384|Alla|  
|System.Security.Cryptography.SHA384Managed|Alla|  
|System.Security.Cryptography.SHA512|Alla|  
|System.Security.Cryptography.SHA512Managed|Alla|  
|System.Single|Alla|  
|System.String|Alla|  
|System.StringSplitOptions|Alla|  
|System.Text.Encoding|Alla|  
|System.Text.RegularExpressions.Capture|Index, Length, Value|  
|System.Text.RegularExpressions.CaptureCollection|Antal objekt|  
|System.Text.RegularExpressions.Group|Insamlingar, lyckades|  
|System.Text.RegularExpressions.GroupCollection|Antal objekt|  
|System.Text.RegularExpressions.Match|Tom, grupper, resultat|  
|System.Text.RegularExpressions.Regex|(Konstruktor) IsMatch, matchning, matchar, Ersätt|  
|System.Text.RegularExpressions.RegexOptions|Compiled, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline|  
|System.TimeSpan|Alla|  
|System.Tuple|Alla|  
|System.UInt16|Alla|  
|System.UInt32|Alla|  
|System.UInt64|Alla|  
|System.Uri|Alla|  
|System.Xml.Linq.Extensions|Alla metoder som stöds|  
|System.Xml.Linq.XAttribute|Alla metoder som stöds|  
|System.Xml.Linq.XCData|Alla metoder som stöds|  
|System.Xml.Linq.XComment|Alla metoder som stöds|  
|System.Xml.Linq.XContainer|Alla metoder som stöds|  
|System.Xml.Linq.XDeclaration|Alla metoder som stöds|  
|System.Xml.Linq.XDocument|Alla metoder som stöds|  
|System.Xml.Linq.XDocumentType|Alla metoder som stöds|  
|System.Xml.Linq.XElement|Alla metoder som stöds|  
|System.Xml.Linq.XName|Alla metoder som stöds|  
|System.Xml.Linq.XNamespace|Alla metoder som stöds|  
|System.Xml.Linq.XNode|Alla metoder som stöds|  
|System.Xml.Linq.XNodeDocumentOrderComparer|Alla metoder som stöds|  
|System.Xml.Linq.XNodeEqualityComparer|Alla metoder som stöds|  
|System.Xml.Linq.XObject|Alla metoder som stöds|  
|System.Xml.Linq.XProcessingInstruction|Alla metoder som stöds|  
|System.Xml.Linq.XText|Alla metoder som stöds|  
|System.Xml.XmlNodeType|Alla|  
  
##  <a name="ContextVariables"></a> Kontexten variabel  
 En variabel med namnet `context` implicit finns i varje princip [uttryck](api-management-policy-expressions.md#Syntax). Dess medlemmar innehåller information som är relevanta för den `\request`. Alla de `context` medlemmar är skrivskyddad.  
  
|Kontexten variabel|Tillåtna metoder, egenskaper och parametervärden|  
|----------------------|-------------------------------------------------------|  
|Kontexten|Api: IApi<br /><br /> Distribution<br /><br /> Förflutna: TimeSpan - tidsintervallet mellan värdet för tidsstämpel och aktuell tid<br /><br /> LastError<br /><br /> Åtgärd<br /><br /> Produkt<br /><br /> Förfrågan<br /><br /> Begärande-ID: Guid - identifierare för unik begäran<br /><br /> Svar<br /><br /> Prenumeration<br /><br /> Tidstämpel: Datum/tid - tidpunkt när en förfrågan togs emot<br /><br /> Spårning: bool - anger om spårning är aktiverad eller inaktiverad <br /><br /> Användare<br /><br /> Variabler: IReadOnlyDictionary < sträng, objekt ><br /><br /> void Trace(message: string)|  
|context.Api|ID: sträng<br /><br /> IsRevisionCurrent: bool<br /><br />  Namn: sträng<br /><br /> Sökväg: sträng<br /><br /> Revision: sträng<br /><br /> ServiceUrl: IUrl<br /><br /> Version: sträng |  
|context.Deployment|Region: sträng<br /><br /> Namn på tjänst: sträng<br /><br /> Certifikat: IReadOnlyDictionary < sträng, X509Certificate2 >|  
|context.LastError|Källa: sträng<br /><br /> Orsak: sträng<br /><br /> Meddelande: sträng<br /><br /> Omfång: sträng<br /><br /> Avsnittet: sträng<br /><br /> Sökväg: sträng<br /><br /> PolicyId: sträng<br /><br /> Mer information om kontexten. LastError, se [felhantering](api-management-error-handling-policies.md).|  
|context.Operation|ID: sträng<br /><br /> Metod: sträng<br /><br /> Namn: sträng<br /><br /> UrlTemplate: sträng|  
|context.Product|API: er: IEnumerable < IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> ID: sträng<br /><br /> Namn: sträng<br /><br /> Tillstånd: enum ProductState {NotPublished, publicerad}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|  
|context.Request|Brödtext: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Huvuden: IReadOnlyDictionary < string, string [] ><br /><br /> IP-adress: sträng<br /><br /> MatchedParameters: IReadOnlyDictionary < sträng, sträng ><br /><br /> Metod: sträng<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|strängkontext. Request.Headers.GetValueOrDefault (headerName: sträng, defaultValue: sträng)|headerName: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar CSV-begäran huvudvärden eller `defaultValue` om sidhuvudet inte hittas.|  
|kontexten. Svar|Brödtext: IMessageBody<br /><br /> Huvuden: IReadOnlyDictionary < string, string [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: sträng|  
|strängkontext. Response.Headers.GetValueOrDefault (headerName: sträng, defaultValue: sträng)|headerName: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar kommaavgränsade svar huvudvärden eller `defaultValue` om sidhuvudet inte hittas.|  
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> ID: sträng<br /><br /> Nyckel: sträng<br /><br /> Namn: sträng<br /><br /> PrimaryKey: sträng<br /><br /> Sekundär nyckel: sträng<br /><br /> StartDate: DateTime?|  
|context.User|E-post: sträng<br /><br /> Förnamn: sträng<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> ID: sträng<br /><br /> Identiteter: IEnumerable < IUserIdentity\><br /><br /> Efternamn: sträng<br /><br /> Obs: sträng<br /><br /> RegistrationDate: DateTime|  
|IApi|ID: sträng<br /><br /> Namn: sträng<br /><br /> Sökväg: sträng<br /><br /> Protokoll: IEnumerable < sträng\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|ID: sträng<br /><br /> Namn: sträng|  
|IMessageBody|Som < T\>(preserveContent: bool = false): där T: sträng, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Den `context.Request.Body.As<T>` och `context.Response.Body.As<T>` metoder som används för att läsa en begäran och svar meddelandetexten i en viss typ av `T`. Metoden använder den ursprungliga brödtext meddelandeströmmen som standard och gör dem tillgängliga efter att den returnerar. Du undviker som genom att metoden fungerar på en kopia av brödtextströmmen, ange den `preserveContent` parameter till `true`. Gå [här](api-management-transformation-policies.md#SetBody) att se ett exempel.|  
|IUrl|Värden: sträng<br /><br /> Sökväg: sträng<br /><br /> Port: int<br /><br /> Fråga: IReadOnlyDictionary < string, string [] ><br /><br /> QueryString: sträng<br /><br /> Schema: sträng|  
|IUserIdentity|ID: sträng<br /><br /> Providern: sträng|  
|ISubscriptionKeyParameterNames|Rubrik: sträng<br /><br /> Fråga: sträng|  
|sträng IUrl.Query.GetValueOrDefault (queryParameterName: sträng, defaultValue: sträng)|queryParameterName: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar parametervärden för CSV-fråga eller `defaultValue` om parametern inte hittas.|  
|T-kontext. Variables.GetValueOrDefault < T\>(variabelnamn: sträng, defaultValue: T)|Variabelnamn: sträng<br /><br /> Standardvärde: T<br /><br /> Returnerar värdet på variabeln typkonverteras till typen `T` eller `defaultValue` om variabeln inte hittas.<br /><br /> Den här metoden genereras ett undantag om den angivna typen inte matchar den faktiska typen av returnerade variabeln.|  
|BasicAuthCredentials AsBasic(input: this string)|indata: sträng<br /><br /> Om parametern innehåller ett giltigt grundläggande HTTP-autentisering auktorisering begäran sidhuvud värde, metoden returnerar ett objekt av typen `BasicAuthCredentials`; annars metoden returnerar null.|  
|bool TryParseBasic (indata: den här strängen, resultat: ut BasicAuthCredentials)|indata: sträng<br /><br /> resultatet: ut BasicAuthCredentials<br /><br /> Om parametern innehåller ett giltigt grundläggande HTTP-autentisering auktoriseringsvärde i resurshuvudet returnerar-metoden `true` och resultatet parametern innehåller ett värde av typen `BasicAuthCredentials`; annars returnerar-metoden `false`.|  
|BasicAuthCredentials|Lösenord: sträng<br /><br /> Användar-ID: sträng|  
|Jwt AsJwt(input: this string)|indata: sträng<br /><br /> Om parametern innehåller ett giltigt JWT-token värde, metoden returnerar ett objekt av typen `Jwt`; annars returnerar-metoden `null`.|  
|bool TryParseJwt (indata: den här strängen, resultat: ut Jwt)|indata: sträng<br /><br /> resultatet: ut Jwt<br /><br /> Om parametern innehåller ett giltigt JWT-token värde, metoden returnerar `true` och resultatet parametern innehåller ett värde av typen `Jwt`; annars returnerar-metoden `false`.|  
|Jwt|Algoritm: sträng<br /><br /> Målgrupp: IEnumerable < sträng\><br /><br /> Anspråk: IReadOnlyDictionary < string, string [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> ID: sträng<br /><br /> Utfärdare: sträng<br /><br /> Inte före: DateTime?<br /><br /> Ämne: sträng<br /><br /> Typ: sträng|  
|sträng Jwt.Claims.GetValueOrDefault (claimName: sträng, defaultValue: sträng)|claimName: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar kommaavgränsad anspråksvärden eller `defaultValue` om sidhuvudet inte hittas.|
|byte [] kryptera (indata: den här byte [], alg: sträng, nyckel: byte [], iv:byte[])|indata - oformaterad text som ska krypteras<br /><br />ALG - namnet på en symmetrisk kryptografisk algoritm<br /><br />nyckel - krypteringsnyckeln<br /><br />IV - Initieringsvektorn<br /><br />Returnerar krypterad klartext.|
|byte [] kryptera (indata: den här byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|indata - oformaterad text som ska krypteras<br /><br />ALG - krypteringsalgoritm<br /><br />Returnerar krypterad klartext.|
|byte [] kryptera (indata: den här byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, nyckel: byte [], iv:byte[])|indata - oformaterad text som ska krypteras<br /><br />ALG - krypteringsalgoritm<br /><br />nyckel - krypteringsnyckeln<br /><br />IV - Initieringsvektorn<br /><br />Returnerar krypterad klartext.|
|byte [] dekryptera (indata: den här byte [], alg: sträng, nyckel: byte [], iv:byte[])|indata - kod text som ska dekrypteras<br /><br />ALG - namnet på en symmetrisk kryptografisk algoritm<br /><br />nyckel - krypteringsnyckeln<br /><br />IV - Initieringsvektorn<br /><br />Returnerar oformaterad text.|
|byte [] dekryptera (indata: den här byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|indata - kod text som ska dekrypteras<br /><br />ALG - krypteringsalgoritm<br /><br />Returnerar oformaterad text.|
|byte [] dekryptera (indata: den här byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, nyckel: byte [], iv:byte[])|indata - indata - kod text som ska dekrypteras<br /><br />ALG - krypteringsalgoritm<br /><br />nyckel - krypteringsnyckeln<br /><br />IV - Initieringsvektorn<br /><br />Returnerar oformaterad text.|


## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:

+ [Principer för i API-hantering](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   
