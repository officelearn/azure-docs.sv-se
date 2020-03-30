---
title: Principuttryck för Azure API Management | Microsoft-dokument
description: Lär dig mer om principuttryck i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244009"
---
# <a name="api-management-policy-expressions"></a>API Management-principuttryck
I den här artikeln beskrivs principuttrycksyntaxen i C# 7. Varje uttryck har åtkomst till den implicit angivna [kontextvariabeln](api-management-policy-expressions.md#ContextVariables) och en tillåten [delmängd](api-management-policy-expressions.md#CLRTypes) av .NET Framework-typer.

Mer information:

- Se hur du anger kontextinformation till din backend-tjänst. Använd [parametern Ange frågesträng](api-management-transformation-policies.md#SetQueryStringParameter) och [Ange HTTP-huvudprinciper](api-management-transformation-policies.md#SetHTTPheader) för att ange den här informationen.
- Se hur du använder [principen Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) för att förauktorisera åtkomst till åtgärder baserat på tokenanspråk.
- Se hur du använder en [API Inspector-spårning](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) för att se hur principer utvärderas och resultaten av dessa utvärderingar.
- Se hur du använder uttryck med [Hämta från cacheminnet](api-management-caching-policies.md#GetFromCache) och [Store till cacheprinciper](api-management-caching-policies.md#StoreToCache) för att konfigurera API Management-svarscachelagring. Ange en varaktighet som matchar svarscacheningen för backend-tjänsten enligt `Cache-Control` det stödda tjänstens direktiv.
- Se hur du utför innehållsfiltrering. Ta bort dataelement från svaret som tas emot från backend med hjälp av [styrflödes-](api-management-advanced-policies.md#choose) och [ange kroppsprinciper.](api-management-transformation-policies.md#SetBody)
- Om du vill hämta principsatserna läser du [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub repo.


## <a name="syntax"></a><a name="Syntax"></a>Syntax
Enstaka uttryck återges `@(expression)`i `expression` , där är en välformad C#-uttryckssats.

Multi-statement-uttryck omges `@{expression}`av . Alla kodsökvägar i flerutdragsuttryck `return` måste sluta med en sats.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a>Exempel

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Användning
Uttryck kan användas som attributvärden eller textvärden i alla API [Management-principer](api-management-policies.md) (såvida inte principreferensen anger något annat).

> [!IMPORTANT]
> När du använder principuttryck finns det bara begränsad verifiering av principuttrycken när principen har definierats. Uttryck körs av gatewayen vid körning, eventuella undantag som genereras av principuttryck resulterar i ett körningsfel.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a>.NET Framework-typer som tillåts i principuttryck
I följande tabell visas de .NET Framework-typer och deras medlemmar som är tillåtna i principuttryck.

|Typ|Medlemmar som stöds|
|--------------|-----------------------|
|Newtonsoft.Json.Formatering|Alla|
|Newtonsoft.Json.JsonConvert|SerializeObject, AvserialiseraObjekt|
|Newtonsoft.Json.Linq.Extensions|Alla|
|Newtonsoft.Json.Linq.JArray|Alla|
|Newtonsoft.Json.Linq.JKonstruktor|Alla|
|Newtonsoft.Json.Linq.JContainer|Alla|
|Newtonsoft.Json.Linq.JObject|Alla|
|Newtonsoft.Json.Linq.JProperty|Alla|
|Newtonsoft.Json.Linq.JRaw|Alla|
|Newtonsoft.Json.Linq.JToken|Alla|
|Newtonsoft.Json.Linq.JTokenType|Alla|
|Newtonsoft.Json.Linq.JValue|Alla|
|System.Array|Alla|
|System.bitKonverter|Alla|
|System.Boolean|Alla|
|System.Byte|Alla|
|System.Char (på)System.Char )|Alla|
|System.Collections.Generic.Dictionary<TKey, TValue>|Alla|
|System.Collections.Generic.HashSet\<T>|Alla|
|System.Collections.Generic.ICollection\<T>|Alla|
|System.Collections.Generic.IDictionary<TKey, TValue>|Alla|
|System.Collections.Generic.IEnumerable\<T>|Alla|
|System.Collections.Generic.IEnumerator\<T>|Alla|
|System.Collections.Generic.IList\<T>|Alla|
|System.Collections.Generic.IReadOnlyCollection\<T>|Alla|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Alla|
|System.Collections.Generic.ISet\<T>|Alla|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Alla|
|System.Collections.Generic.List\<T>|Alla|
|System.Collections.Generic.Queue\<T>|Alla|
|System.Collections.Generic.Stack\<T>|Alla|
|System.Konvertera|Alla|
|System.DateTime|(Konstruktör), Add, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddTicks, AddYears, Date, Day, DayOfWeek, DayOfYear, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, Millisecond, Minute, MinValue, Month, Now , Parse, Andra, Subtrahera, Fästingar, TimeOfDay, Idag, ToString, UtcNow, År|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Alla|
|System.Decimal|Alla|
|System.Double (System.Double)|Alla|
|System.Undantag|Alla|
|System.Guid (på samma sätt som)|Alla|
|System.Int16|Alla|
|System.Int32|Alla|
|System.Int64|Alla|
|System.IO.StringReader|Alla|
|System.IO.StringWriter|Alla|
|System.Linq.Uppräkningsbar|Alla|
|System.Matematik|Alla|
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
|System.Security.Cryptography.SHA256Hantaged|Alla|
|System.Security.Cryptography.SHA384|Alla|
|System.Security.Cryptography.SHA384Hantaged|Alla|
|System.Security.Cryptography.SHA512|Alla|
|System.Security.Cryptography.SHA512Hanterad|Alla|
|System.Security.Cryptography.SymmetricAlgorithm|Alla|
|System.Security.Cryptography.X509Certificates.PublicKey|Alla|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Alla|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Namn|
|System.Security.Cryptography.X509Certificates.X509Certificate|Alla|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Alla|
|System.Security.Cryptography.X509Certificates.X509ContentType|Alla|
|System.Security.Cryptography.X509Certificates.X509NameType|Alla|
|System.Single|Alla|
|System.Sträng|Alla|
|System.StringComparer|Alla|
|System.StringComparison|Alla|
|System.StringSplitOptions|Alla|
|System.Text.Kodning|Alla|
|System.Text.RegularExpressions.Capture|Index, Längd, Värde|
|System.Text.RegularExpressions.CaptureKollyning|Antal, artikel|
|System.Text.RegularExpressions.Group|Fångar, framgång|
|System.Text.RegularExpressions.GroupCollection|Antal, artikel|
|System.Text.RegularExpressions.Match|Tom, Grupper, Resultat|
|System.Text.RegularExpressions.Regex|(Konstruktör), IsMatch, Match, Matcher, Ersätt, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Alla|
|System.Text.StringBuilder|Alla|
|system.timespan|Alla|
|System.TimeZone|Alla|
|System.TimeZoneInfo.AdjustmentRule|Alla|
|System.TimeZoneInfo.TransitionTime|Alla|
|System.TimeZoneInfo|Alla|
|System.Tuple (System.Tuple)|Alla|
|System.UInt16|Alla|
|System.UInt32|Alla|
|System.UInt64|Alla|
|System.Uri (på samma sätt som)|Alla|
|System.UriPartial|Alla|
|System.Xml.Linq.Extensions|Alla|
|System.Xml.Linq.XAttribute|Alla|
|System.Xml.Linq.xCData|Alla|
|System.Xml.Linq.XKommentar|Alla|
|System.Xml.Linq.XContainer|Alla|
|System.Xml.Linq.XDeclaration|Alla|
|System.Xml.Linq.xDokument|Alla, förutom: Ladda|
|System.Xml.Linq.xDocumentType|Alla|
|System.Xml.Linq.XElement|Alla|
|System.Xml.Linq.XName|Alla|
|System.Xml.Linq.XNamespace|Alla|
|System.Xml.Linq.XNode|Alla|
|System.Xml.Linq.XNodeDocumentOrderComparer|Alla|
|System.Xml.Linq.XNodeEqualityComparer|Alla|
|System.Xml.Linq.xobject|Alla|
|System.Xml.Linq.XProcessingInstruction|Alla|
|System.Xml.Linq.XText|Alla|
|System.Xml.XmlNodeTyp|Alla|

## <a name="context-variable"></a><a name="ContextVariables"></a>Kontextvariabel
En variabel `context` med namnet är implicit tillgänglig i varje [principuttryck](api-management-policy-expressions.md#Syntax). Dess medlemmar lämnar information som `\request`är relevant för . Alla `context` medlemmar är skrivskyddade.

|Kontextvariabel|Tillåtna metoder, egenskaper och parametervärden|
|----------------------|-------------------------------------------------------|
|Sammanhang|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Distribution](#ref-context-deployment)<br /><br /> Förfluten: TimeSpan - tidsintervall mellan värdet på tidsstämpel och aktuell tid<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Drift](#ref-context-operation)<br /><br /> [Produkt](#ref-context-product)<br /><br /> [Förfrågan](#ref-context-request)<br /><br /> RequestId: Guid - unik begärandeidentifierare<br /><br /> [Svar](#ref-context-response)<br /><br /> [Prenumeration](#ref-context-subscription)<br /><br /> Tidsstämpel: DateTime - tidpunkt då begäran togs emot<br /><br /> Spårning: bool - anger om spårning är på eller av <br /><br /> [Användare](#ref-context-user)<br /><br /> [Variabler](#ref-context-variables): IReadOnlyDictionary<sträng, objekt><br /><br /> ogiltiga spårning(meddelande: sträng)|
|<a id="ref-context-api"></a>Sammanhang. Api|Id: sträng<br /><br /> IsCurrentRevision: bool<br /><br />  Namn: sträng<br /><br /> Bana: sträng<br /><br /> Revidering: sträng<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Version: sträng |
|<a id="ref-context-deployment"></a>Sammanhang. Distribution|Region: sträng<br /><br /> ServiceName: sträng<br /><br /> Certifikat: IReadOnlyDictionary<sträng, X509Certificate2>|
|<a id="ref-context-lasterror"></a>Sammanhang. Lasterror|Källa: sträng<br /><br /> Orsak: sträng<br /><br /> Meddelande: sträng<br /><br /> Omfattning: sträng<br /><br /> Avsnitt: sträng<br /><br /> Bana: sträng<br /><br /> PolicyId: sträng<br /><br /> Mer information om sammanhanget finns. LastError, se [Felhantering](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>Sammanhang. Drift|Id: sträng<br /><br /> Metod: sträng<br /><br /> Namn: sträng<br /><br /> UrlTemplate: sträng|
|<a id="ref-context-product"></a>Sammanhang. Produkt|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> GodkännandeRequired: bool<br /><br /> Grupper: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: sträng<br /><br /> Namn: sträng<br /><br /> Tillstånd: uppräkning ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> PrenumerationKäckt: bool|
|<a id="ref-context-request"></a>Sammanhang. Begäran|Kropp: [IMessageBody](#ref-imessagebody) eller `null` om begäran inte har en kropp.<br /><br /> Certifikat: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Rubriker](#ref-context-request-headers): IReadOnlyDictionary<sträng, sträng[]><br /><br /> IpAddress: sträng<br /><br /> MatchedParameters: IReadOnlyDictionary<sträng, sträng><br /><br /> Metod: sträng<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>strängkontext. Request.Headers.GetValueOrDefault(headerName: sträng, defaultValue: string)|headerName: sträng<br /><br /> defaultValue: sträng<br /><br /> Returnerar kommaavgränsade begäranhuvudvärden eller `defaultValue` om huvudet inte hittas.|
|<a id="ref-context-response"></a>Sammanhang. Svar|Kropp: [IMessageBody](#ref-imessagebody)<br /><br /> [Rubriker](#ref-context-response-headers): IReadOnlyDictionary<sträng, sträng[]><br /><br /> Statuskod: int<br /><br /> StatusReason: sträng|
|<a id="ref-context-response-headers"></a>strängkontext. Response.Headers.GetValueOrDefault(headerName: sträng, defaultValue: string)|headerName: sträng<br /><br /> defaultValue: sträng<br /><br /> Returnerar komeparerade svarshuvudvärden eller `defaultValue` om huvudet inte hittas.|
|<a id="ref-context-subscription"></a>Sammanhang. Prenumeration|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: sträng<br /><br /> Nyckel: sträng<br /><br /> Namn: sträng<br /><br /> PrimaryKey: sträng<br /><br /> SecondaryKey: sträng<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>Sammanhang. Användaren|E-post: sträng<br /><br /> Förnamn: sträng<br /><br /> Grupper: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: sträng<br /><br /> Identiteter: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> Efternamn: sträng<br /><br /> Obs: sträng<br /><br /> RegistreringDatum: DateTime|
|<a id="ref-iapi"></a>IApi (på andra sätt)|Id: sträng<br /><br /> Namn: sträng<br /><br /> Bana: sträng<br /><br /> Protokoll: IEnumerable<sträng\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup (IGroup)|Id: sträng<br /><br /> Namn: sträng|
|<a id="ref-imessagebody"></a>IMessageBody|Som<T\>(preserveContent: bool = false): Where T: string, byte[],JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> `context.Request.Body.As<T>` Metoderna `context.Response.Body.As<T>` och används för att läsa en begäran och `T`svarsmeddelandekroppar i en angiven typ . Som standard använder metoden den ursprungliga meddelandekroppsströmmen och gör den otillgänglig när den har returnerats. För att undvika att genom att metoden fungerar på `preserveContent` en `true`kopia av brödtextströmmen, ställ in parametern på . Gå [hit](api-management-transformation-policies.md#SetBody) för att se ett exempel.|
|<a id="ref-iurl"></a>IUrl (på ett år)|Värd: sträng<br /><br /> Bana: sträng<br /><br /> Hamn: int<br /><br /> [Fråga](#ref-iurl-query): IReadOnlyDictionary<sträng, sträng[]><br /><br /> QueryString: sträng<br /><br /> Schema: sträng|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: sträng<br /><br /> Provider: sträng|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Rubrik: sträng<br /><br /> Fråga: sträng|
|<a id="ref-iurl-query"></a>sträng IUrl.Query.GetValueOrDefault(queryParameterName: sträng, defaultValue: string)|queryParameterName: sträng<br /><br /> defaultValue: sträng<br /><br /> Returnerar komeparerade frågeparatmetervärden eller `defaultValue` om parametern inte hittas.|
|<a id="ref-context-variables"></a>T sammanhang. Variabler.GetValueOrDefault<T\>(variableName: sträng, standardVärde: T)|variableName: sträng<br /><br /> defaultValue: T<br /><br /> Returnerar variabelt `T` värde `defaultValue` som castas till typ eller om variabeln inte hittas.<br /><br /> Den här metoden genererar ett undantag om den angivna typen inte matchar den faktiska typen av den returnerade variabeln.|
|BasicAuthCredentials AsBasic(indata: den här strängen)|ingång: sträng<br /><br /> Om indataparametern innehåller ett giltigt lösenord för begäran om HTTP `BasicAuthCredentials`Basic-autentisering returnerar metoden ett objekt av typen . Annars returnerar metoden null.|
|bool TryParseBasic (ingång: denna sträng, resultat: ut BasicAuthCredentials)|ingång: sträng<br /><br /> resultat: ut BasicAuthCredentials<br /><br /> Om indataparametern innehåller ett giltigt HTTP Basic Authentication `true` auktoriseringsvärde i begäranden returnerar metoden och resultatparametern innehåller ett typvärde `BasicAuthCredentials`. annars returneras `false`metoden .|
|GrundläggandeAutentiseringsminnen|Lösenord: sträng<br /><br /> UserId: sträng|
|Jwt AsJwt (ingång: denna sträng)|ingång: sträng<br /><br /> Om indataparametern innehåller ett giltigt JWT-tokenvärde `Jwt`returnerar metoden ett objekt av typen ; annars returneras `null`metoden .|
|bool TryParseJwt (ingång: denna sträng, resultat: ut Jwt)|ingång: sträng<br /><br /> resultat: ut Jwt<br /><br /> Om indataparametern innehåller ett giltigt JWT-tokenvärde returneras `true` metoden och `Jwt`resultatparametern innehåller ett typvärde . annars returneras `false`metoden .|
|Jwt|Algoritm: sträng<br /><br /> Publik: IEnumerable<sträng\><br /><br /> Anspråk: IReadOnlyDictionary<sträng, sträng[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: sträng<br /><br /> Emittent: sträng<br /><br /> UtfärdatPå: DateTime?<br /><br /> InteFörutn: DateTime?<br /><br /> Ämne: sträng<br /><br /> Typ: sträng|
|sträng Jwt.Claims.GetValueOrDefault(claimName: sträng, defaultValue: string)|claimName: sträng<br /><br /> defaultValue: sträng<br /><br /> Returnerar kommaavgränsade `defaultValue` anspråksvärden eller om huvudet inte hittas.|
|byte[] Kryptera(indata: den här bytet[], alg: sträng, nyckel:byte[], iv:byte[])|ingång - klartext som ska krypteras<br /><br />alg - namnet på en symmetrisk krypteringsalgoritm<br /><br />nyckel - krypteringsnyckel<br /><br />iv - initieringsvektor<br /><br />Returnerar krypterad klartext.|
|byte[] Kryptera(indata: den här bytet[], alg: System.Security.Cryptography.SymmetricAlgorithm)|ingång - klartext som ska krypteras<br /><br />alg - krypteringsalgoritm<br /><br />Returnerar krypterad klartext.|
|byte[] Kryptera(indata: den här bytet[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|ingång - klartext som ska krypteras<br /><br />alg - krypteringsalgoritm<br /><br />nyckel - krypteringsnyckel<br /><br />iv - initieringsvektor<br /><br />Returnerar krypterad klartext.|
|byte[] Dekryptera(indata: den här bytet[], alg: sträng, nyckel:byte[], iv:byte[])|ingång - cypher text som ska dekrypteras<br /><br />alg - namnet på en symmetrisk krypteringsalgoritm<br /><br />nyckel - krypteringsnyckel<br /><br />iv - initieringsvektor<br /><br />Returnerar klartext.|
|byte[] Dekryptera(indata: denna byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|ingång - cypher text som ska dekrypteras<br /><br />alg - krypteringsalgoritm<br /><br />Returnerar klartext.|
|byte[] Dekryptera(indata: den här bytet[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|ingång - cypher text som ska dekrypteras<br /><br />alg - krypteringsalgoritm<br /><br />nyckel - krypteringsnyckel<br /><br />iv - initieringsvektor<br /><br />Returnerar klartext.|
|bool VerifyNoRevocation(indata: detta System.Security.Cryptography.X509Certificates.X509Certificate2)|Utför en X.509-kedjevalidering utan att kontrollera certifikatåterkallningsstatus.<br /><br />indata - certifikatobjekt<br /><br />Returnerar `true` om valideringen lyckas. `false` om valideringen misslyckas.|


## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

+ [Principer i API-hantering](api-management-howto-policies.md)
+ [Omvandla API:er](transform-api.md)
+ [Principreferens](api-management-policy-reference.md) för en fullständig lista över policyutdrag och deras inställningar
+ [Policyexempel](policy-samples.md)
