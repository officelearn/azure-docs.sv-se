---
title: Azure Service Fabric – Använd hanterad identitet med Service Fabric program | Microsoft Docs
description: Använda hanterade identiteter från Service Fabric program kod
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 7/25/2019
ms.author: atsenthi
ms.openlocfilehash: 8e535fc581e186abd032206c2bbf78623d95967f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899776"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services-preview"></a>Hur du utnyttjar ett Service Fabric programmets hanterade identitet för att få åtkomst till Azure-tjänster (för hands version)

Service Fabric program kan dra nytta av hanterade identiteter för att få åtkomst till andra Azure-resurser som stöder Azure Active Directory-baserad autentisering. Ett program kan hämta en [](../active-directory/develop/developer-glossary.md#access-token) åtkomsttoken som representerar identiteten, som kan vara systemtilldelad eller tilldelad, och använda den som en "Bearer"-token för att autentisera sig mot en annan tjänst, även kallat en [skyddad resurs Server](../active-directory/develop/developer-glossary.md#resource-server). Token representerar den identitet som tilldelats Service Fabric programmet och kommer bara att utfärdas till Azure-resurser (inklusive SF-program) som delar identiteten. Mer detaljerad information om hanterade identiteter finns i översikts dokumentationen för [Managed Identity](../active-directory/managed-identities-azure-resources/overview.md) , samt skillnaden mellan systemtilldelade och användarspecifika identiteter. Vi kommer att referera till ett hanterat identitets aktiverat Service Fabric program som [klient program](../active-directory/develop/developer-glossary.md#client-application) i den här artikeln.

> [!IMPORTANT]
> En hanterad identitet representerar associationen mellan en Azure-resurs och ett huvud namn för tjänsten i motsvarande Azure AD-klient som är associerad med den prenumeration som innehåller resursen. I samband med Service Fabric stöds hanterade identiteter endast för program som distribueras som Azure-resurser. 

> [!IMPORTANT]
> Innan du använder den hanterade identiteten för ett Service Fabric-program måste klient programmet beviljas åtkomst till den skyddade resursen. Se listan över [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) för att kontrol lera om det finns stöd och till respektive tjänsts dokumentation för specifika steg för att ge en identitets åtkomst till resurser av intresse. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Skaffa en åtkomsttoken med hjälp av REST API
I kluster som är aktiverade för hanterad identitet exponerar Service Fabric runtime en localhost-slutpunkt som program kan använda för att hämta åtkomsttoken. Slut punkten är tillgänglig på varje nod i klustret och är tillgänglig för alla entiteter på den noden. Auktoriserade anropare kan hämta åtkomsttoken genom att anropa den här slut punkten och presentera en autentiseringsnyckel. koden genereras av Service Fabric runtime för varje distinkt service code-aktivering och är kopplad till livs längden för processen som är värd för tjänst kods paketet.

Mer specifikt kommer miljön för en hanterad identitets aktive rad Service Fabric-tjänst att dirigeras med följande variabler:
- ' MSI_ENDPOINT ': localhost-slutpunkten, fullständig med sökväg, API-version och parametrar som motsvarar tjänstens hanterade identitet
- ' MSI_SECRET ': en autentiseringsnyckel, som är en ogenomskinlig sträng och som unikt representerar tjänsten på den aktuella noden

> [!NOTE]
> Namnen "MSI_ENDPOINT" och "MSI_SECRET" avser den tidigare beteckningen för hanterade identiteter ("Hanterad tjänstidentitet") och som nu är föråldrad. Namnen är också konsekventa med motsvarande miljö variabel namn som används av andra Azure-tjänster som stöder hanterade identiteter.

> [!IMPORTANT]
> Program koden bör ta hänsyn till värdet för miljövariabeln "MSI_SECRET" som känsliga data – den bör inte loggas eller spridas på annat sätt. Autentiseringsmetoden har inget värde utanför den lokala noden, eller efter att processen som är värd för tjänsten har avbrutits, men den representerar identiteten för den Service Fabric tjänsten och bör därför behandlas med samma försiktighets åtgärder som själva åtkomsttoken.

Klienten utför följande steg för att hämta en token:
- formar en URI genom att sammanfoga den hanterade identitets slut punkten (MSI_ENDPOINT-värde) med API-versionen och resursen (mål gruppen) som krävs för token
- skapar en http-begäran för angiven URI
- lägger till autentiseringsmetoden (MSI_SECRET-värdet) som en rubrik i begäran
- skickar begäran

Ett lyckat svar innehåller en JSON-nyttolast som representerar den resulterande åtkomsttoken, samt metadata som beskriver den. Ett misslyckat svar kommer även att innehålla en förklaring av problemet. Se nedan om du vill ha mer information om fel hantering.

Åtkomsttoken cachelagras av Service Fabric på olika nivåer (nod, kluster, Resource Provider-tjänst), så ett lyckat svar innebär inte nödvändigt vis att token har utfärdats direkt som svar på användarens begäran. Token cachelagras under mindre än deras livs längd och så att ett program garanterat får en giltig token. Vi rekommenderar att program koden cachelagrar sig själv alla åtkomsttoken som den hämtar. caching-nyckeln ska innehålla (en härledning av) mål gruppen. 


Exempel förfrågan:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
vilken

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet som anger att du vill hämta data från slut punkten. I det här fallet en OAuth-åtkomsttoken. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | Den hanterade identitets slut punkten för Service Fabric program som tillhandahålls via miljövariabeln MSI_ENDPOINT. |
| `api-version` | En frågesträngparametern, som anger API-versionen för den hanterade identitets-token-tjänsten. för närvarande är `2019-07-01-preview`det enda godkända värdet och kan komma att ändras. |
| `resource` | En frågesträngparametern som anger URI för app-ID för mål resursen. Detta kommer att avspeglas som `aud` mål för den utfärdade token. I det här exemplet begärs en token för åtkomst till Azure Key Vault, vars app-\/ID-URI är https:/keyvault.Azure.com/. |
| `Secret` | Ett huvud fält för HTTP-begäran som krävs av den Service Fabric Managed Identity token-tjänsten för att Service Fabric Services ska kunna autentisera anroparen. Det här värdet tillhandahålls av MSI_SECRET-miljövariabeln SF-körning via miljö. |


Exempel svar:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://keyvault.azure.com/"
}
```
vilken

| Element | Beskrivning |
| ------- | ----------- |
| `token_type` | Typ av token; i det här fallet är en "Bearer"-åtkomsttoken, vilket innebär att presentatören ("Bearer") för denna token är det avsedda ämnet för token. |
| `access_token` | Den begärda åtkomsttoken. När du anropar en skyddad REST API, bäddas token `Authorization` in i fältet begär ande huvud som en "Bearer"-token, vilket gör att API: et kan autentisera anroparen. | 
| `expires_on` | Tidsstämpeln för förfallo datum för åtkomsttoken. representeras som antalet sekunder från "1970-01-01T0:0: 0Z UTC" och motsvarar tokens `exp` anspråk. I det här fallet upphör token att gälla den 2019-08-08T06:10:11 + 00:00 (i RFC 3339)|
| `resource` | Resursen för vilken åtkomsttoken utfärdades, som anges via `resource` frågesträngparametern ' AUD '-anspråk, motsvarar token ' '-anspråk. |


## <a name="acquiring-an-access-token-using-c"></a>Förvärva en åtkomsttoken med hjälp avC#
Ovanstående blir i C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;

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
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("MSI_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("MSI_SECRET");
            var managedIdentityAuthenticationHeader = "secret";
            var managedIdentityApiVersion = "2019-07-01-preview";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);

            try
            {
                var response = await new HttpClient().SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var accessToken = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                return accessToken.Trim('"');
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
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Få åtkomst till Key Vault från ett Service Fabric program med hanterad identitet
Det här exemplet bygger på ovanstående för att demonstrera åtkomst till en hemlighet som lagras i en Key Vault med hjälp av hanterad identitet.

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

            Console.WriteLine($"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Console.WriteLine("\n== Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    var secretMetadata = secretResponse.Body.ToString();
                    Console.WriteLine($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    Console.WriteLine($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
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

            Console.WriteLine(response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority"></param>
        /// <param name="resource"></param>
        /// <param name="scope"></param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            if (config.DoVerboseLogging)
                Console.WriteLine($"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";
            if (config.DoVerboseLogging)
                Console.WriteLine($"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            if (config.DoVerboseLogging)
                Console.WriteLine($"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            if (config.DoVerboseLogging)
                Console.WriteLine($"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var accessToken = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            if (config.DoVerboseLogging)
                Console.WriteLine("returning access code..");

            return accessToken.Trim('"');
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
```

