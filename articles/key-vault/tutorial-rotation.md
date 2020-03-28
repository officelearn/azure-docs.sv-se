---
title: Självstudiekurs för rotation för en användare/lösenord
description: Använd den här självstudien för att automatisera rotation av en användare/lösenord
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239373"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Automatisera rotationen av en hemlighet för resurser med autentisering av en användare/lösenord

Även om det bästa sättet att autentisera till Azure-tjänster är att använda en [hanterad identitet,](managed-identity.md)finns det vissa scenarier där detta inte är ett alternativ. I dessa fall används åtkomstnycklar eller hemligheter. Åtkomstnycklar eller hemligheter bör roteras med jämna mellanrum.

Den här självstudien visar hur du automatiserar den periodiska rotationen av hemligheter för databaser och tjänster med autentisering av en användare/lösenord. I det här scenariot roteras SQL-serverlösenord som lagras i nyckelvalvet med hjälp av en funktion som utlöses av händelserutnätsmeddelande:

![Rotationsdiagram](./media/rotate1.png)

1. Trettio dagar före utgångsdatumet för en hemlighet publicerar Key Vault händelsen "nära utgång" till Event Grid.
1. Event Grid kontrollerar händelseprenumerationerna och anropar slutpunkten för funktionsappen som prenumererar på den här händelsen med hjälp av http-inlägg.
1. Funktionsappen tar emot den hemliga informationen, genererar ett nytt slumpmässigt lösenord och skapar en ny version för hemligheten med ett nytt lösenord i Key Vault.
1. Funktionen App uppdaterar SQL med nytt lösenord.

> [!NOTE]
> Det kan finnas en fördröjning mellan steg 3 och 4 och under den tiden hemlighet i Key Vault skulle inte vara giltig för att autentisera till SQL. I händelse av fel i något av stegen event grid försöker i 2 timmar.

## <a name="setup"></a>Installation

## <a name="create-a-key-vault-and-sql-server"></a>Skapa ett nyckelvalv och en SQL-server

Innan vi börjar måste vi skapa ett Nyckelvalv, skapa en SQL Server och databas och lagra SQL Server-administratörslösenordet i Key Vault.

