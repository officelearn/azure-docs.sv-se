---
title: 'Snabbstart: Konfigurera och hämta en hemlighet från Azure Key Vault med hjälp av en nodwebbapp – Azure Key Vault | Microsoft Docs'
description: I den här snabbstarten konfigurerar du och hämtar en hemlighet från Azure Key Vault med hjälp av en .NET-webbapp
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 300ee1b01f346f7e1c118b76d64d0eda6e4d7934
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565555"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>Snabbstart: Konfigurera och hämta en hemlighet från Azure Key Vault med hjälp av en .NET-webbapp

I den här snabbstarten följer du stegen för att få ett Azure-webbprogram att läsa information från Azure Key Vault med hjälp av hanterade identiteter för Azure-resurser. Key Vault hjälper till att skydda informationen. Lär dig att:

* Skapa ett nyckelvalv.
* Lagra en hemlighet i nyckelvalvet.
* Hämta en hemlighet från nyckelvalvet.
* Skapa ett Azure-webbprogram.
* Aktivera en [hanterad tjänstidentitet](../active-directory/managed-identities-azure-resources/overview.md) för webbappen.
* Bevilja de behörigheter som krävs för att webbprogrammet ska kunna läsa data från nyckelvalvet.

Innan du fortsätter rekommenderar vi att du läser avsnittet om [grundbegreppen för Key Vault](key-vault-whatis.md#basic-concepts).

>[!NOTE]
>Key Vault är en central lagringsplats för programmeringsbaserad lagring av hemligheter. Det här kräver att program och användare först autentiseras mot Key Vault, dvs. presenterar en hemlighet. Den här första hemligheten måste roteras med jämna mellanrum för att uppfylla de rekommenderade säkerhetsmetoderna. 
>
>Med [hanterade tjänstidentiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) får program som körs i Azure en identitet som hanteras automatiskt av Azure. Det här löser *problemet med den första hemligheten* så att användare och program kan följa bästa praxis utan att behöva bekymra sig om roteringen av den första hemligheten.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* I Windows:
  * [Visual Studio 2017 version 15.7.3 eller senare](https://www.microsoft.com/net/download/windows) med följande arbetsbelastningar:
    * ASP.NET och webbutveckling
    * .NET Core plattformsoberoende utveckling
  * [.NET Core 2.1 SDK eller senare](https://www.microsoft.com/net/download/windows)

* På Mac:
  * Visa [nyheter i Visual Studio för Mac](https://visualstudio.microsoft.com/vs/mac/).

* Alla plattformar:
  * Git ([ladda ned](https://git-scm.com/downloads)).
  * En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 och senare. Det här är tillgängligt för Windows, Mac och Linux.

## <a name="log-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Välj ett resursgruppnamn och fyll i platshållaren.
I följande exempel skapas en resursgrupp i regionen USA, östra:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Den resursgrupp du nyss skapade används i hela den här artikeln.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Nu ska du skapa ett nyckelvalv i resursgruppen som du skapade i föregående steg. Ange följande information:

* Key Vault-namn: Namnet måste vara en sträng på 3 till 24 tecken och får endast innehålla 0–9, a–z, A–Z och ett bindestreck (-).
* Namn på resursgrupp.
* Plats: **USA, östra**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Du kan lagra en SQL-anslutningssträng eller annan information som du behöver skydda men göra tillgänglig för ditt program.

Skriv följande kommandon för att skapa en hemlighet i nyckelvalvet som kallas **AppSecret**. Den här hemligheten lagrar värdet **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Det här kommandot visar den hemliga informationen, inklusive URI:n. När du har slutfört de här stegen bör du ha en URI till en hemlighet i nyckelvalvet. Anteckna den här informationen. Du behöver den senare.

## <a name="clone-the-repo"></a>Klona lagringsplatsen

Klona lagringsplatsen för att skapa en lokal kopia där du kan redigera källan. Kör följande kommando:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Öppna och redigera lösningen

Redigera program.cs-filen för att köra exemplet med namnet på ditt specifika nyckelvalv:

1. Bläddra till mappen key-vault-dotnet-core-quickstart.
2. Öppna filen key-vault-dotnet-core-quickstart.sln i Visual Studio 2017.
3. Öppna Program.cs-filen och uppdatera platshållaren *YourKeyVaultName* (ditt nyckelvalvsnamn) med namnet på det nyckelvalv som du skapade tidigare.

Den här lösningen använder NuGet-biblioteken [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Kör appen

På huvudmenyn för Visual Studio 2017 väljer du **Debug** > **Start** without debugging (Felsökning > Starta utan felsökning). När webbläsaren visas går du till sidan **Om**. Värdet för **AppSecret** visas.

## <a name="publish-the-web-application-to-azure"></a>Publicera webbappen till Azure

Publicera den här appen till Azure för att se den live som webbapp, och också se att du kan hämta det hemliga värdet:

1. I Visual Studio väljer du projektet **key-vault-dotnet-core-quickstart**.
2. Välj **Publicera** > **Starta**.
3. Skapa en ny **apptjänst** och välj sedan **Publicera**.
4. Ändra namnet på appen till **keyvaultdotnetcorequickstart**.
5. Välj **Skapa**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>Aktivera en hanterad identitet för webbappen

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. [Hanterade identiteter för Azure-resurser (översikt)](../active-directory/managed-identities-azure-resources/overview.md) löser detta problem på ett enklare sätt genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden.

Kör kommandot assign-identity i Azure CLI för att skapa identiteten för det här programmet:

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>Det här kommandot fungerar på samma sätt som när du går till portalen och väljer **På** för inställningen **Identitet/Systemtilldelad** i egenskaperna för webbappen.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Tilldela behörigheter till ditt program att läsa hemligheter från Key Vault

Anteckna utdata när du publicerar programmet till Azure. Det ska vara i formatet:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Kör sedan det här kommandot med namnet på ditt nyckelvalv och värdet för **PrincipalId**:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

Nu när du kör programmet bör ditt hemliga värde hämtas. I det föregående kommandot ger du identiteten för App Service behörighet att utföra åtgärderna **get** (hämta) och **list** (lista) i ditt nyckelvalv.

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när du inte längre behöver dem. Det gör du genom att markera resursgruppen för den virtuella datorn och välja **Ta bort**.

Ta bort nyckelvalvet med hjälp av kommandot [az keyvault delete](https://docs.microsoft.com/en-us/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-delete):

```azurecli
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