## <a name="error-handling"></a>Felhantering
I fältet status kod i HTTP-svarshuvuden anges framgångs status för begäran. statusen "200 OK" anger att åtgärden har slutförts och svaret innehåller åtkomsttoken enligt beskrivningen ovan. Följande är en kort uppräkning av möjliga fel svar.

| Statuskod | Fel Orsak | Så här hanterar du |
| ----------- | ------------ | ------------- |
| 404 hittades inte. | Okänd verifieringsmetod, eller så har programmet inte tilldelats någon hanterad identitet. | Rätta till programinstallationen eller koden för token-hämtning. |
| 429 för många begär Anden. |  Begränsnings gränsen har nåtts, vilket införs av AAD eller SF. | Försök igen med exponentiell backoff. Se rikt linjer nedan. |
| 4xx-fel i begäran. | En eller flera av parametrarna för begäran var felaktiga. | Försök inte igen.  Granska fel informationen för mer information.  4xx-fel är design tids fel.|
| 5xx-fel från tjänsten. | Under systemet för hanterad identitet eller Azure Active Directory returnerade ett tillfälligt fel. | Det är säkert att försöka igen efter en kort stund. Du kan träffa ett begränsnings villkor (429) när du försöker igen.|

Om ett fel inträffar innehåller motsvarande HTTP-svar ett JSON-objekt med fel information:

