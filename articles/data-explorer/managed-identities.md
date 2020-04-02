---
title: Konfigurera hanterade identiteter för Azure Data Explorer-kluster
description: Lär dig hur du konfigurerar hanterade identiteter för Azure Data Explorer-kluster.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 900bf815917a4b7c9841860d663a2183b1ab71b3
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529678"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Konfigurera hanterade identiteter för ditt Azure Data Explorer-kluster

En [hanterad identitet från Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) gör att klustret enkelt kan komma åt andra AAD-skyddade resurser, till exempel Azure Key Vault. Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Den här artikeln visar hur du skapar en hanterad identitet för Azure Data Explorer-kluster. Konfiguration av hanterade identiteter stöds för närvarande endast för att [aktivera kundhanterade nycklar för klustret](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> Hanterade identiteter för Azure Data Explorer fungerar inte som förväntat om ditt Azure Data Explorer-kluster migreras över prenumerationer eller klienter. Appen måste skaffa en ny identitet, vilket kan göras genom att [inaktivera](#disable-a-system-assigned-identity) och [återaktivera](#add-a-system-assigned-identity) funktionen. Åtkomstprinciper för underordnade resurser måste också uppdateras för att använda den nya identiteten.

## <a name="add-a-system-assigned-identity"></a>Lägga till en systemtilldelad identitet
                                                                                                    
Tilldela en systemtilldelad identitet som är knuten till klustret och tas bort om klustret tas bort. Ett kluster kan bara ha en systemtilldelad identitet. Skapa ett kluster med en systemtilldelad identitet kräver ytterligare en egenskap som ska anges i klustret. Den systemtilldelade identiteten läggs till med C#, ARM-mallar eller Azure-portalen enligt beskrivningen nedan.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Lägga till en systemtilldelad identitet med Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

#### <a name="new-azure-data-explorer-cluster"></a>Nytt Azure Data Explorer-kluster

1. [Skapa ett Azure Data Explorer-kluster](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. Välj **På**fliken **Säkerhet** > **System-tilldelat identitet**. Om du vill ta bort den tilldelade datorn väljer du **Av**.
2. Välj **Nästa:Taggar>** eller **Granska + skapa** för att skapa klustret.

    ![Lägga till systembe tilldelat identitet i nytt kluster](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Befintligt Azure Data Explorer-kluster

1. Öppna ett befintligt Azure Data Explorer-kluster.
1. Välj **Inställningar** > **Identitet** i den vänstra rutan i portalen.
1. På fliken **Identitetsfönster** > **System:**
   1. Flytta skjutreglaget **Status** till **På**.
   1. Välj **Spara**
   1. Välj **Ja** i popup-fönstret

    ![Lägg till systemtilldelad identitet](media/managed-identities/turn-system-assigned-identity-on.png)

1. Efter några minuter visar skärmen: 
  * **Objekt-ID** - används för kundhanterade nycklar 
  * **Rolltilldelningar** - klicka på länk för att tilldela relevanta roller

    ![System tilldelade identitet på](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Lägga till en systemtilldelad identitet med C #

#### <a name="prerequisites"></a>Krav

Så här konfigurerar du en hanterad identitet med Azure Data Explorer C#-klienten:

* Installera [Azure Data Explorer (Kusto) NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installera [paketet Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) för autentisering.
* [Skapa ett Azure AD-program](/azure/active-directory/develop/howto-create-service-principal-portal) och tjänsthuvudnamn som kan komma åt resurser. Du lägger till rolltilldelning i `Directory (tenant) ID` `Application ID`prenumerationsomfånget och får den nödvändiga , och `Client Secret`.

#### <a name="create-or-update-your-cluster"></a>Skapa eller uppdatera klustret

1. Skapa eller uppdatera klustret med hjälp av egenskapen: `Identity`

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
    
2. Kör följande kommando för att kontrollera om klustret har skapats eller uppdaterats med en identitet:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Om resultatet innehåller `ProvisioningState` `Succeeded` med värdet skapades eller uppdaterades klustret och följande egenskaper bör ha följande egenskaper:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`och `TenantId` ersätts med GUID. Egenskapen `TenantId` identifierar den AAD-klient som identiteten tillhör. Är `PrincipalId` en unik identifierare för klustrets nya identitet. I AAD har tjänstens huvudnamn samma namn som du gav till din App Service- eller Azure Functions-instans.

# <a name="arm-template"></a>[ARM-mall](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Lägga till en systemtilldelad identitet med hjälp av en Azure Resource Manager-mall

En Azure Resource Manager-mall kan användas för att automatisera distributionen av dina Azure-resurser. Mer information om hur du distribuerar till Azure Data Explorer finns i [Skapa ett Azure Data Explorer-kluster och databas med hjälp av en Azure Resource Manager-mall](create-cluster-database-resource-manager.md).

Om du lägger till den systemtilldelade typen får Azure att skapa och hantera identiteten för klustret. Alla typer `Microsoft.Kusto/clusters` av resurser kan skapas med en identitet genom att inkludera följande egenskap i resursdefinitionen: 

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

`<TENANTID>`och `<PRINCIPALID>` ersätts med GUID. Egenskapen `TenantId` identifierar den AAD-klient som identiteten tillhör. Är `PrincipalId` en unik identifierare för klustrets nya identitet. I AAD har tjänstens huvudnamn samma namn som du gav till din App Service- eller Azure Functions-instans.

---

## <a name="disable-a-system-assigned-identity"></a>Inaktivera en systemtilldelad identitet

Om du tar bort en systemtilldelad identitet tas den också bort från AAD. Systemtilldelade identiteter tas också bort automatiskt från AAD när klusterresursen tas bort. En systemtilldelad identitet kan tas bort genom att inaktivera funktionen.  Den systemtilldelade identiteten tas bort med C#, ARM-mallar eller Azure-portalen enligt beskrivningen nedan.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Inaktivera en systemtilldelad identitet med Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **Inställningar** > **Identitet** i den vänstra rutan i portalen.
1. På fliken **Identitetsfönster** > **System:**
    1. Flytta skjutreglaget **Status** till **Av**.
    1. Välj **Spara**
    1. I popup-fönstret väljer du **Ja** för att inaktivera den systemtilldelade identiteten. **I fönstret Identitet** återgår till samma villkor som innan den systemtilldelade identiteten tillkom.

    ![System tilldelade identitet av](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Ta bort en systemtilldelad identitet med C #

Kör följande om du vill ta bort den systemtilldelade identiteten:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[ARM-mall](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Ta bort en systemtilldelad identitet med hjälp av en Azure Resource Manager-mall

Kör följande om du vill ta bort den systemtilldelade identiteten:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Nästa steg

* [Säkra Azure Data Explorer-kluster i Azure](security.md)
* [Skydda klustret i Azure Data Explorer - Azure-portalen](manage-cluster-security.md) genom att aktivera kryptering i vila.
 * [Konfigurera kundhanterade nycklar med C #](customer-managed-keys-csharp.md)
 * [Konfigurera kundhanterade nycklar med azure Resource Manager-mallen](customer-managed-keys-resource-manager.md)
