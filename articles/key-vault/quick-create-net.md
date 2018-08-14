---
title: Azure-snabbstart – Konfigurera ett Azure-webbprogram att ställa in och hämta en hemlighet från Key Vault | Microsoft Docs
description: Snabbstart som beskriver hur du konfigurerar ett ASP.Net Core-program för att ställa in och hämta en hemlighet från Key Vault
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 8b5624ae3083d92213b4ee919dc0860bf5ff4ab7
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480210"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-net-web-app"></a>Snabbstart: Ställ in och hämta en hemlighet från Azure Key Vault med hjälp av en .NET-webbapp

I den här självstudien går du igenom de steg som behövs för att ett Azure-webbprogram ska läsa information från Key Vault med hjälp av hanterade tjänstidentiteter. Lär dig att:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lagra en hemlighet i Key Vault.
> * Hämta en hemlighet från Key Vault.
> * Skapa ett Azure-webbprogram.
> * [Aktivera hanterade tjänstidentiteter](../active-directory/managed-service-identity/overview.md).
> * Bevilja de behörigheter som krävs för att webbprogrammet ska kunna läsa data från Key Vault.

Innan du går vidare bör du läsa [grundbegreppen](key-vault-whatis.md#basic-concepts), särskilt [hanterad tjänstidentitet](../active-directory/managed-service-identity/overview.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

* I Windows:
  * [Visual Studio 2017 version 15.7.3 eller senare](https://www.microsoft.com/net/download/windows) med följande arbetsbelastningar:
    * ASP.NET och webbutveckling
    * .NET Core plattformsoberoende utveckling
  * [.NET Core 2.1 SDK eller senare](https://www.microsoft.com/net/download/windows)

* På Mac:
  * https://visualstudio.microsoft.com/vs/mac/

* Alla plattformar:
  * Ladda ned GIT från [den här platsen](https://git-scm.com/downloads).
  * En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Du behöver Azure CLI version 2.0.4 eller senare. Detta är tillgängligt för Windows, Mac och Linux

## <a name="login-to-azure"></a>Logga in till Azure

   Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Välj ett resursgruppnamn och fyll i platshållaren.
I följande exempel skapas en resursgrupp med namnet *<YourResourceGroupName>* på platsen *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Den resursgrupp som du nyss skapade används i hela den här artikeln.

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

Nu ska du skapa ett nyckelvalv i resursgruppen som du skapade i föregående steg. Ange följande information:

* Valvnamn – **välj ett Key Vault-namn här**. Key Vault-namnet måste vara en sträng med 3–24 tecken som innehåller endast (0–9, a–z, A–Z och -).
* Resursgruppnamn – **välj ett resursgruppnamn här**.
* Plats – **USA, östra**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Du kan lagra en SQL-anslutningssträng eller annan information som du behöver skydda men göra tillgänglig för ditt program. I den här självstudiekursen kallas lösenordet **AppSecret** och lagrar värdet för **MySecret**.

Skriv kommandona nedan för att skapa en hemlighet i Key Vault med namnet **AppSecret** som ska lagra värdet **MySecret**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Det här kommandot visar den hemliga informationen, inklusive URI:n. När du har slutfört de här stegen bör du ha en URI till en hemlighet i Azure Key Vault. Anteckna den här informationen. Du behöver den senare.

## <a name="clone-the-repo"></a>Klona lagringsplatsen

Klona lagringsplatsen för att skapa en lokal kopia så att du kan redigera källan genom att köra följande kommando:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Öppna och redigera lösningen

Redigera filen program.cs för att köra exemplet med namnet på ditt specifika nyckelvalv.

1. Navigera till mappen key-vault-dotnet-core-quickstart
2. Öppna filen key-vault-dotnet-core-quickstart.sln i Visual Studio 2017
3. Öppna filen Program.cs och uppdatera platshållaren <YourKeyVaultName> med namnet på det nyckelvalv som du skapade tidigare.

Den här lösningen använder NuGet-biblioteken [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

## <a name="run-the-app"></a>Kör appen

På huvudmenyn för Visual Studio 2017 väljer du Debug > Start without Debugging (Felsökning > Starta utan felsökning). När webbläsaren visas går du till sidan Om. Värdet för AppSecret visas.

## <a name="publish-the-web-application-to-azure"></a>Publicera webbappen till Azure

Vi publicerar den här appen till Azure för att se den live som en webbapp och också se att vi hämta det hemliga värdet

1. I Visual Studio väljer du projektet **key-vault-dotnet-core-quickstart**.
2. Välj **Publicera** och sedan **Start**.
3. Skapa en ny **apptjänst** och välj **Publicera**.
4. Ändra namnet på appen till ”keyvaultdotnetcorequickstart”
5. Välj **Skapa**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>Aktivera hanterade tjänstidentiteter (MSI)

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Azure Key Vault för att kunna hämta dem. Hanterad tjänstidentitet (MSI) gör det här enklare genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden.

1. Gå tillbaka till Azure CLI
2. Kör kommandot assign-identity för att skapa identiteten för det här programmet:

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>Det här kommandot fungerar på samma sätt som när du går till portalen och väljer **På** för **Hanterad tjänstidentitet** i egenskaperna för webbprogrammet.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Tilldela behörigheter till ditt program att läsa hemligheter från Key Vault

Anteckna utdata när du [publicerar programmet till Azure][]. Det ska vara i formatet:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Kör sedan det här kommandot med namnet på ditt Key Vault och värdet för PrincipalId som kopieras från ovan:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>Nästa steg

* [Startsida för Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Dokumentation om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK för .NET](https://github.com/Azure/azure-sdk-for-net)
* [Azure REST API-referens](https://docs.microsoft.com/rest/api/keyvault/)
