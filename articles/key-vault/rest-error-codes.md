---
title: REST API-felkoder - Azure Key Vault
description: Dessa felkoder kan returneras av en åtgärd på en Azure Key Vault-webbtjänst.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 01fb5393217834bc0196da25c4a56314ca7eae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294539"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>AZURE Key Vault REST API-felkoder
 
Följande felkoder kan returneras av en åtgärd på en Azure Key Vault-webbtjänst.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Oautentiserade begäran

401 innebär att begäran inte är oautentiserade för Key Vault. 

En begäran autentiseras om:

- Nyckelvalvet känner till den som ringers identitet. Och
- Anroparen kan försöka komma åt Key Vault-resurser. 

Det finns flera olika skäl till varför en begäran kan returnera 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Ingen autentiseringstoken som är kopplad till begäran. 

Här är ett exempel PUT-begäran som anger värdet för en hemlighet:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

Rubriken "Auktorisering" är den åtkomsttoken som krävs för varje anrop till Key Vault för dataplansoperationer. Om huvudet saknas måste svaret vara 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Token saknar rätt resurs som är associerad med den. 

När du begär en åtkomsttoken från Azure OAUTH-slutpunkten är en parameter som kallas "resurs" obligatorisk. Värdet är viktigt för tokenprovidern eftersom det scopes token för dess avsedda användning. Resursen för **alla** token för att komma åt ett Key Vault är *https:\//vault.keyvault.net* (utan avslutande snedstreck).

### <a name="the-token-is-expired"></a>Token har upphört att gälla

Tokens är base64 kodade och värdena kan avkodas på webbplatser som [http://jwt.calebb.net](http://jwt.calebb.net). Här är ovanstående token avkodas:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

Vi kan se många viktiga delar i denna token:

- aud (publik): Tokens resurs. Observera att <https://vault.azure.net>detta är . Den här token fungerar INTE för någon resurs som inte uttryckligen matchar det här värdet, till exempel diagram.
- iat (utfärdat på): Antalet fästingar sedan början av epoken när token utfärdades.
- nbf (inte före): Antalet fästingar sedan epoken började när denna token blir giltig.
- exp (förfallodatum): Antalet fästingar sedan början av epoken när den här token upphör att gälla.
- appid (program-ID): GUID för program-ID som gör den här begäran.
- tid (klient-ID): GUID för klient-ID för huvudmannen som gör den här begäran

Det är viktigt att alla värden identifieras korrekt i token för att begäran ska fungera. Om allt är korrekt, kommer begäran inte resultera i 401.

### <a name="troubleshooting-401"></a>Felsökning 401

401:ar bör undersökas från den punkt där tokengenerering sker, innan begäran görs till nyckelvalvet. I allmänhet används kod för att begära token. När token har tagits emot skickas den till Key Vault-begäran. Om koden körs lokalt kan du använda Fiddler för att `https://login.microsoftonline.com`samla in begäran/svaret på . En begäran ser ut så här:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Följande information som tillhandahålls av användaren är korrekt:

- Klient-ID:et för nyckelvalv
- Resursvärde inställt på https%3A%2F%2Fvault.azure.net (URL-kodad)
- Klientorganisations-ID
- Klienthemlighet

Se till att resten av begäran är nästan identisk.

Om du bara kan hämta svarsåtkomsttoken kan du avkoda den (som visas ovan) för att säkerställa klient-ID, klient-ID (app-ID) och resursen.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Otillräckliga behörigheter

HTTP 403 innebär att begäran autentiserades (den känner till den begärande identiteten) men identiteten har inte behörighet att komma åt den begärda resursen. Det finns två orsaker:

- Det finns ingen åtkomstprincip för identiteten.
- IP-adressen för den begärande resursen är inte vitlistad i nyckelvalvets brandväggsinställningar.

HTTP 403 inträffar ofta när kundens program inte använder klient-ID som kunden tror att det är. Det innebär vanligtvis att åtkomstprinciperna inte har ställts in korrekt för den faktiska anropande identiteten.

### <a name="troubleshooting-403"></a>Felsökning 403

Aktivera först loggning. Instruktioner om hur du gör det finns i [Loggning av Azure Key Vault](key-vault-logging.md).

När loggningen är aktiverad kan du avgöra om 403 beror på åtkomstprincipen eller brandväggsprincipen.

#### <a name="error-due-to-firewall-policy"></a>Fel på grund av brandväggsprincipen

"Klientadress (00.00.00.00) är inte auktoriserad och anroparen är inte en betrodd tjänst"

Det finns en begränsad lista över "Azure Trusted Services". Azure-webbplatser är **inte** en betrodd Azure-tjänst. Mer information finns i [åtkomsten till nyckelvalvsåtkomst för key vault-brandväggen via Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Du måste lägga till IP-adressen för Azure-webbplatsen i Nyckelvalvet för att den ska fungera.

Om det beror på åtkomstprincip: hitta objekt-ID för begäran och se till att objekt-ID matchar det objekt som användaren försöker tilldela åtkomstprincipen. Det kommer ofta att finnas flera objekt i AAD som har samma namn, så att välja rätt är mycket viktigt. Genom att ta bort och lägga till åtkomstprincipen igen är det möjligt att se om flera objekt finns med samma namn.

Dessutom kräver de flesta åtkomstprinciper inte att det "auktoriserade programmet" används som visas i portalen. Auktoriserat program används för autentiseringsscenarier för "för den skull" som är sällsynta. 


## <a name="http-429-too-many-requests"></a>HTTP 429: För många begäranden

Begränsning sker när antalet begäranden överskrider det angivna maxbeloppet för tidsramen. Om begränsning inträffar kommer Key Vault svar vara HTTP 429. Det finns angivna maximiför olika typer av förfrågningar. Till exempel: skapandet av en HSM 2048-bitarsnyckel är 5 begäranden per 10 sekunder, men alla andra HSM-transaktioner har en gräns på 1 000 begäranden/10 sekunder. Därför är det viktigt att förstå vilka typer av samtal som görs vid fastställandet av orsaken till begränsning.
I allmänhet är begäranden till Key Vault begränsade till 2000 begäranden/10 sekunder. Undantag är nyckelåtgärder, som dokumenteras i [servicegränser för Key Vault](key-vault-service-limits.md)

### <a name="troubleshooting-429"></a>Felsökning 429
Begränsning bearbetas med hjälp av dessa tekniker:

- Minska antalet begäranden som görs till Nyckelvalvet genom att avgöra om det finns mönster för en begärd resurs och försöker cachelagra dem i det anropande programmet. 

- När Begränsning av Key Vault inträffar anpassar du den begärande koden så att den använder en exponentiell backoff för återförsök. Algoritmen förklaras här: [Så här begränsar du din app](key-vault-ovw-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Om antalet begäranden inte kan minskas genom cachelagring och tidsinnad backoff inte fungerar, överväg du att dela upp nycklarna i flera nyckelvalv. Servicegränsen för en enskild prenumeration är 5x den enskilda Key Vault-gränsen. Om du använder mer än 5 nyckelvalv bör man överväga att använda flera prenumerationer. 

Detaljerad vägledning inklusive begäran om att öka gränserna, kan hittas här: [Key Vault strypning vägledning](key-vault-ovw-throttling.md)


