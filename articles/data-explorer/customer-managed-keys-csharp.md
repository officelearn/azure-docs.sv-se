---
title: Konfigurera Kundhanterade nycklar medC#
description: Den här artikeln beskriver hur du konfigurerar kryptering av Kundhanterade nycklar på dina data i Azure Datautforskaren.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: ac37c0e1f1f7d769a881c5965744b29d40468486
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276672"
---
# <a name="configure-customer-managed-keys-using-c"></a>Konfigurera Kundhanterade nycklar medC#

> [!div class="op_single_selector"]
> * [C#](create-cluster-database-csharp.md)
> * [Azure Resource Manager-mall](create-cluster-database-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med Kundhanterade nycklar

I det här avsnittet visas hur du konfigurerar kryptering av Kundhanterade nycklar med hjälp C# av Azure datautforskaren-klienten. 

### <a name="prerequisites"></a>Krav

* Om du inte har Visual Studio 2019 installerat kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

### <a name="install-c-nuget"></a>Installera C# NuGet

* Installera [Azure datautforskaren (Kusto) NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Installera [Microsoft. IdentityModel. clients. ActiveDirectory NuGet-paketet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) för autentisering.

### <a name="authentication"></a>Autentisering

Om du vill köra exemplen i den här artikeln [skapar du ett Azure AD-program](/azure/active-directory/develop/howto-create-service-principal-portal) och tjänstens huvud namn som har åtkomst till resurser. Du kan lägga till roll tilldelning i prenumerations omfånget och hämta nödvändiga `Directory (tenant) ID`, `Application ID`och `Client Secret`.

### <a name="configure-cluster"></a>Konfigurera klustret

Som standard använder Azure Datautforskaren-kryptering Microsoft-hanterade nycklar. Konfigurera Azure Datautforskaren-klustret för att använda Kundhanterade nycklar och ange nyckeln som ska associeras med klustret.

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

1. Kör följande kommando för att kontrol lera om klustret har uppdaterats:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Om resultatet innehåller `ProvisioningState` med `Succeeded`-värdet har klustret uppdaterats.

## <a name="update-the-key-version"></a>Uppdatera nyckel versionen

När du skapar en ny version av en nyckel måste du uppdatera klustret för att använda den nya versionen. Kontakta först `Get-AzKeyVaultKey` för att hämta den senaste versionen av nyckeln. Uppdatera sedan klustrets nyckel valvs egenskaper så att de använder den nya versionen av nyckeln, som visas i [Konfigurera kluster](#configure-cluster).

## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Datautforskaren kluster i Azure](security.md)
* [Konfigurera hanterade identiteter för ditt Azure Datautforskaren-kluster](managed-identities.md)
* [Skydda ditt kluster i Azure datautforskaren-Azure Portal](manage-cluster-security.md) genom att aktivera kryptering i vila.
* [Konfigurera Kundhanterade nycklar med hjälp av Azure Resource Manager mall](customer-managed-keys-resource-manager.md)