| Element | Beskrivning |
| ------- | ----------- |
| code | Felkod. |
| correlationId | Ett korrelations-ID som kan användas för fel sökning. |
| message | Utförlig beskrivning av felet. **Fel beskrivningar kan ändras när som helst. Är inte beroende av själva fel meddelandet.**|

Exempel fel:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Följande är en lista över vanliga Service Fabric fel som är speciella för hanterade identiteter:

| Kod | Message | Beskrivning | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Det gick inte att hitta hemligheten i begärandehuvuden. | Autentiserings koden har inte angetts med begäran. | 
| ManagedIdentityNotFound | Hanterad identitet hittades inte för den angivna program värden. | Programmet saknar identitet eller så är autentiserings koden okänd. |
| ArgumentNullOrEmpty | Parametern Resource får inte vara null eller en tom sträng. | Resursen (Audience) tillhandahölls inte i begäran. |
| InvalidApiVersion | API-versionen stöds inte. Versionen som stöds är 2019-07-01-Preview. | Den API-version som saknas eller inte stöds anges i URI: n för begäran. |
| InternalServerError | Ett fel uppstod. | Ett fel påträffades i under systemet för hanterad identitet, eventuellt utanför Service Fabric stacken. Den mest sannolika orsaken är ett felaktigt värde som har angetts för resursen (Sök efter efterföljande '/'?) | 

## <a name="retry-guidance"></a>Vägledning för nytt försök 

Den enda felkoden som kan återförsök är vanligt vis 429 (för många begär Anden). interna Server fel/5xx fel koder kan gå att försöka igen, även om orsaken kan vara permanent. 

Begränsnings gränser gäller för det antal anrop som görs till under systemet för hanterad identitet – särskilt de överordnade beroenden (den hanterade identiteten Azure-tjänst eller Secure token service). Service Fabric cachelagrar token på olika nivåer i pipelinen, men med tanke på de berörda komponenternas distribuerade egenskaper kan anroparen uppleva inkonsekventa begränsnings svar (d.v.s. begränsas av en nod/instans av ett program, men inte på en annan nod när du begär en token för samma identitet.) När begränsnings villkoret har angetts kan efterföljande förfrågningar från samma program Miss lyckas med HTTP-statuskod 429 (för många begär Anden) tills villkoret är avmarkerat.  

Vi rekommenderar att begär Anden som Miss lyckas på grund av begränsningen provas med en exponentiell backoff, enligt följande: 

| Anrops index | Åtgärd vid mottagande av 429 | 
| --- | --- | 
| 1 | Vänta 1 sekund och försök igen |
| 2 | Vänta 2 sekunder och försök igen |
| 3 | Vänta 4 sekunder och försök igen |
| 4 | Vänta 8 sekunder och försök igen |
| 4 | Vänta 8 sekunder och försök igen |
| 5 | Vänta 16 sekunder och försök igen |

## <a name="resource-ids-for-azure-services"></a>Resurs-ID för Azure-tjänster
Se [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-msi.md) för en lista över resurser som stöder Azure AD och deras respektive resurs-ID.

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användardefinierad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Bevilja ett Azure Service Fabric program åtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)