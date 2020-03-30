---
title: 'Konfigurera kundhanterade nycklar med C #'
description: I den här artikeln beskrivs hur du konfigurerar kryptering med kundhanterade nycklar på dina data i Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a00b0876c4a188b932032129ed5a394e94198930
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297950"
---
# <a name="configure-customer-managed-keys-using-c"></a>Konfigurera kundhanterade nycklar med C #

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-mall](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med kundhanterade nycklar

I det här avsnittet visas hur du konfigurerar kryptering med kundhanterade nycklar med Azure Data Explorer C#-klienten. 

### <a name="prerequisites"></a>Krav

* Om du inte har Installerat Visual Studio 2019 kan du ladda ned och använda den **kostnadsfria** [Visual Studio 2019 Community Edition.](https://www.visualstudio.com/downloads/) Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

### <a name="install-c-nuget"></a>Installera C# NuGet

* Installera [Azure Data Explorer (Kusto) NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Installera [paketet Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) för autentisering.

### <a name="authentication"></a>Autentisering

Om du vill köra exemplen i den här artikeln [skapar du ett Azure AD-program](/azure/active-directory/develop/howto-create-service-principal-portal) och tjänsthuvudnamn som kan komma åt resurser. Du kan lägga till rolltilldelning i `Directory (tenant) ID` `Application ID`prenumerationsomfånget och hämta den nödvändiga , och `Client Secret`.

### <a name="configure-cluster"></a>Konfigurera kluster

Som standard använder Azure Data Explorer-kryptering Microsoft-hanterade nycklar. Konfigurera azure data explorer-klustret för att använda kundhanterade nycklar och ange nyckeln som ska associeras med klustret.

1. Uppdatera klustret med hjälp av följande kod:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Kör följande kommando för att kontrollera om klustret har uppdaterats:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Om resultatet innehåller `ProvisioningState` `Succeeded` värdet har klustret uppdaterats.

## <a name="update-the-key-version"></a>Uppdatera nyckelversionen

När du skapar en ny version av en nyckel måste du uppdatera klustret för att använda den nya versionen. Ring först `Get-AzKeyVaultKey` för att få den senaste versionen av nyckeln. Uppdatera sedan klustrets nyckelvalvsegenskaper för att använda den nya versionen av nyckeln, som visas i [Konfigurera kluster](#configure-cluster).

## <a name="next-steps"></a>Nästa steg

* [Säkra Azure Data Explorer-kluster i Azure](security.md)
* [Konfigurera hanterade identiteter för ditt Azure Data Explorer-kluster](managed-identities.md)
* [Skydda klustret i Azure Data Explorer - Azure-portalen](manage-cluster-security.md) genom att aktivera kryptering i vila.
* [Konfigurera kundhanterade nycklar med azure Resource Manager-mallen](customer-managed-keys-resource-manager.md)


