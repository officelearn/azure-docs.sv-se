---
title: Använd hjälp databas funktionen för att ansluta databaser i Azure Datautforskaren
description: Lär dig mer om hur du ansluter databaser i Azure Datautforskaren med hjälp av databas funktionen.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 495f53bc97835c4940f7b36d23349b768a7a637f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440968"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Använd följande databas för att koppla databaser i Azure Datautforskaren

Med **hjälp av databas** funktionen kan du koppla en databas som finns i ett annat kluster till ditt Azure datautforskaren-kluster. **Följande databas** är i *skrivskyddat* läge, vilket gör det möjligt att visa data och köra frågor på data som har matats in i **ledar databasen**. Följande databas synkroniserar ändringar i ledar databaserna. På grund av synkroniseringen är det en data fördröjning på några sekunder till några minuter i data tillgänglighet. Längden på tids fördröjningen beror på den övergripande storleken på ledar databasens metadata. Ledare och Uppföljnings databaser använder samma lagrings konto för att hämta data. Lagrings platsen ägs av ledar databasen. I följande databas visas data utan att du behöver mata in dem. Eftersom den bifogade databasen är en skrivskyddad databas går det inte att ändra data, tabeller och principer i [databasen, förutom](#manage-principals)princip för [cachelagring, principer](#configure-caching-policy)och [behörigheter](#manage-permissions). Det går inte att ta bort anslutna databaser. De måste kopplas bort av ledaren eller följaren och de kan bara tas bort. 

Att ansluta en databas till ett annat kluster med hjälp av följande funktioner används som infrastruktur för att dela data mellan organisationer och team. Funktionen är användbar för att åtskilja beräknings resurser för att skydda en produktions miljö från icke-produktions användnings fall. Du kan också använda uppföljning för att koppla kostnaden för Azure Datautforskaren-kluster till den part som kör frågor på data.

## <a name="which-databases-are-followed"></a>Vilka databaser följs?

* Ett kluster kan följa en databas, flera databaser eller alla databaser i ett ledar kluster. 
* Ett enda kluster kan följa databaser från flera ledar kluster. 
* Ett kluster kan innehålla både uppföljnings databaser och ledar databaser

## <a name="prerequisites"></a>Krav

1. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.
1. [Skapa kluster och databas](/azure/data-explorer/create-cluster-database-portal) för ledare och uppföljning.
1. Mata in [data](/azure/data-explorer/ingest-sample-data) till ledar databasen med hjälp av en av de olika metoderna som beskrivs i inmatnings [översikten](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Ansluta en databas

Det finns olika metoder som du kan använda för att koppla en databas. I den här artikeln diskuterar vi hur du kopplar en databas C# med hjälp av eller en Azure Resource Manager-mall. Om du vill ansluta en databas måste du ha behörighet för ledar klustret och Uppföljnings klustret. Mer information om behörigheter finns i [Manage Permissions](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Koppla en databas medC#

#### <a name="needed-nugets"></a>Nödvändig NuGets

* Installera [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installera [Microsoft. rest. ClientRuntime. Azure. Authentication för autentisering](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="code-example"></a>Kod exempel

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "adc";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Koppla en databas med python

#### <a name="needed-modules"></a>Moduler som krävs

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Kod exempel

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "adc"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Koppla en databas med en Azure Resource Manager mall

I det här avsnittet får du lära dig hur du ansluter en databas med hjälp av en [Azure Resource Manager mall](../azure-resource-manager/management/overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Distribuera mallen 

Du kan distribuera Azure Resource Manager-mallen med [hjälp av Azure Portal](https://portal.azure.com) eller med hjälp av PowerShell.

   ![mall distribution](media/follower/template-deployment.png)


|**Inställning**  |**Beskrivning**  |
|---------|---------|
|Namn på Uppföljnings kluster     |  Namnet på Uppföljnings klustret       |
|Konfigurations namn för bifogad databas    |    Namnet på det anslutna databas konfigurations objekt. Namnet måste vara unikt på kluster nivån.     |
|Databasnamn     |      Namnet på databasen som ska följas. Om du vill följa alla ledares databaser använder du "*".   |
|Resurs-ID för ledar kluster    |   Resurs-ID för ledar klustret.      |
|Standard princip för ändrings typ    |   Standard princip för ändrings typ. Kan vara `Union`, `Replace` eller `None`. Mer information om ändrings typen av standard princip objekt finns i avsnittet [ändra typ kontroll kommando](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Location   |   Platsen för alla resurser. Ledaren och följaren måste finnas på samma plats.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Kontrol lera att databasen har kopplats

Du kan kontrol lera att databasen har anslutits genom att leta upp dina anslutna databaser i [Azure Portal](https://portal.azure.com). 

1. Gå till klustret i följar och välj **databaser**
1. Sök efter nya skrivskyddade databaser i databas listan.

    ![Skrivskyddad uppföljnings databas](media/follower/read-only-follower-database.png)

Också

1. Navigera till ledar klustret och välj **databaser**
2. Kontrol lera att relevanta databaser är markerade som **delade med andra** > **Ja**

    ![Läsa och skriva anslutna databaser](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Koppla från följer-databasen medC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Koppla från den anslutna databasen från följande kluster

Med hjälp av klustret kan du koppla från alla anslutna databaser på följande sätt:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Koppla från den anslutna databasen från ledar klustret

Med ledar klustret kan du koppla från alla anslutna databaser på följande sätt:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Koppla från följer-databasen med python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Koppla från den anslutna databasen från följande kluster

Med hjälp av klustret kan du koppla från alla anslutna databaser på följande sätt:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "adc"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Koppla från den anslutna databasen från ledar klustret

Med ledar klustret kan du koppla från alla anslutna databaser på följande sätt:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "adc"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Hantera Principal, behörigheter och princip för cachelagring

### <a name="manage-principals"></a>Hantera huvud konton

När du bifogar en databas anger du **"standard princip för ändrings typ"** . Standardvärdet är insamlingen av [auktoriserade objekt](/azure/kusto/management/access-control/index#authorization) i ledar databasen

|**Metod** |**Beskrivning**  |
|---------|---------|
|**Union**     |   De bifogade databas huvud namnen innehåller alltid de ursprungliga databas huvud kontona och ytterligare nya säkerhets objekt som lagts till i följande databas.      |
|**Bytt**   |    Inga arv av huvud konton från den ursprungliga databasen. Nya säkerhets objekt måste skapas för den bifogade databasen.     |
|**Ingen**   |   De bifogade databas huvud kontona innehåller bara huvud kontona för den ursprungliga databasen utan ytterligare huvud objekt.      |

Mer information om hur du använder kontroll kommandon för att konfigurera de auktoriserade huvud kontona finns i [kontrol lera kommandon för att hantera ett uppföljnings kluster](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>Hantera behörigheter

Att hantera behörighet för skrivskyddad databas är detsamma som för alla databas typer. Se [Hantera behörigheter i Azure Portal](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Konfigurera princip för cachelagring

Databas administratören för databaserna kan ändra [principen för cachelagring](/azure/kusto/management/cache-policy) av den anslutna databasen eller någon av dess tabeller i värd klustret. Standardvärdet är insamlingen av databas-och cachelagrade principer på databas-och tabell nivå. Du kan till exempel ha en princip på 30 dagar för att placera ledar databasen för att köra Månatlig rapportering och en tre dagars caching-princip på databasen för att endast fråga efter den senaste informationen för fel sökning. Mer information om hur du använder kontroll kommandon för att konfigurera principen för cachelagring i databasen eller tabellen finns i [kontrol lera kommandon för att hantera ett uppföljnings kluster](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Begränsningar

* Följaren och ledar klustren måste vara i samma region.
* [Strömnings](/azure/data-explorer/ingest-data-streaming) inmatning kan inte användas för en databas som följs.
* Du kan inte ta bort en databas som är kopplad till ett annat kluster innan du kopplar från den.
* Du kan inte ta bort ett kluster som har en databas som är kopplad till ett annat kluster innan du kopplar bort det.
* Det går inte att stoppa ett kluster som har bifogad följare eller ledar databas (er). 

## <a name="next-steps"></a>Nästa steg

* Information om konfiguration av uppföljnings kluster finns i [kontrol lera kommandon för att hantera ett uppföljnings kluster](/azure/kusto/management/cluster-follower).