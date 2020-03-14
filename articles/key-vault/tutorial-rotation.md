---
title: Självstudie för enkel användare/lösen ords rotation
description: Använd den här självstudien för att automatisera rotationen av enskild användare/lösen ord
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239373"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Automatisera rotationen av en hemlighet för resurser med autentisering med enskild användare/lösen ord

Det bästa sättet att autentisera till Azure-tjänster är att använda en [hanterad identitet](managed-identity.md), men det finns vissa scenarier där detta inte är ett alternativ. I dessa fall används åtkomst nycklar eller hemligheter. Åtkomst nycklar eller hemligheter bör roteras med jämna mellanrum.

Den här självstudien visar hur du automatiserar den periodiska rotationen av hemligheter för databaser och tjänster med autentisering med enskild användare/lösen ord. Mer specifikt roterar det här scenariot SQL Server-lösenord som lagras i Key Vault med hjälp av en funktion som utlöses av Event Grid-avisering:

![Rotations diagram](./media/rotate1.png)

1. Trettio dagar före utgångs datumet för en hemlighet Key Vault publicera händelsen "nära förfallo datum" för att Event Grid.
1. Event Grid kontrollerar händelse prenumerationerna och använder http post för att anropa den Funktionsapp slut punkten som prenumererar på den här händelsen.
1. Function-appen tar emot hemlig information, genererar ett nytt slumpmässigt lösen ord och skapar en ny version av hemligheten med ett nytt lösen ord i Key Vault.
1. Funktionen app uppdaterar SQL med nytt lösen ord.

> [!NOTE]
> Det kan finnas en fördröjning mellan steg 3 och 4 och under denna tids hemlighet i Key Vault skulle inte vara giltig för att autentisera till SQL. I händelse av ett problem med att utföra stegen Event Grid försök i två timmar.

## <a name="setup"></a>Konfiguration

## <a name="create-a-key-vault-and-sql-server"></a>Skapa ett nyckel valv och en SQL Server

Innan vi börjar måste vi skapa en Key Vault, skapa en SQL Server och en databas och lagra SQL Server administratörs lösen ord i Key Vault.

I den här självstudien används en redan skapad Azure Resource Manager mall för att skapa komponenter. Du hittar hela koden här: exempel på en [mall för grundläggande hemlighet](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Klicka på distributions länk för Azure-mall:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. För "resurs grupp" väljer du "Skapa ny" och ger den namnet "simplerotation".
1. Välj Köp.

    ![Skapa ny resurs grupp](./media/rotate2.png)

När du har slutfört de här stegen har du ett nyckel valv, en SQL-Server och en SQL-databas. Du kan kontrol lera detta i en Azure CLI-Terminal genom att köra:

```azurecli
az resource list -o table
```

Resultatet ser ut ungefär så här:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Skapa Funktionsapp

Skapa en Funktionsapp med en Systemhanterad identitet, samt de ytterligare nödvändiga komponenterna: 

Function-appen kräver följande komponenter och konfiguration:
- App Service-plan
- Lagringskonto
- Åtkomst princip för åtkomst till hemligheter i Key Vault med Funktionsapp hanterad identitet

1. Klicka på distributions länk för Azure-mall:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. För "resurs grupp" väljer du "simplerotation".
1. Välj Köp.

   ![Inköps skärmen](./media/rotate3.png)

När du har slutfört stegen ovan kommer du att ha ett lagrings konto, en Server grupp och en Funktionsapp.  Du kan kontrol lera detta i en Azure CLI-Terminal genom att köra:

```azurecli
az resource list -o table
```

Resultatet ser ut ungefär så här:

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

Information om hur du skapar Funktionsapp och använder hanterad identitet för att komma åt Key Vault finns i [skapa en Function-app från Azure Portal](../azure-functions/functions-create-function-app-portal.md) och [tillhandahålla Key Vault autentisering med en hanterad identitet](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>Rotations funktion och distribution
Funktionen använder händelsen som utlösare och utför rotationen av en hemlig uppdatering Key Vault och SQL Database.

#### <a name="function-event-trigger-handler"></a>Utlös ande hanterare för funktions händelse

Funktionen nedan läser händelse data och kör rotations logiken

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
Den här rotations metoden läser databas information från hemligheten, skapar en ny version av hemligheten och uppdaterar databasen med en ny hemlighet.

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
Du kan hitta hela koden här: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Funktions distribution

1. Hämta zip-filen för Function-appen: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Ladda upp filen simplerotationsample-FN. zip till Azure Cloud Shell.
 
1. Använd följande CLI-kommando för att distribuera zip-filen till Function-appen:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Inköps skärmen](./media/rotate4.png)

Efter distributionen bör du notera två funktioner under simplerotation-FN:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Lägg till händelse prenumeration för "SecretNearExpiry"-händelse

Kopiera Function-appens eventgrid_extension nyckel.

![Azure Cloud Shell](./media/rotate6.png)

![Testa och verifiera](./media/rotate7.png)

Använd den kopierade eventgrid-tilläggs nyckeln och ditt prenumerations-ID i nedanstående kommando för att skapa en Event Grid-prenumeration för SecretNearExpiry-händelser.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Lägg till hemlighet i Key Vault
Ange åtkomst principen för att ge användare behörigheten "Hantera hemligheter".

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Skapa nu en ny hemlighet med taggar som innehåller SQL-databasens data källa och användar-ID, med utgångs datum angivet för imorgon.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Att skapa en hemlighet med ett kort utgångs datum publicerar omedelbart en SecretNearExpiry-händelse, som i sin tur utlöser funktionen för att rotera hemligheten.

### <a name="test-and-verify"></a>Testa och verifiera
Efter några minuter bör sqluser-hemligheten roteras automatiskt.

Om du vill verifiera verifiering av hemliga rotationer går du till Key Vault > hemligheter

![Testa och verifiera](./media/rotate8.png)

Öppna hemligheten "sqluser" och visa den ursprungliga och roterade versionen

![Testa och verifiera](./media/rotate9.png)

## <a name="create-web-app"></a>Skapa webbapp

Om du vill verifiera SQL-autentiseringsuppgifter skapar du ett webb program. Det här webb programmet kommer att hämta hemligheten från Key Vault, extrahera information om SQL-databasen och autentiseringsuppgifterna från hemligheten och testa anslutningen till SQL.

Webbappen kräver följande komponenter och konfiguration:
- Webbapp med Systemhanterad identitet
- Åtkomst princip för att få åtkomst till hemligheter i Key Vault att använda webbappens hanterad identitet

1. Klicka på distributions länk för Azure-mall:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Välj resurs gruppen **simplerotation**
1. Klicka på Köp

### <a name="deploy-web-app"></a>Distribuera webbapp

Käll koden för webbappen som du hittar på https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp för distribution av webbappen, gör följande:

1. Hämta zip-filen för Function-appen från https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Ladda upp filen `simplerotationsample-app.zip` till Azure Cloud Shell.
1. Använd det här Azure CLI-kommandot för att distribuera zip-filen till Function-appen:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Öppna webb program

Gå till det distribuerade programmet och klicka på "URL":
 
![Testa och verifiera](./media/rotate10.png)

Det genererade hemliga värdet ska visas med databasen ansluten som sant.

## <a name="learn-more"></a>Läs mer:

- Översikt: [övervaka Key Vault med Azure Event Grid (förhands granskning)](event-grid-overview.md)
- Gör så här: [ta emot e-post när hemliga nyckel valv ändringar](event-grid-logicapps.md)
- [Azure Event Grid händelse schema för Azure Key Vault (förhands granskning)](../event-grid/event-schema-key-vault.md)
