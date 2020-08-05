---
title: Rotations guide för resurser med en uppsättning autentiseringsuppgifter för autentisering som lagras i Azure Key Vault
description: I den här självstudien får du lära dig hur du automatiserar rotationen av en hemlighet för resurser som använder en uppsättning autentiseringsuppgifter för autentisering.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: b61ba7f160d012cc3d9ad9f477e969a626fdc38e
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541427"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatisera rotationen av en hemlighet för resurser som använder en uppsättning autentiseringsuppgifter för autentisering

Det bästa sättet att autentisera till Azure-tjänster är genom att använda en [hanterad identitet](../general/managed-identity.md), men det finns vissa scenarier där det inte är ett alternativ. I dessa fall används åtkomst nycklar eller hemligheter. Du bör regelbundet rotera åtkomst nycklar eller hemligheter.

Den här självstudien visar hur du automatiserar den periodiska rotationen av hemligheter för databaser och tjänster som använder en uppsättning autentiseringsuppgifter för autentisering. Mer specifikt roterar SQL Server lösen ord som lagras i Azure Key Vault genom att använda en funktion som utlöses av Azure Event Grid meddelande:

![Diagram över rotations lösning](../media/rotate1.png)

1. Trettio dagar före utgångs datumet för en hemlighet, Key Vault publicerar händelsen "nära förfallo datum" för att Event Grid.
1. Event Grid kontrollerar händelse prenumerationerna och använder HTTP POST för att anropa funktions-app-slutpunkten som prenumereras på händelsen.
1. Function-appen tar emot hemlig information, genererar ett nytt slumpmässigt lösen ord och skapar en ny version av hemligheten med det nya lösen ordet i Key Vault.
1. Funktions programmet uppdaterar SQL Server med det nya lösen ordet.

> [!NOTE]
> Det kan finnas en fördröjning mellan steg 3 och 4. Under den tiden kan hemligheten i Key Vault inte autentisera till SQL Server. Om något av stegen Miss lyckas Event Grid försök i två timmar.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault
* SQL Server

Nedan kan du använda distributions länken, om du inte har befintliga Key Vault och SQL Server:

[![Bild som visar en knapp med etiketten "distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Under **resurs grupp**väljer du **Skapa ny**. Namnge gruppen **akvrotation**.
1. Under **SQL admin-inloggning**skriver du SQL Administrator inloggnings namn. 
1. Välj **Granska + skapa**.
1. Välj **Skapa**

    ![Skapa en resursgrupp](../media/rotate2.png)

Nu har du en Key Vault och en SQL Server instans. Du kan kontrol lera den här installationen i Azure CLI genom att köra följande kommando:

```azurecli
az resource list -o table
```

Resultatet ser ut ungefär så här:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv          akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Skapa och distribuera SQL Server-funktionen för lösen ords rotation

Skapa sedan en Function-app med en Systemhanterad identitet, förutom de andra nödvändiga komponenterna, och distribuera funktioner för SQL Server-lösenords rotation

Function-appen kräver följande komponenter:
- En Azure App Service plan
- En Funktionsapp med SQL Password rotations funktioner med händelse utlösare och http-utlösare 
- Ett lagrings konto som krävs för hantering av funktions programs utlösare
- En åtkomst princip för Funktionsapp identitet för att få åtkomst till hemligheter i Key Vault
- En EventGrid händelse prenumeration för **SecretNearExpiry** -händelse

1. Välj distributions länk för Azure-mallar: 

   [![Bild som visar en knapp med etiketten "distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. I listan **resurs grupp** väljer du **akvrotation**.
1. I **SQL Server-namnet**anger du SQL Server-namnet med lösen ordet för att rotera
1. I **Key Vault namn**skriver du namnet på nyckel valvet
1. I **Funktionsapp namn**skriver du namnet på appens funktion
1. I **hemligt namn**skriver du det hemliga namnet där lösen ordet ska lagras
1. I **lagrings platsen-URL**skriver du funktions kod GitHub Location ( **https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp.git** )
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

   ![Välj granska + skapa](../media/rotate3.png)

När du har slutfört föregående steg har du ett lagrings konto, en Server grupp och en Function-app. Du kan kontrol lera den här installationen i Azure CLI genom att köra följande kommando:

```azurecli
az resource list -o table
```

Resultatet ser ut ungefär så här:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

Information om hur du skapar en Function-app och använder hanterad identitet för att få åtkomst till Key Vault finns i [skapa en Function-app från Azure Portal](../../azure-functions/functions-create-function-app-portal.md) och [tillhandahålla Key Vault autentisering med en hanterad identitet](../general/managed-identity.md).

### <a name="rotation-function"></a>Rotations funktion
Distribuerad i föregående steg-funktion använder en händelse för att utlösa rotationen av en hemlighet genom att uppdatera Key Vault och SQL-databasen. 

#### <a name="function-trigger-event"></a>Funktions Utlös ande händelse

Den här funktionen läser händelse data och kör rotations logiken:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Logik för hemlig rotation
Den här rotations metoden läser databas information från hemligheten, skapar en ny version av hemligheten och uppdaterar databasen med den nya hemligheten:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
Du hittar den fullständiga koden på [GitHub](https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp).

## <a name="add-the-secret-to-key-vault"></a>Lägg till hemligheten i Key Vault
Ange åtkomst principen för att ge användare behörighet att *Hantera hemligheter* :

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Skapa en ny hemlighet med taggar som innehåller SQL Server resurs-ID, SQL Server inloggnings namn och giltighets period för hemligheten i dagar. Ange namnet på hemligheten, det ursprungliga lösen ordet från SQL Database (i vårt exempel "Simple123") och ange ett förfallo datum som har angetts för morgon dagen.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

Att skapa en hemlighet med ett kort utgångs datum kommer att publicera en `SecretNearExpiry` händelse inom 15 minuter, vilket i sin tur utlöser funktionen för att rotera hemligheten.

## <a name="test-and-verify"></a>Testa och verifiera

Om du vill kontrol lera att hemligheten har roterats går du till **Key Vault**  >  **hemligheter**:

![Gå till hemligheter](../media/rotate8.png)

Öppna **sqlPassword** -hemligheten och Visa de ursprungliga och roterade versionerna:

![Öppna sqluser-hemligheten](../media/rotate9.png)

### <a name="create-a-web-app"></a>Skapa en webbapp

Om du vill verifiera SQL-autentiseringsuppgifterna skapar du en webbapp. Den här webbappen hämtar hemligheten från Key Vault, extraherar information om SQL-databasen och autentiseringsuppgifterna från hemligheten och testa anslutningen till SQL Server.

Webb programmet kräver följande komponenter:
- En webbapp med Systemhanterad identitet
- En åtkomst princip för att få åtkomst till hemligheter i Key Vault via hanterad identitet för webbappar

1. Välj distributions länk för Azure-mallar: 

   [![Bild som visar en knapp med etiketten "distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmaster%2Farm-templates%2FWeb-App%2Fazuredeploy.json)

1. Välj resurs gruppen **akvrotation** .
1. I **SQL Server-namnet**anger du SQL Server-namnet med lösen ordet för att rotera
1. I **Key Vault namn**skriver du namnet på nyckel valvet
1. I **hemligt namn**skriver du ett hemligt namn där lösen ordet lagras
1. I **lagrings platsen-URL**skriver du Web App Code GitHub Location ( **https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** )
1. Välj **Granska + skapa**.
1. Välj **Skapa**.


### <a name="open-the-web-app"></a>Öppna webbapp

Gå till den distribuerade program-URL: en:
 
https://akvrotation-app.azurewebsites.net/

När programmet öppnas i webbläsaren visas det **genererade hemliga värdet** och värdet *Sant*för **databas anslutet** .

## <a name="learn-more"></a>Mer information

- Självstudie: [rotation för resurser med två uppsättningar autentiseringsuppgifter](tutorial-rotation-dual.md)
- Översikt: [övervaka Key Vault med Azure Event Grid (förhands granskning)](../general/event-grid-overview.md)
- Gör så här: [ta emot e-post när hemliga nyckel valv ändringar](../general/event-grid-logicapps.md)
- [Azure Event Grid händelse schema för Azure Key Vault (förhands granskning)](../../event-grid/event-schema-key-vault.md)
