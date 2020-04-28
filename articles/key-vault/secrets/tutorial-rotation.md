---
title: Självstudie med en rotation med enkel användare/enstaka lösen ord
description: I den här självstudien får du lära dig hur du automatiserar rotationen av en hemlighet för resurser som använder autentisering med enkel användare eller enstaka lösen ord.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f9c0dca29d173eb2c7893a20b2ab41dd31522e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183219"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>Automatisera rotationen av en hemlighet för resurser som använder autentisering med enkel användare/enstaka lösen ord

Det bästa sättet att autentisera till Azure-tjänster är genom att använda en [hanterad identitet](../general/managed-identity.md), men det finns vissa scenarier där det inte är ett alternativ. I dessa fall används åtkomst nycklar eller hemligheter. Du bör regelbundet rotera åtkomst nycklar eller hemligheter.

Den här självstudien visar hur du automatiserar den periodiska rotationen av hemligheter för databaser och tjänster som använder autentisering med enkel användare eller enstaka lösen ord. Mer specifikt roterar SQL Server lösen ord som lagras i Azure Key Vault genom att använda en funktion som utlöses av Azure Event Grid meddelande:

![Diagram över rotations lösning](../media/rotate1.png)

1. Trettio dagar före utgångs datumet för en hemlighet, Key Vault publicerar händelsen "nära förfallo datum" för att Event Grid.
1. Event Grid kontrollerar händelse prenumerationerna och använder HTTP POST för att anropa funktions-app-slutpunkten som prenumereras på händelsen.
1. Function-appen tar emot hemlig information, genererar ett nytt slumpmässigt lösen ord och skapar en ny version av hemligheten med det nya lösen ordet i Key Vault.
1. Funktions programmet uppdaterar SQL Server med det nya lösen ordet.

> [!NOTE]
> Det kan finnas en fördröjning mellan steg 3 och 4. Under den tiden kan hemligheten i Key Vault inte autentisera till SQL Server. Om något av stegen Miss lyckas Event Grid försök i två timmar.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Skapa ett nyckel valv och SQL Server instans

Det första steget är att skapa ett nyckel valv och en SQL Server instans och databas och lagra SQL Server administratörs lösen ord i Key Vault.

