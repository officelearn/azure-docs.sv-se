---
title: Azure API Management-principuttryck | Microsoft Docs
description: Läs mer om principuttryck i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 90b2dfdbec0d6dc81a05b845832fda92fe36d98c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656599"
---
# <a name="api-management-policy-expressions"></a>Principuttryck för API Management
Den här artikeln beskriver principuttryck syntaxen är C# 7. Varje uttryck har åtkomst till den angivna implicit [kontext](api-management-policy-expressions.md#ContextVariables) variabeln och tillåtet [delmängd](api-management-policy-expressions.md#CLRTypes) av .NET Framework-typer.

Mer information:

- Se hur du anger kontextinformation till serverdelstjänsten. Använd den [ange parametern för frågesträngen](api-management-transformation-policies.md#SetQueryStringParameter) och [ange HTTP-huvud](api-management-transformation-policies.md#SetHTTPheader) principer anger den här informationen.
- Se hur du använder den [verifiera JWT](api-management-access-restriction-policies.md#ValidateJWT) principen för att förauktorisera åtkomst till åtgärder baserat på tokenanspråken.
- Se hur du använder en [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) spåra för att se hur principer utvärderas och resultatet av dessa utvärderingar.
- Se hur du använder uttryck med den [kom från cachen](api-management-caching-policies.md#GetFromCache) och [Store till cache](api-management-caching-policies.md#StoreToCache) principer för att konfigurera cachelagring av svar i API Management. Ange hur länge som matchar svar cachelagring av backend-tjänsten som anges av tjänsten säkerhetskopierade `Cache-Control` direktiv.
- Se hur du utför innehållsfiltrering. Ta bort dataelement från svaret från en serverdel med hjälp av den [Åtkomstkontrollflödet](api-management-advanced-policies.md#choose) och [ange brödtext](api-management-transformation-policies.md#SetBody) principer.
- För att ladda ned principrapporter den [api--samples/hanteringsprinciper](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub-lagringsplatsen.


## <a name="Syntax"></a> Syntax
Enkel uttryck är inom `@(expression)`, där `expression` är en korrekt formaterad C# uttrycksinstruktion.

Flera uttryck är inom `@{expression}`. Alla kodsökvägar inom flera uttryck måste sluta med en `return` instruktionen.

## <a name="PolicyExpressionsExamples"></a> Exempel

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

## <a name="PolicyExpressionsUsage"></a>Användning
Uttryck kan användas som attributvärden eller textvärden i alla API Management [principer](api-management-policies.md) (såvida inte principreferens anger något annat).

> [!IMPORTANT]
> När du använder principuttryck finns endast begränsad kontroll av principuttryck när principen har definierats. Uttryck utförs av gateway vid körning, eventuella undantag som genereras av principen uttryck resulterar i ett körningsfel.

## <a name="CLRTypes"></a> .NET framework-typer tillåts i principuttryck
I följande tabell visas de .NET Framework-typerna och deras medlemmar som tillåts i principuttryck.

|Typ|Stöds medlemmar|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Alla|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Alla|
|Newtonsoft.Json.Linq.JArray|Alla|
|Newtonsoft.Json.Linq.JConstructor|Alla|
|Newtonsoft.Json.Linq.JContainer|Alla|
|Newtonsoft.Json.Linq.JObject|Alla|
|Newtonsoft.Json.Linq.JProperty|Alla|
|Newtonsoft.Json.Linq.JRaw|Alla|
|Newtonsoft.Json.Linq.JToken|Alla|
|Newtonsoft.Json.Linq.JTokenType|Alla|
|Newtonsoft.Json.Linq.JValue|Alla|
|System.Array|Alla|
|System.BitConverter|Alla|
|System.Boolean|Alla|
|System.Byte|Alla|
|System.Char|Alla|
|System.Collections.Generic.Dictionary<TKey, TValue>|Alla|
|System.Collections.Generic.HashSet<T>|Alla|
|System.Collections.Generic.ICollection<T>|Alla|
|System.Collections.Generic.IDictionary<TKey, TValue>|Alla|
|System.Collections.Generic.IEnumerable<T>|Alla|
|System.Collections.Generic.IEnumerator<T>|Alla|
|System.Collections.Generic.IList<T>|Alla|
|System.Collections.Generic.IReadOnlyCollection<T>|Alla|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Alla|
|System.Collections.Generic.ISet<T>|Alla|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Alla|
|System.Collections.Generic.List<T>|Alla|
|System.Collections.Generic.Queue<T>|Alla|
|System.Collections.Generic.Stack<T>|Alla|
|System.Convert|Alla|
|System.DateTime|(Konstruktor), lägger till, AddDays AddHours, AddMilliseconds, AddMinutes, AddMonths, Lägg_till_sekunder, AddTicks, AddYears, datum, dag, DayOfWeek, DayOfYear, DaysInMonth, timme, IsDaylightSavingTime, IsLeapYear, MaxValue, millisekunder, minut, MinValue, månad, nu , Parsa, subtrahera andra Ticken, TimeOfDay, idag, ToString, UtcNow, år|
|System.DateTimeKind|UTC|
|System.DateTimeOffset|Alla|
|System.Decimal|Alla|
|System.Double|Alla|
|System.Exception|Alla|
|System.Guid|Alla|
|System.Int16|Alla|
|System.Int32|Alla|
|System.Int64|Alla|
|System.IO.StringReader|Alla|
|System.IO.StringWriter|Alla|
|System.Linq.Enumerable|Alla|
|System.Math|Alla|
|System.MidpointRounding|Alla|
|System.Net.WebUtility|Alla|
|System.Nullable|Alla|
|System.Random|Alla|
|System.SByte|Alla|
|System.Security.Cryptography.AsymmetricAlgorithm|Alla|
|System.Security.Cryptography.CipherMode|Alla|
|System.Security.Cryptography.HashAlgorithm|Alla|
|System.Security.Cryptography.HashAlgorithmName|Alla|
|System.Security.Cryptography.HMAC|Alla|
|System.Security.Cryptography.HMACMD5|Alla|
|System.Security.Cryptography.HMACSHA1|Alla|
|System.Security.Cryptography.HMACSHA256|Alla|
|System.Security.Cryptography.HMACSHA384|Alla|
|System.Security.Cryptography.HMACSHA512|Alla|
|System.Security.Cryptography.KeyedHashAlgorithm|Alla|
|System.Security.Cryptography.MD5|Alla|
|System.Security.Cryptography.Oid|Alla|
|System.Security.Cryptography.PaddingMode|Alla|
|System.Security.Cryptography.RNGCryptoServiceProvider|Alla|
|System.Security.Cryptography.RSA|Alla|
|System.Security.Cryptography.RSAEncryptionPadding|Alla|
|System.Security.Cryptography.RSASignaturePadding|Alla|
|System.Security.Cryptography.SHA1|Alla|
|System.Security.Cryptography.SHA1Managed|Alla|
|System.Security.Cryptography.SHA256|Alla|
|System.Security.Cryptography.SHA256Managed|Alla|
|System.Security.Cryptography.SHA384|Alla|
|System.Security.Cryptography.SHA384Managed|Alla|
|System.Security.Cryptography.SHA512|Alla|
|System.Security.Cryptography.SHA512Managed|Alla|
|System.Security.Cryptography.SymmetricAlgorithm|Alla|
|System.Security.Cryptography.X509Certificates.PublicKey|Alla|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Alla|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Namn|
|System.Security.Cryptography.X509Certificates.X509Certificate|Alla|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Alla|
|System.Security.Cryptography.X509Certificates.X509ContentType|Alla|
|System.Security.Cryptography.X509Certificates.X509NameType|Alla|
|System.Single|Alla|
|System.String|Alla|
|System.StringComparer|Alla|
|System.StringComparison|Alla|
|System.StringSplitOptions|Alla|
|System.Text.Encoding|Alla|
|System.Text.RegularExpressions.Capture|Index, längd, värde|
|System.Text.RegularExpressions.CaptureCollection|Antal objekt|
|System.Text.RegularExpressions.Group|Insamlingar, lyckades|
|System.Text.RegularExpressions.GroupCollection|Antal objekt|
|System.Text.RegularExpressions.Match|Tom, grupper, resultat|
|System.Text.RegularExpressions.Regex|(Constructor), IsMatch, Match, Matches, Replace, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Alla|
|System.Text.StringBuilder|Alla|
|System.TimeSpan|Alla|
|System.TimeZone|Alla|
|System.TimeZoneInfo.AdjustmentRule|Alla|
|System.TimeZoneInfo.TransitionTime|Alla|
|System.TimeZoneInfo|Alla|
|System.Tuple|Alla|
|System.UInt16|Alla|
|System.UInt32|Alla|
|System.UInt64|Alla|
|System.Uri|Alla|
|System.UriPartial|Alla|
|System.Xml.Linq.Extensions|Alla|
|System.Xml.Linq.XAttribute|Alla|
|System.Xml.Linq.XCData|Alla|
|System.Xml.Linq.XComment|Alla|
|System.Xml.Linq.XContainer|Alla|
|System.Xml.Linq.XDeclaration|Alla|
|System.Xml.Linq.XDocument|Alla, utom: Läsa in|
|System.Xml.Linq.XDocumentType|Alla|
|System.Xml.Linq.XElement|Alla|
|System.Xml.Linq.XName|Alla|
|System.Xml.Linq.XNamespace|Alla|
|System.Xml.Linq.XNode|Alla|
|System.Xml.Linq.XNodeDocumentOrderComparer|Alla|
|System.Xml.Linq.XNodeEqualityComparer|Alla|
|System.Xml.Linq.XObject|Alla|
|System.Xml.Linq.XProcessingInstruction|Alla|
|System.Xml.Linq.XText|Alla|
|System.Xml.XmlNodeType|Alla|

## <a name="ContextVariables"></a> Sammanhangsvariabel
En variabel med namnet `context` finns implicit i varje princip [uttryck](api-management-policy-expressions.md#Syntax). Medlemmarna innehåller information som är relevanta för den `\request`. Alla de `context` medlemmar är skrivskyddade.

|Sammanhangsvariabel|Tillåtna metoder, egenskaper och parametervärden|
|----------------------|-------------------------------------------------------|
|Kontext|Api: IApi<br /><br /> Distribution<br /><br /> Förfluten tid: TimeSpan - tidsintervallet mellan värdet för tidsstämpeln och aktuell tid<br /><br /> LastError<br /><br /> Åtgärd<br /><br /> Product<br /><br /> Förfrågan<br /><br /> RequestId: GUID - identifierare för unika begäran<br /><br /> Svar<br /><br /> Prenumeration<br /><br /> Tidsstämpel: DateTime - tidpunkt när en förfrågan togs emot<br /><br /> Spårning: bool - anger om spårning har aktiverats eller inaktiverats <br /><br /> Användare<br /><br /> Variabler: IReadOnlyDictionary < string, object ><br /><br /> Annullera Trace(message: string)|
|context.Api|ID: sträng<br /><br /> IsCurrentRevision: bool<br /><br />  Namn: sträng<br /><br /> Sökväg: sträng<br /><br /> Revision: sträng<br /><br /> ServiceUrl: IUrl<br /><br /> Version: sträng |
|context.Deployment|Region: sträng<br /><br /> Tjänstnamn: sträng<br /><br /> Certifikat: IReadOnlyDictionary<string, X509Certificate2>|
|context.LastError|Källa: sträng<br /><br /> Orsak: sträng<br /><br /> Meddelande: sträng<br /><br /> Omfång: sträng<br /><br /> Avsnittet: sträng<br /><br /> Sökväg: sträng<br /><br /> PolicyId: sträng<br /><br /> Mer information om kontext. LastError, se [felhantering](api-management-error-handling-policies.md).|
|context.Operation|ID: sträng<br /><br /> Metod: sträng<br /><br /> Namn: sträng<br /><br /> UrlTemplate: string|
|context.Product|API: er: IEnumerable < IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Grupper: IEnumerable < IGroup\><br /><br /> ID: sträng<br /><br /> Namn: sträng<br /><br /> Tillstånd: enum ProductState {NotPublished, publicerade}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|context.Request|Brödtext: IMessageBody<br /><br /> Certifikat: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Rubriker: IReadOnlyDictionary < string, string [] ><br /><br /> IP-adress: sträng<br /><br /> MatchedParameters: IReadOnlyDictionary < sträng, sträng ><br /><br /> Metod: sträng<br /><br /> OriginalUrl:IUrl<br /><br /> URL: IUrl|
|strängkontext. Request.Headers.GetValueOrDefault (headerName: sträng, defaultValue: sträng)|Huvudnamn: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar huvudvärden för CSV-begäran eller `defaultValue` om sidhuvudet inte hittas.|
|context.Response|Brödtext: IMessageBody<br /><br /> Rubriker: IReadOnlyDictionary < string, string [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: sträng|
|strängkontext. Response.Headers.GetValueOrDefault (headerName: sträng, defaultValue: sträng)|Huvudnamn: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar CSV-svar värden i huvudet eller `defaultValue` om sidhuvudet inte hittas.|
|context.Subscription|CreatedTime: Datetime<br /><br /> EndDate: DateTime?<br /><br /> ID: sträng<br /><br /> Nyckel: sträng<br /><br /> Namn: sträng<br /><br /> PrimaryKey: sträng<br /><br /> Sekundär nyckel: sträng<br /><br /> Startdatum: DateTime?|
|context.User|E-post: sträng<br /><br /> Förnamn: sträng<br /><br /> Grupper: IEnumerable < IGroup\><br /><br /> ID: sträng<br /><br /> Identiteter: IEnumerable < IUserIdentity\><br /><br /> Efternamn: sträng<br /><br /> Obs: sträng<br /><br /> RegistrationDate: Datetime|
|IApi|ID: sträng<br /><br /> Namn: sträng<br /><br /> Sökväg: sträng<br /><br /> Protokoll: IEnumerable < sträng\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|
|IGroup|ID: sträng<br /><br /> Namn: sträng|
|IMessageBody|Som < T\>(preserveContent: bool = false): Där T: sträng JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Den `context.Request.Body.As<T>` och `context.Response.Body.As<T>` metoderna används för att läsa en begäran och svaret meddelandetexten i en viss typ av `T`. Som standard metoden använder den ursprungliga brödtext meddelandeströmmen och återger den otillgänglig när den returnerar. Om du vill undvika det genom att använda metoden som fungerar på en kopia av brödtextströmmen, ange den `preserveContent` parameter `true`. Gå [här](api-management-transformation-policies.md#SetBody) att se ett exempel.|
|IUrl|Värden: sträng<br /><br /> Sökväg: sträng<br /><br /> Port: int<br /><br /> Fråga: IReadOnlyDictionary < string, string [] ><br /><br /> QueryString: sträng<br /><br /> Schema: sträng|
|IUserIdentity|ID: sträng<br /><br /> Providern: sträng|
|ISubscriptionKeyParameterNames|Rubrik: sträng<br /><br /> Fråga: sträng|
|sträng IUrl.Query.GetValueOrDefault (queryParameterName: sträng, defaultValue: sträng)|queryParameterName: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar kommaavgränsad frågeparametervärden eller `defaultValue` om parametern inte hittas.|
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> Standardvärde: T<br /><br /> Returnerar värdet på variabeln typkonverteras till typen `T` eller `defaultValue` om variabeln inte hittas.<br /><br /> Den här metoden genereras ett undantag om den angivna typen inte matchar den faktiska typen av returnerade variabeln.|
|BasicAuthCredentials AsBasic(input: this string)|inkommande: sträng<br /><br /> Om parametern innehåller ett giltigt grundläggande autentisering för HTTP-begäran auktoriseringsrubrikvärde, metoden returnerar ett objekt av typen `BasicAuthCredentials`; annars returnerar-metoden null.|
|bool TryParseBasic (indata: den här strängen, resultat: ut BasicAuthCredentials)|inkommande: sträng<br /><br /> resultat: ut BasicAuthCredentials<br /><br /> Om parametern innehåller ett giltigt grundläggande HTTP-autentisering auktoriseringsvärde i rubriken returnerar-metoden `true` och resultatet parametern innehåller ett värde av typen `BasicAuthCredentials`; annars returnerar-metoden `false`.|
|BasicAuthCredentials|Lösenord: sträng<br /><br /> Användar-ID: sträng|
|Jwt AsJwt(input: this string)|inkommande: sträng<br /><br /> Om parametern innehåller ett giltigt JWT-token värde, metoden returnerar ett objekt av typen `Jwt`; annars returnerar-metoden `null`.|
|bool TryParseJwt (indata: den här strängen, resultat: ut Jwt)|inkommande: sträng<br /><br /> resultat: ut Jwt<br /><br /> Om parametern indata innehåller ett giltigt JWT-token värde, returnerar-metoden `true` och resultatet parametern innehåller ett värde av typen `Jwt`; annars returnerar-metoden `false`.|
|Jwt|Algoritm: sträng<br /><br /> Målgrupp: IEnumerable < sträng\><br /><br /> Anspråk: IReadOnlyDictionary < string, string [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> ID: sträng<br /><br /> Utfärdare: sträng<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Ämne: sträng<br /><br /> Typ: sträng|
|sträng Jwt.Claims.GetValueOrDefault (claimName: sträng, defaultValue: sträng)|claimName: sträng<br /><br /> Standardvärde: sträng<br /><br /> Returnerar kommaavgränsad anspråksvärden eller `defaultValue` om sidhuvudet inte hittas.|
|byte [] kryptera (indata: den här byte [], algoritm: sträng, nyckel: byte [], iv:byte[])|indata - klartext krypteras<br /><br />algoritm - namnet på en symmetrisk kryptografisk algoritm<br /><br />nyckel - krypteringsnyckeln<br /><br />IV - Initieringsvektorn<br /><br />Returnerar krypterad klartext.|
|byte [] kryptera (indata: den här byte [], algoritm: System.Security.Cryptography.SymmetricAlgorithm)|indata - klartext krypteras<br /><br />algoritm - krypteringsalgoritm<br /><br />Returnerar krypterad klartext.|
|byte [] kryptera (indata: den här byte [], algoritm: System.Security.Cryptography.SymmetricAlgorithm, nyckel: byte [], iv:byte[])|indata - klartext krypteras<br /><br />algoritm - krypteringsalgoritm<br /><br />nyckel - krypteringsnyckeln<br /><br />IV - Initieringsvektorn<br /><br />Returnerar krypterad klartext.|
|byte [] dekryptera (indata: den här byte [], algoritm: sträng, nyckel: byte [], iv:byte[])|indata - text som kod ska dekrypteras<br /><br />algoritm - namnet på en symmetrisk kryptografisk algoritm<br /><br />nyckel - krypteringsnyckeln<br /><br />IV - Initieringsvektorn<br /><br />Returnerar klartext.|
|byte [] dekryptera (indata: den här byte [], algoritm: System.Security.Cryptography.SymmetricAlgorithm)|indata - text som kod ska dekrypteras<br /><br />algoritm - krypteringsalgoritm<br /><br />Returnerar klartext.|
|byte [] dekryptera (indata: den här byte [], algoritm: System.Security.Cryptography.SymmetricAlgorithm, nyckel: byte [], iv:byte[])|kod för indata - indata - text som ska dekrypteras<br /><br />algoritm - krypteringsalgoritm<br /><br />nyckel - krypteringsnyckeln<br /><br />IV - Initieringsvektorn<br /><br />Returnerar klartext.|


## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)
