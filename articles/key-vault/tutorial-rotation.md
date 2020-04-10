---
title: Självstudiekurs för rotation med en användare/ett lösenord
description: Använd den här självstudien om du vill lära dig hur du automatiserar rotationen av en hemlighet för resurser som använder autentisering med en användare/ett lösenord.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 684bfccd698217acdafdaac8c33f1e5531cdb4bf
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998899"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>Automatisera rotationen av en hemlighet för resurser som använder autentisering med en användare/ett lösenord

Det bästa sättet att autentisera till Azure-tjänster är att använda en [hanterad identitet](managed-identity.md), men det finns vissa scenarier där det inte är ett alternativ. I sådana fall används åtkomstnycklar eller hemligheter. Du bör regelbundet rotera åtkomstnycklar eller hemligheter.

Den här självstudien visar hur du automatiserar den periodiska rotationen av hemligheter för databaser och tjänster som använder autentisering med en användare/ett lösenord. Den här självstudien roterar SQL Server-lösenord som lagras i Azure Key Vault med hjälp av en funktion som utlöses av Azure Event Grid-meddelande:

![Diagram över rotationslösning](./media/rotate1.png)

1. Trettio dagar före utgångsdatumet för en hemlighet publicerar Key Vault händelsen "nära utgång" till Event Grid.
1. Event Grid kontrollerar händelseprenumerationerna och använder HTTP POST för att anropa funktionsappens slutpunkt som prenumererar på händelsen.
1. Funktionsappen tar emot den hemliga informationen, genererar ett nytt slumpmässigt lösenord och skapar en ny version för hemligheten med det nya lösenordet i Key Vault.
1. Funktionsappen uppdaterar SQL Server med det nya lösenordet.

> [!NOTE]
> Det kan finnas en fördröjning mellan steg 3 och 4. Under den tiden kan hemligheten i Key Vault inte autentisera till SQL Server. I händelse av ett fel i något av stegen försöker Event Grid i två timmar.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Skapa en nyckelvalv och SQL Server-instans

Det första steget är att skapa ett nyckelvalv och en SQL Server-instans och databas och lagra SQL Server-administratörslösenordet i Key Vault.