I den här självstudien används en befintlig Azure Resource Manager-mall för att skapa komponenter. Du kan hitta koden här: exempel på en [mall för grundläggande hemlighet](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Välj distributions länk för Azure-mallar:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Under **resurs grupp**väljer du **Skapa ny**. Namnge gruppen **simplerotation**.
1. Välj **Köp**.

    ![Skapa en resursgrupp](../media/rotate2.png)

Nu har du ett nyckel valv, en SQL Server-instans och en SQL-databas. Du kan kontrol lera den här installationen i Azure CLI genom att köra följande kommando:

```azurecli
az resource list -o table
```

Resultatet ser ut ungefär så här:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Skapa sedan en Function-app med en Systemhanterad identitet, förutom de andra nödvändiga komponenterna.

Function-appen kräver följande komponenter:
- En Azure App Service plan
- Ett lagringskonto
- En åtkomst princip för att få åtkomst till hemligheter i Key Vault via funktion appens hanterade identitet

1. Välj distributions länk för Azure-mallar:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. I listan **resurs grupp** väljer du **simplerotation**.
1. Välj **Köp**.

   ![Välj Köp](../media/rotate3.png)

När du har slutfört föregående steg har du ett lagrings konto, en Server grupp och en Function-app. Du kan kontrol lera den här installationen i Azure CLI genom att köra följande kommando:

```azurecli
az resource list -o table
```

Resultatet ser ut ungefär så här:

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

Information om hur du skapar en Function-app och använder hanterad identitet för att få åtkomst till Key Vault finns i [skapa en Function-app från Azure Portal](../../azure-functions/functions-create-function-app-portal.md) och [tillhandahålla Key Vault autentisering med en hanterad identitet](../general/managed-identity.md).

### <a name="rotation-function"></a>Rotations funktion
Funktionen använder en händelse för att utlösa rotationen av en hemlighet genom att uppdatera Key Vault och SQL-databasen.

#### <a name="function-trigger-event"></a>Funktions Utlös ande händelse

Den här funktionen läser händelse data och kör rotations logiken:

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
Den här rotations metoden läser databas information från hemligheten, skapar en ny version av hemligheten och uppdaterar databasen med den nya hemligheten:

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

#### <a name="function-deployment"></a>Funktions distribution

1. Hämta zip-filen för funktionen app från [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Ladda upp filen simplerotationsample-FN. zip till Azure Cloud Shell.

   ![Ladda upp filen](../media/rotate4.png)
1. Använd det här Azure CLI-kommandot för att distribuera zip-filen till Function-appen:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

När funktionen har distribuerats bör du se två funktioner under simplerotation-FN:

![Funktionerna SimpleRotation och SimpleRotationHttpTest](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Lägg till en händelse prenumeration för SecretNearExpiry-händelsen

Kopiera funktions appens `eventgrid_extension` nyckel:

   ![Välja inställningar för funktionsapp](../media/rotate6.png)

   ![eventgrid_extension nyckel](../media/rotate7.png)

Använd den kopierade `eventgrid_extension` nyckeln och ditt PRENUMERATIONS-ID i följande kommando för att skapa en `SecretNearExpiry` Event Grid prenumeration för händelser:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Lägg till hemligheten i Key Vault
Ange åtkomst principen för att ge användare behörighet att *Hantera hemligheter* :

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Skapa en ny hemlighet med taggar som innehåller data källan för SQL Database och användar-ID: t. Ange ett förfallo datum som har angetts för imorgon.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Att skapa en hemlighet med ett kort utgångs datum publicerar `SecretNearExpiry` omedelbart en händelse, vilket i sin tur utlöser funktionen för att rotera hemligheten.

## <a name="test-and-verify"></a>Testa och verifiera
Efter några minuter bör `sqluser` hemligheten roteras automatiskt.

Om du vill kontrol lera att hemligheten har roterats går du till **Key Vault** > **hemligheter**:

![Gå till hemligheter](../media/rotate8.png)

Öppna **sqluser** -hemligheten och Visa de ursprungliga och roterade versionerna:

![Öppna sqluser-hemligheten](../media/rotate9.png)

### <a name="create-a-web-app"></a>Skapa en webbapp

Om du vill verifiera SQL-autentiseringsuppgifterna skapar du en webbapp. Den här webbappen hämtar hemligheten från Key Vault, extraherar information om SQL-databasen och autentiseringsuppgifterna från hemligheten och testa anslutningen till SQL Server.

Webb programmet kräver följande komponenter:
- En webbapp med Systemhanterad identitet
- En åtkomst princip för att få åtkomst till hemligheter i Key Vault via hanterad identitet för webbappar

1. Välj distributions länk för Azure-mallar:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Välj resurs gruppen **simplerotation** .
1. Välj **Köp**.

### <a name="deploy-the-web-app"></a>Distribuera webbappen

Du hittar käll koden för webb programmet på [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Slutför följande steg för att distribuera webbappen:

1. Hämta zip-filen för funktionen app från [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Ladda upp filen simplerotationsample-app. zip till Azure Cloud Shell.
1. Använd det här Azure CLI-kommandot för att distribuera zip-filen till Function-appen:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Öppna webbapp

Gå till det distribuerade programmet och välj URL: en:
 
![Välj URL](../media/rotate10.png)

När programmet öppnas i webbläsaren visas det **genererade hemliga värdet** och värdet *Sant*för **databas anslutet** .

## <a name="learn-more"></a>Läs mer

- Översikt: [övervaka Key Vault med Azure Event Grid (förhands granskning)](../general/event-grid-overview.md)
- Gör så här: [ta emot e-post när hemliga nyckel valv ändringar](../general/event-grid-logicapps.md)
- [Azure Event Grid händelse schema för Azure Key Vault (förhands granskning)](../../event-grid/event-schema-key-vault.md)
