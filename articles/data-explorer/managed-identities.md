---
title: Konfigurera hanterade identiteter för Azure Datautforskaren-kluster
description: Lär dig hur du konfigurerar hanterade identiteter för Azure Datautforskaren-kluster.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 3b0bb62de4a96a45d607e05b32a87feec692e4d4
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725980"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Konfigurera hanterade identiteter för ditt Azure Datautforskaren-kluster

Med en [hanterad identitet från Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) kan klustret enkelt komma åt andra AAD-skyddade resurser som Azure Key Vault. Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Den här artikeln visar hur du skapar en hanterad identitet för Azure Datautforskaren-kluster. 

> [!Note]
> Hanterade identiteter för Azure Datautforskaren fungerar inte som förväntat om din app migreras mellan prenumerationer eller klienter. Appen måste skaffa en ny identitet, som kan göras genom att inaktivera och återaktivera funktionen med hjälp av [ta bort en identitet](#remove-an-identity). Åtkomst principer för underordnade resurser måste också uppdateras för att använda den nya identiteten.

## <a name="add-a-system-assigned-identity"></a>Lägg till en tilldelad identitet

Klustret kan tilldelas en **tilldelad identitet** som är kopplad till klustret och tas bort om klustret tas bort. Ett kluster kan bara ha en tilldelad identitet. Att skapa ett kluster med en tilldelad identitet kräver att en ytterligare egenskap anges i klustret.

### <a name="add-a-system-assigned-identity-using-c"></a>Lägg till en tilldelad identitet med hjälp avC#

Gör så här om du vill konfigurera en hanterad C# identitet med hjälp av Azure datautforskaren-klienten:

* Installera [Azure datautforskaren (Kusto) NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installera [Microsoft. IdentityModel. clients. ActiveDirectory NuGet-paketet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) för autentisering.
* Om du vill köra följande exempel [skapar du ett Azure AD-program](/azure/active-directory/develop/howto-create-service-principal-portal) och tjänstens huvud namn som har åtkomst till resurser. Du kan lägga till roll tilldelning i prenumerations omfånget och hämta nödvändiga `Directory (tenant) ID`, `Application ID`och `Client Secret`.

#### <a name="create-or-update-your-cluster"></a>Skapa eller uppdatera ditt kluster

1. Skapa eller uppdatera klustret med hjälp av `Identity`-egenskapen:

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
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Kör följande kommando för att kontrol lera om klustret har skapats eller uppdaterats med en identitet:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Om resultatet innehåller `ProvisioningState` med `Succeeded`-värdet, skapades eller uppdaterades klustret och måste ha följande egenskaper:
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` och `TenantId` ersätts med GUID. Egenskapen `TenantId` identifierar AAD-klienten som identiteten tillhör. `PrincipalId` är en unik identifierare för klustrets nya identitet. I AAD har tjänstens huvud namn samma namn som du gav App Service-eller Azure Functions-instansen.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Lägga till en tilldelad identitet med hjälp av en Azure Resource Manager mall

En Azure Resource Manager mall kan användas för att automatisera distributionen av dina Azure-resurser. Mer information om hur du distribuerar till Azure Datautforskaren finns i [skapa ett Azure datautforskaren-kluster och-databas med hjälp av en Azure Resource Manager mall](create-cluster-database-resource-manager.md).

Genom att lägga till den systemtilldelade typen kan Azure Skapa och hantera identiteten för klustret. Alla resurser av typen `Microsoft.Kusto/clusters` kan skapas med en identitet genom att inkludera följande egenskap i resurs definitionen: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Ett exempel:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

När klustret skapas har det följande ytterligare egenskaper:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` och `<PRINCIPALID>` ersätts med GUID. Egenskapen `TenantId` identifierar AAD-klienten som identiteten tillhör. `PrincipalId` är en unik identifierare för klustrets nya identitet. I AAD har tjänstens huvud namn samma namn som du gav App Service-eller Azure Functions-instansen.

## <a name="remove-an-identity"></a>Ta bort en identitet

Om du tar bort en tilldelad identitet tas även den bort från AAD. Systemtilldelade identiteter tas också bort automatiskt från AAD när kluster resursen tas bort. Du kan ta bort en systemtilldelad identitet genom att inaktivera funktionen:

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Datautforskaren kluster i Azure](security.md)
* [Skydda ditt kluster i Azure datautforskaren-Azure Portal](manage-cluster-security.md) genom att aktivera kryptering i vila.
 * [Konfigurera Kundhanterade nycklar medC#](customer-managed-keys-csharp.md)
 * [Konfigurera Kundhanterade nycklar med hjälp av Azure Resource Manager mall](customer-managed-keys-resource-manager.md)