Den här självstudien använder en befintlig Azure Resource Manager-mall för att skapa komponenter. Koden hittar du här: Exempel på [grundläggande hemlig rotationsmall](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Välj distributionslänken för Azure-mall:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Under **Resursgrupp**väljer du **Skapa ny**. Namnge **gruppens enkelrotation**.
1. Välj **Köp**.

    ![Skapa en resursgrupp](./media/rotate2.png)

Du har nu ett nyckelvalv, en SQL Server-instans och en SQL-databas. Du kan verifiera den här inställningen i Azure CLI genom att köra följande kommando:

```azurecli
az resource list -o table
```

Resultatet kommer att se något följande utgång:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Skapa sedan en funktionsapp med en systemhanterad identitet, utöver de andra nödvändiga komponenterna.

Funktionsappen kräver följande komponenter:
- En Azure App Service-plan
- Ett lagringskonto
- En åtkomstprincip för åtkomst till hemligheter i Key Vault via funktionsapphanterad identitet

1. Välj distributionslänken för Azure-mall:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Välj **enkelrotation**i listan **Resursgrupp** .
1. Välj **Köp**.

   ![Välj Inköp](./media/rotate3.png)

När du har slutfört föregående steg har du ett lagringskonto, en servergrupp och en funktionsapp. Du kan verifiera den här inställningen i Azure CLI genom att köra följande kommando:

```azurecli
az resource list -o table
```

Resultatet kommer att se ut ungefär så här:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

Information om hur du skapar en funktionsapp och använder hanterad identitet för att komma åt Key Vault finns i [Skapa en funktionsapp från Azure-portalen](../azure-functions/functions-create-function-app-portal.md) och [Ange autentisering med nyckelvalv med en hanterad identitet](managed-identity.md).

### <a name="rotation-function"></a>Rotationsfunktion
Funktionen använder en händelse för att utlösa rotationen av en hemlighet genom att uppdatera Key Vault och SQL-databasen.

#### <a name="function-trigger-event"></a>Händelse för funktionsutlösare

Den här funktionen läser händelsedata och kör rotationslogiken:

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Logik för hemlig rotation
Den här rotationsmetoden läser databasinformation från hemligheten, skapar en ny version av hemligheten och uppdaterar databasen med den nya hemligheten:

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Du hittar den fullständiga koden på [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Funktionsdistribution

1. Hämta zip-filen för funktionsappen från [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Ladda upp filen simplerotationsample-fn.zip till Azure Cloud Shell.

   ![Ladda upp filen](./media/rotate4.png)
1. Använd det här Azure CLI-kommandot för att distribuera zip-filen till funktionsappen:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

När funktionen har distribuerats bör du se två funktioner under simplerotation-fn:

![SimpleRotation och SimpleRotationHttpTest funktioner](./media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Lägga till en händelseprenumeration för SecretNearExpiry-händelsen

Kopiera funktionsappens `eventgrid_extension` nyckel:

   ![Välja inställningar för funktionsapp](./media/rotate6.png)

   ![eventgrid_extension nyckel](./media/rotate7.png)

Använd den `eventgrid_extension` kopierade nyckeln och ditt prenumerations-ID i `SecretNearExpiry` följande kommando för att skapa en Event Grid-prenumeration för händelser:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Lägg till hemligheten i Key Vault
Ange din åtkomstprincip så att hantera hemligheter ger användare behörighet att *hantera hemligheter:*

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Skapa en ny hemlighet med taggar som innehåller SQL-databasdatakällan och användar-ID. Inkludera ett utgångsdatum som är inställt för i morgon.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Om du skapar en hemlighet med `SecretNearExpiry` ett kort utgångsdatum publiceras omedelbart en händelse, vilket i sin tur utlöser funktionen för att rotera hemligheten.

## <a name="test-and-verify"></a>Testa och verifiera
Efter några minuter `sqluser` ska hemligheten roteras automatiskt.

Om du vill kontrollera att hemligheten har roterat går du till **Key Vault** > **Secrets:**

![Gå till Hemligheter](./media/rotate8.png)

Öppna **sqluser** hemlighet och visa den ursprungliga och roterade versioner:

![Öppna sqluser hemligheten](./media/rotate9.png)

### <a name="create-a-web-app"></a>Skapa en webbapp

Skapa en webbapp om du vill verifiera SQL-autentiseringsuppgifterna. Den här webbappen hämtar hemligheten från Key Vault, extraherar SQL-databasinformation och autentiseringsuppgifter från hemligheten och testar anslutningen till SQL Server.

Webbappen kräver följande komponenter:
- En webbapp med systemhanterad identitet
- En åtkomstprincip för åtkomst till hemligheter i Key Vault via webbapphanterad identitet

1. Välj distributionslänken för Azure-mall:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Välj resursgruppen **enkelrotation.**
1. Välj **Köp**.

### <a name="deploy-the-web-app"></a>Distribuera webbappen

Du hittar källkoden för webbappen på [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Så här distribuerar du webbappen:

1. Hämta zip-filen för funktionsappen från [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Ladda upp filen simplerotationsample-app.zip till Azure Cloud Shell.
1. Använd det här Azure CLI-kommandot för att distribuera zip-filen till funktionsappen:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Öppna webbappen

Gå till det distribuerade programmet och välj URL:en:
 
![Välj URL:en](./media/rotate10.png)

Du bör se det genererade hemliga värdet med värdet Databasansluten för sant.

## <a name="learn-more"></a>Läs mer

- Översikt: [Övervaka nyckelvalv med Azure Event Grid (förhandsversion)](event-grid-overview.md)
- Så här får du [e-post när en nyckelvalvshemlighet ändras](event-grid-logicapps.md)
- [Azure Event Grid-händelseschema för Azure Key Vault (förhandsversion)](../event-grid/event-schema-key-vault.md)