Den här självstudien använder en förskapad Azure Resource Manager-mall för att skapa komponenter. Du hittar hela koden här: [Grundläggande secret rotation mall exempel](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Klicka på länken för distribution av Azure-mall:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. För "Resursgrupp" väljer du "Skapa ny" och ger den namnet "simplerotation".
1. Välj "Köp".

    ![Skapa ny resursgrupp](./media/rotate2.png)

När du har slutfört de här stegen har du ett nyckelvalv, en SQL-server och en SQL-databas. Du kan verifiera detta i en Azure CLI-terminal genom att köra:

```azurecli
az resource list -o table
```

Resultaten kommer att se något detta:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Skapa funktionsapp

Skapa en funktionsapp med en systemhanterad identitet samt ytterligare nödvändiga komponenter: 

Funktionsappen kräver komponenter och konfiguration under komponenter och konfiguration:
- App Service-plan
- Lagringskonto
- Åtkomstprincip för åtkomst till hemligheter i Key Vault med funktionsapphanterad identitet

1. Klicka på länken för distribution av Azure-mall:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. För "Resursgrupp" väljer du "simplerotation".
1. Välj "Köp".

   ![Skärmen Köp](./media/rotate3.png)

När du har slutfört stegen ovan har du ett lagringskonto, en servergrupp och en funktionsapp.  Du kan verifiera detta i en Azure CLI-terminal genom att köra:

```azurecli
az resource list -o table
```

Resultaten kommer att se något detta:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

Information om hur du skapar Funktionsapp och använda hanterad identitet för att komma åt Key Vault finns i [Skapa en funktionsapp från Azure-portalen](../azure-functions/functions-create-function-app-portal.md) och [Tillhandahålla nyckelvalvautentisering med en hanterad identitet](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>Rotationsfunktion och distribution
Funktionen använder händelsen som utlösare och utför rotation av en hemlig uppdatering av Key Vault och SQL-databas.

#### <a name="function-event-trigger-handler"></a>Utlösare för funktionshändelse

Nedan Funktionen läser händelsedata och kör rotationslogik

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
Den här rotationsmetoden läser databasinformation från hemligheten, skapar en ny version av hemligheten och uppdaterar databasen med en ny hemlighet.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Du hittar hela koden här:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Funktionsdistribution

1. Ladda ner funktionen app zip-fil:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Ladda upp filen simplerotationsample-fn.zip till Azure Cloud Shell.
 
1. Använd under CLI-kommandot för att distribuera zip-fil till funktionsappen:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Skärmen Köp](./media/rotate4.png)

Efter distributionen bör du märka två funktioner under simplerotation-fn:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Lägg till händelseprenumeration för händelsen "SecretNearExpiry"

Kopiera funktionsappens eventgrid_extension.Copy the function app eventgrid_extension key.

![Azure Cloud Shell](./media/rotate6.png)

![Testa och verifiera](./media/rotate7.png)

Använd den kopierade händelseförslagsnyckeln och ditt prenumerations-ID i kommandot nedan för att skapa en prenumeration på ett händelserutnät för SecretNearExpiry-händelser.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Lägg till hemlighet i Key Vault
Ange din åtkomstprincip så att "hantera hemligheter" behörighet till användare.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Nu skapa en ny hemlighet med taggar som innehåller SQL Database datasource och användar-ID, med utgångsdatum som fastställts för i morgon.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Om du skapar en hemlighet med ett kort utgångsdatum publiceras omedelbart en SecretNearExpiry-händelse, vilket i sin tur utlöser funktionen för att rotera hemligheten.

### <a name="test-and-verify"></a>Testa och verifiera
Efter några minuter bör sqluser hemlighet rotera automatiskt.

Om du vill verifiera verifiering av hemlig rotation går du till Key Vault > Secrets

![Testa och verifiera](./media/rotate8.png)

Öppna "sqluser" hemlighet och visa den ursprungliga och roterade versionen

![Testa och verifiera](./media/rotate9.png)

## <a name="create-web-app"></a>Skapa webbapp

Skapa ett webbprogram om du vill verifiera SQL-autentiseringsuppgifter. Detta webbprogram kommer att få hemligheten från nyckel valv, extrahera SQL databas information och referenser från hemligheten och testa anslutningen till SQL.

Webbappen kräver komponenter och konfiguration nedan:
- Webbapp med systemhanterad identitet
- Åtkomstprincip för åtkomst till hemligheter i Key Vault med hjälp av webbapphanterad identitet

1. Klicka på länken för distribution av Azure-mall:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Välj resursgrupp **för enkelrotation**
1. Klicka på Köp

### <a name="deploy-web-app"></a>Distribuera Web App

Källkod för webbappen som https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp du hittar på För distribution av webbappen gör du följande:

1. Ladda ner funktionsapp zip-filen frånhttps://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Ladda upp `simplerotationsample-app.zip` filen till Azure Cloud Shell.
1. Använd det här Azure CLI-kommandot för att distribuera zip-filen till funktionsappen:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Öppna webbprogram

Gå till det distribuerade programmet och klicka på "URL":
 
![Testa och verifiera](./media/rotate10.png)

Det genererade hemliga värdet ska visas med databasansluten som true.

## <a name="learn-more"></a>Läs mer:

- Översikt: [Övervaka nyckelvalv med Azure Event Grid (förhandsversion)](event-grid-overview.md)
- Så här får du [e-post när en nyckelvalvshemlighet ändras](event-grid-logicapps.md)
- [Azure Event Grid-händelseschema för Azure Key Vault (förhandsversion)](../event-grid/event-schema-key-vault.md)
