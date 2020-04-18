---
title: Så här använder du en systemtilldelad hanterad identitet för att komma åt Azure Cosmos DB-data
description: Lär dig hur du konfigurerar en Azure Active Directory (Azure AD) systemtilldelade hanterade identitet (hanterad tjänstidentitet) för att komma åt nycklar från Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641172"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Använda systemtilldelade hanterade identiteter för att komma åt Azure Cosmos DB-data

I den här artikeln ska du konfigurera en *robust, nyckelrotsaberoende* lösning för åtkomst till Azure Cosmos DB-nycklar med hjälp av [hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Exemplet i den här artikeln använder Azure Functions, men du kan använda alla tjänster som stöder hanterade identiteter. 

Du får lära dig hur du skapar en funktionsapp som kan komma åt Azure Cosmos DB-data utan att behöva kopiera några Azure Cosmos DB-nycklar. Funktionsappen vaknar varje minut och registrerar den aktuella temperaturen på en akvarium. Mer information om hur du konfigurerar en timerutlöst funktionsapp finns [i Skapa en funktion i Azure som utlöses av en timerartikel.](../azure-functions/functions-create-scheduled-function.md)

För att förenkla scenariot är en [inställning för tid till live](./time-to-live.md) redan konfigurerad för att rensa äldre temperaturdokument. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Tilldela en systemtilldelad hanterad identitet till en funktionsapp

I det här steget ska du tilldela en systemtilldelad hanterad identitet till din funktionsapp.

1. Öppna fönstret **Azure-funktion i** [Azure-portalen](https://portal.azure.com/)och gå till din funktionsapp. 

1. Öppna fliken **Plattformsfunktioner** > **Identitet:** 

   ![Skärmbild som visar plattformsfunktioner och identitetsalternativ för funktionsappen.](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. På fliken **Identitet** **aktiverar** du systemidentitetsstatus och väljer **Spara**. **Status** **Fönstret Identitet** bör se ut så här:  

   ![Skärmbild som visar systemidentitetsstatus inställd på På.](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>Bevilja åtkomst till ditt Azure Cosmos-konto

I det här steget ska du tilldela en roll till funktionsappens systemtilldelade hanterade identitet. Azure Cosmos DB har flera inbyggda roller som du kan tilldela till den hanterade identiteten. För den här lösningen använder du följande två roller:

|Inbyggd roll  |Beskrivning  |
|---------|---------|
|[Deltagare på DocumentDB-konto](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan hantera Azure Cosmos DB-konton. Tillåter hämtning av läs-/skrivnycklar. |
|[Cosmos DB-kontoläsare](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan läsa Azure Cosmos DB-kontodata. Tillåter hämtning av läsnycklar. |

> [!IMPORTANT]
> Stöd för rollbaserad åtkomstkontroll i Azure Cosmos DB gäller endast för kontrollplansåtgärder. Dataplanåtgärder skyddas via huvudnycklar eller resurstoken. Mer information finns i artikeln [Säker åtkomst till data.](secure-access-to-data.md)

> [!TIP] 
> När du tilldelar roller tilldelar du bara den åtkomst som behövs. Om tjänsten bara kräver att du läser data tilldelar du rollen **Cosmos DB-kontoläsare** till den hanterade identiteten. Mer information om vikten av minsta behörighetsåtkomst finns i artikeln [Lägre exponering av privilegierade konton.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

I det här fallet kommer funktionsappen att läsa temperaturen i akvariet och sedan skriva tillbaka dessa data till en behållare i Azure Cosmos DB. Eftersom funktionsappen måste skriva data måste du tilldela rollen **DocumentDB Account Contributor.** 

1. Logga in på Azure-portalen och gå till ditt Azure Cosmos DB-konto. Öppna fönstret **IAM (Access Control)** och sedan fliken **Rolltilldelning:**

   ![Skärmbild som visar kontrollfönstret i Access och fliken Rolltilldelningar.](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Välj **+ Lägg till** > **rolltilldelning**.

1. Uppgiftspanelen Lägg till **roll** öppnas till höger:

   ![Skärmbild som visar fönstret Lägg till rolltilldelning.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Roll**: Välj **DocumentDB-kontodeltagare**
   * **Tilldela åtkomst till**: Under underavsnittet **Välj systemtilldelade hanterade identitet** väljer du **Funktionsapp**.
   * **Välj**: Fönstret fylls i med alla funktionsappar i prenumerationen som har en **hanterad systemidentitet**. I det här fallet väljer du funktionsappen **FishTankTemperatureService:** 

      ![Skärmbild som visar fönstret Lägg till rolltilldelning ifylld med exempel.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. När du har valt funktionsappen väljer du **Spara**.

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programmässigt komma åt Azure Cosmos DB-nycklar

Nu har vi en funktionsapp som har en systemtilldelad hanterad identitet med rollen **DocumentDB Account Contributor** i Azure Cosmos DB-behörigheterna. Följande funktionsappkod hämtar Azure Cosmos DB-nycklar, skapar ett CosmosClient-objekt, hämtar temperaturen i akvariet och sparar sedan detta i Azure Cosmos DB.

Det här exemplet använder [API:et för listnycklar](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) för att komma åt dina Azure Cosmos DB-kontonycklar.

> [!IMPORTANT] 
> Om du vill tilldela rollen [Cosmos DB-kontoläsare](#grant-access-to-your-azure-cosmos-account) måste du använda [API:et för skrivskyddade nycklar](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys). Detta kommer att fylla bara skrivskyddade nycklar.

API:et `DatabaseAccountListKeysResult` för listnycklar returnerar objektet. Den här typen definieras inte i C#-biblioteken. Följande kod visar implementeringen av den här klassen:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

I exemplet används också ett enkelt dokument som heter "TemperatureRecord", som definieras på följande sätt:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Du använder biblioteket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) för att hämta den systemtilldelade hanterade identitetstoken. Mer information om hur du hämtar token och `Microsoft.Azure.Service.AppAuthentication` mer information om biblioteket finns i [autentiseringsartikeln för tjänst.](../key-vault/general/service-to-service-authentication.md)


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Du är nu redo att [distribuera din funktionsapp](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Nästa steg

* [Certifikatbaserad autentisering med Azure Cosmos DB och Azure Active Directory](certificate-based-authentication.md)
* [Säkra Azure Cosmos DB-nycklar med Azure Key Vault](access-secrets-from-keyvault.md)
* [Säkerhetsbaslinje för Azure Cosmos DB](security-baseline.md)
