---
title: Så här använder du en systemtilldelad hanterad identitet för att komma åt Azure Cosmos DB-data
description: Lär dig hur du konfigurerar en Azure AD-systemtilldelade hanterade identitet för att komma åt nycklar från Azure Cosmos DB. msi, hanterad tjänstidentitet, aad, azure active directory, identitet
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 102efba5d028eef621f392ef1739ea9ebeca0b44
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80417238"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>Så här använder du en systemtilldelad hanterad identitet för att komma åt Azure Cosmos DB-data

I den här artikeln ska du ställa in en **robust, nyckelrotationsoberoende,** lösning för att komma åt Azure Cosmos DB-nycklar genom att utnyttja [hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). I exemplet i den här artikeln används en Azure-funktion. Du kan dock uppnå den här lösningen genom att använda alla tjänster som stöder hanterade identiteter. 

Du får lära dig hur du skapar en Azure-funktion som kan komma åt Azure Cosmos DB utan att behöva kopiera några Azure Cosmos DB-nycklar. Funktionen kommer att vakna varje minut och registrera den aktuella temperaturen på ett akvarium akvarium. Mer information om hur du konfigurerar en timerutlöst Azure-funktion finns [i skapa en funktion i Azure som utlöses av en timerartikel.](../azure-functions/functions-create-scheduled-function.md)

För att förenkla scenariot hanteras rensning av äldre temperaturdokument av en redan konfigurerad [time to live-inställning.](./time-to-live.md) 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Tilldela en systemtilldelad hanterad identitet till en Azure-funktion

I det här steget ska du tilldela en systemtilldelad hanterad identitet till din Azure-funktion.

1. Öppna fönstret **Azure-funktion i** [Azure-portalen](https://portal.azure.com/)och navigera till din funktionsapp. 

1. Öppna fliken **Plattformsfunktioner** > **Identitet:** 

   ![Fliken Identitet](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. På fliken **Identitet** **aktiverar** du **statusen Systemidentitet.** Var noga med att välja **Spara**och bekräfta att du vill aktivera systemidentiteten. I slutet bör **fönstret Systemidentitet** se ut på följande sätt:  

   ![Systemidentitet aktiverat](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>Bevilja hanterad identitetsåtkomst till ditt Azure Cosmos-konto

I det här steget ska du tilldela en roll till Azure-funktionens systemtilldelade hanterade identitet. Azure Cosmos DB har flera inbyggda roller som du kan tilldela till den hanterade identiteten. För den här lösningen ska du använda följande två roller:

|Inbyggd roll  |Beskrivning  |
|---------|---------|
|[Deltagare på DocumentDB-konto](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan hantera Azure Cosmos DB-konton. Tillåter hämtning av läs-/skrivnycklar. |
|[Cosmos DB-kontoläsare](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan läsa Azure Cosmos DB-kontodata. Tillåter hämtning av läsnycklar. |

> [!IMPORTANT]
> RBAC-stöd i Azure Cosmos DB gäller endast för styrplansoperationer. Dataplanåtgärder skyddas med hjälp av huvudnycklar eller resurstoken. Mer information finns i artikeln [Säker åtkomst till data.](secure-access-to-data.md)

> [!TIP] 
> När du tilldelar roller tilldelar du bara den åtkomst som behövs. Om din tjänst bara kräver läsdata tilldelar du den hanterade identiteten till rollen **Cosmos DB Account Reader.** Mer information om vikten av minsta behörighetsåtkomst finns i den lägre exponeringen av artikeln [med privilegierade konton.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

För ditt scenario kommer du att läsa temperaturen och sedan skriva tillbaka dessa data till en behållare i Azure Cosmos DB. Eftersom du måste skriva data använder du rollen **DocumentDB Account Contributor.** 

1. Logga in på Azure-portalen och navigera till ditt Azure Cosmos DB-konto. Öppna **IAM-fönstret (Access Management)** och sedan fliken **Rolltilldelningar:**

   ![IAM-fönster](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Välj knappen **+ Lägg till** och lägg sedan till **rolltilldelning**.

1. Panelen **Lägg till rolluppgift** öppnas till höger:

   ![Lägg till roll](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Roll** - Välj **DocumentDB-kontodeltagare**
   * **Tilldela åtkomst till** - Under underavsnittet Välj **systemtilldelade hanterade identitet** väljer du **Funktionsapp**.
   * **Välj** - Fönstret fylls i med alla funktionsappar i din prenumeration som har en **hanterad systemidentitet**. I vårt fall väljer jag **SummaryService** funktion app: 

      ![Välj tilldelning](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. När funktionsappens identitet har valts klickar du på **Spara**.

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Programmässigt komma åt Azure Cosmos DB-nycklarna från Azure-funktionen

Nu har vi en funktionsapp som har en systemtilldelad hanterad identitet. Den identiteten får rollen **DocumentDB Account Contributor** i Azure Cosmos DB-behörigheterna. Följande funktionsappkod får Azure Cosmos DB-nycklar, skapar ett CosmosClient-objekt, får temperaturen och sparar sedan den i Cosmos DB.

Det här exemplet använder [API:et för listnycklar](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) för att komma åt dina Azure Cosmos DB-kontonycklar.

> [!IMPORTANT] 
> Om du vill tilldela rollen [ **Cosmos DB-kontoläsare** ](#grant-the-managed-identity-access-to-your-azure-cosmos-account) måste du använda [api:et för](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)skrivskyddade listnycklar . Detta fyller bara på skrivskyddade nycklar.

API:et `DatabaseAccountListKeysResult` för listnycklar returnerar objektet. Den här typen definieras inte i C#-biblioteken. Följande kod visar implementeringen av den här klassen:  

```csharp 
namespace SummarizationService 
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

Du kommer att använda [microsoft.Azure.Services.AppAuthentication-biblioteket](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) för att hämta den systemtilldelade hanterade identitetstoken. Mer information om andra sätt att hämta `Microsoft.Azure.Service.AppAuthentication` token och mer information om biblioteket finns i artikeln Autentisering av tjänst till [tjänst.](../key-vault/service-to-service-authentication.md)

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
    public static class TemperatureMonitor
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

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
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
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Du är nu redo att [distribuera din Azure-funktion](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Nästa steg

* [Certifikatbaserad autentisering med Azure Cosmos DB och Active Directory](certificate-based-authentication.md)
* [Skydda Azure Cosmos-nycklar med Azure Key Vault](access-secrets-from-keyvault.md)
* [Säkerhetsbaslinje för Azure Cosmos DB](security-baseline.md)
