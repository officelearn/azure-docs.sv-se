---
title: Använda hanterad identitet med ett program
description: Så här använder du hanterade identiteter i Azure Service Fabric-programkod för att komma åt Azure Services.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: cbdb1190ec3238a6accd34db3025e08c194d60b8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415619"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Så här utnyttjar du ett Service Fabric-programs hanterade identitet för att komma åt Azure-tjänster

Tjänst fabric-program kan utnyttja hanterade identiteter för att komma åt andra Azure-resurser som stöder Azure Active Directory-baserad autentisering. Ett program kan hämta en [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token) som representerar dess identitet, som kan vara systemtilldelad eller användartilldelad, och använda den som en "bärare"-token för att autentisera sig till en annan tjänst - även känd som en [skyddad resursserver](../active-directory/develop/developer-glossary.md#resource-server). Token representerar den identitet som tilldelats Programmet Service Fabric och utfärdas endast till Azure-resurser (inklusive SF-program) som delar den identiteten. Se dokumentationen för översikt [över hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för en detaljerad beskrivning av hanterade identiteter samt skillnaden mellan systemtilldelade och användartilldelade identiteter. Vi kommer att referera till ett managed-identity-aktiverat Service Fabric-program som [klientprogrammet](../active-directory/develop/developer-glossary.md#client-application) i hela den här artikeln.

> [!IMPORTANT]
> En hanterad identitet representerar kopplingen mellan en Azure-resurs och ett tjänsthuvudnamn i motsvarande Azure AD-klient som är associerad med prenumerationen som innehåller resursen. I samband med Service Fabric stöds därför hanterade identiteter endast för program som distribueras som Azure-resurser. 

> [!IMPORTANT]
> Innan du använder den hanterade identiteten för ett Service Fabric-program måste klientprogrammet beviljas åtkomst till den skyddade resursen. Se listan över [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) för att söka efter support och sedan till respektive tjänsts dokumentation för specifika steg för att bevilja en identitetsåtkomst till resurser av intresse. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Skaffa en åtkomsttoken med REST API
I kluster som är aktiverade för hanterad identitet exponerar Service Fabric-körningen en localhost-slutpunkt som program kan använda för att hämta åtkomsttoken. Slutpunkten är tillgänglig på varje nod i klustret och är tillgänglig för alla entiteter på den noden. Behöriga anropare kan få åtkomsttoken genom att anropa den här slutpunkten och presentera en autentiseringskod. Koden genereras av Service Fabric-körningen för varje distinkt tjänstkodpaketaktivering och är bunden till livstiden för processen som är värd för det tjänstkodpaketet.

I synnerhet dirigeras miljön för en tjänst med hanterad identitetsaktiverad Service Fabric med följande variabler:
- "IDENTITY_ENDPOINT": den localhost-slutpunkten som motsvarar tjänstens hanterade identitet
- "IDENTITY_HEADER": en unik autentiseringskod som representerar tjänsten på den aktuella noden
- "IDENTITY_SERVER_THUMBPRINT" : Tumavtryck av tjänstinfrastrukturens hanterade identitetsserver

> [!IMPORTANT]
> Programkoden bör betrakta värdet av miljövariabeln "IDENTITY_HEADER" som känsliga uppgifter - den bör inte loggas eller på annat sätt spridas. Autentiseringskoden har inget värde utanför den lokala noden, eller efter att processen som värd för tjänsten har avslutats, men den representerar identiteten på Tjänsten Service Fabric och bör därför behandlas med samma försiktighetsåtgärder som åtkomsttoken själv.

För att få en token utför klienten följande steg:
- bildar en URI genom att sammanfoga slutpunkten för hanterad identitet (IDENTITY_ENDPOINT värde) med API-versionen och den resurs (målgrupp) som krävs för token
- skapar en GET http-begäran för den angivna URI-begäran
- lägger till lämplig verifieringslogik för servercertifikat
- lägger till autentiseringskoden (IDENTITY_HEADER värde) som ett huvud i begäran
- skickar in begäran

Ett lyckat svar innehåller en JSON-nyttolast som representerar den resulterande åtkomsttoken, samt metadata som beskriver den. Ett misslyckat svar kommer också att innehålla en förklaring av felet. Se nedan för ytterligare information om felhantering.

Åtkomsttoken cachelagras av Service Fabric på olika nivåer (nod, kluster, resursprovidertjänst), så ett lyckat svar innebär inte nödvändigtvis att token utfärdades direkt som svar på användarprogrammets begäran. Tokens cachelagras för mindre än deras livstid och därför garanteras ett program att ta emot en giltig token. Vi rekommenderar att programkoden cachelagrar sig själv alla åtkomsttoken som hämtas. cachelagringsnyckeln bör innehålla (en härledning av) publiken. 


> [!NOTE]
> Den enda accepterade API-versionen är för närvarande `2019-07-01-preview`och kan komma att ändras.

Exempel på begäran:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
där:

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet, som anger att du vill hämta data från slutpunkten. I det här fallet en OAuth-åtkomsttoken. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Slutpunkten för hanterad identitet för Service Fabric-program som tillhandahålls via miljövariabeln IDENTITY_ENDPOINT. |
| `api-version` | En frågesträngparameter som anger API-versionen av tjänsten Hanterad identitetstoken. för närvarande är `2019-07-01-preview`det enda accepterade värdet och kan komma att ändras. |
| `resource` | En frågesträngparameter som anger målresursens app-ID-IURI. Detta kommer att `aud` återspeglas som (publiken) anspråk på den utfärdade token. I det här exemplet begärs en token för att komma\/åt Azure Key Vault, vars app-ID URI är https: /vault.azure.net/. |
| `Secret` | Ett HTTP-huvudfält för begäran, som krävs av tjänsten Service Fabric Managed Identity Token Service for Service Fabric-tjänster för att autentisera anroparen. Det här värdet tillhandahålls av SF-körningen via IDENTITY_HEADER miljövariabeln. |


Exempelsvar:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
där:

| Element | Beskrivning |
| ------- | ----------- |
| `token_type` | Typ av token; I det här fallet är en "Bärare" åtkomsttoken, vilket innebär att presentatören ("bäraren") för denna token är det avsedda ämnet för token. |
| `access_token` | Den begärda åtkomsttoken. När du anropar ett skyddat REST `Authorization` API bäddas token in i fältet för begäranhuvud som en "bärare"-token, vilket gör att API:et kan autentisera anroparen. | 
| `expires_on` | Tidsstämpeln för åtkomsttokens förfallodatum. representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" och motsvarar `exp` tokens anspråk. I det här fallet upphör token att gälla 2019-08-08T06:10:11+00:00 (i RFC 3339)|
| `resource` | Den resurs som åtkomsttoken utfärdades för, `resource` som anges via frågesträngparametern för begäran. motsvarar tokens "aud"-anspråk. |


## <a name="acquiring-an-access-token-using-c"></a>Skaffa en åtkomsttoken med C #
Ovanstående blir, i C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Komma åt Key Vault från ett Service Fabric-program med hanterad identitet
Det här exemplet bygger på ovanstående för att demonstrera åtkomst till en hemlighet som lagras i ett Key Vault med hjälp av hanterad identitet.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Felhantering
Fältet statuskod i HTTP-svarshuvudet anger begärans lyckade status. status för 200 OK anger framgång och svaret innehåller åtkomsttoken enligt beskrivningen ovan. Följande är en kort uppräkning av möjliga fel svar.

| Statuskod | Felorsak | Hur man hanterar |
| ----------- | ------------ | ------------- |
| 404 Hittades inte. | Okänd autentiseringskod eller så har programmet inte tilldelats någon hanterad identitet. | Korrigera programinställningarna eller tokeninhämtningskoden. |
| 429 För många förfrågningar. |  Begränsningsgränsen har nåtts, som införts av AAD eller SF. | Försök igen med exponentiell backoff. Se vägledning nedan. |
| 4xx Fel i begäran. | En eller flera av parametrarna för begäran var felaktiga. | Försök inte igen.  Mer information finns i felinformationen.  4xx fel är design-tid fel.|
| 5xx Fel från tjänsten. | Undersystemet Hanterad identitet eller Azure Active Directory returnerade ett tillfälligt fel. | Det är säkert att försöka igen efter en kort stund. Du kan träffa ett begränsningstillstånd (429) vid återförsök.|

Om ett fel uppstår innehåller motsvarande HTTP-svarstext ett JSON-objekt med felinformation:

| Element | Beskrivning |
| ------- | ----------- |
| kod | Felkod. |
| correlationId | Ett korrelations-ID som kan användas för felsökning. |
| meddelande | Utförlig beskrivning av fel. **Felbeskrivningar kan ändras när som helst. Lita inte på själva felmeddelandet.**|

Exempelfel:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Följande är en lista över typiska Service Fabric-fel som är specifika för hanterade identiteter:

| Kod | Meddelande | Beskrivning | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Hemligheten hittades inte i begäranderubrikerna. | Autentiseringskoden angavs inte med begäran. | 
| Hanteradidentitet intefound | Det gick inte att hitta den hanterade identiteten för den angivna programvärden. | Programmet har ingen identitet eller så är autentiseringskoden okänd. |
| ArgumentNullOrEmpty | Parametern "resurs" får inte vara null eller tom sträng. | Resursen (målgruppen) angavs inte i begäran. |
| Ogiltigt ApiVersion | Api-versionen '' stöds inte. Versionen som stöds är "2019-07-01-preview". | API-versionen som inte stöds eller inte stöds har angetts i URI:n för begäran. |
| InternalServerError | Ett fel inträffade. | Ett fel påträffades i undersystemet för hanterad identitet, eventuellt utanför Service Fabric-stacken. Mest troligt är orsaken ett felaktigt värde som anges för resursen (kontrollera efterföljande /'?) | 

## <a name="retry-guidance"></a>Vägledning för återförsök 

Vanligtvis är den enda felkoden för återförsök 429 (för många begäranden). interna serverfel/5xx-felkoder kan kunna återanvändas, även om orsaken kan vara permanent. 

Begränsningsgränser gäller för antalet anrop som görs till det hanterade identitetsundersystemet - särskilt "uppströms"-beroenden (Azure-tjänsten för hanterad identitet eller den säkra tokentjänsten). Service Fabric cachelagrar token på olika nivåer i pipelinen, men med tanke på de berörda komponenternas distribuerade karaktär kan anroparen uppleva inkonsekventa begränsningssvar (dvs. få begränsning på en nod/instans av ett program, men inte på en annan nod när du begär en token för samma identitet.) När begränsningsvillkoret har angetts kan efterföljande begäranden från samma program misslyckas med HTTP-statuskoden 429 (för många begäranden) tills villkoret har rensats.  

Vi rekommenderar att begäranden som misslyckats på grund av begränsning görs på nytt med en exponentiell backoff enligt följande: 

| Ring index | Åtgärder för att ta emot 429 | 
| --- | --- | 
| 1 | Vänta 1 sekund och försök igen |
| 2 | Vänta 2 sekunder och försök igen |
| 3 | Vänta 4 sekunder och försök igen |
| 4 | Vänta 8 sekunder och försök igen |
| 4 | Vänta 8 sekunder och försök igen |
| 5 | Vänta 16 sekunder och försök igen |

## <a name="resource-ids-for-azure-services"></a>Resurs-ID:er för Azure-tjänster
Se [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-msi.md) för en lista över resurser som stöder Azure AD och deras respektive resurs-ID.

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användartilldelad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Bevilja en Azure Service Fabric-programåtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)
