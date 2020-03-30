---
title: Använda efterföljare databasfunktionen för att bifoga databaser i Azure Data Explorer
description: Lär dig mer om hur du bifogar databaser i Azure Data Explorer med hjälp av efterardatabasfunktionen.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: f6dbdb54c1c5a5d477c3ccb988963758faab83b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140022"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Använda följardatabasen för att bifoga databaser i Azure Data Explorer

Med **efterföljarens databasfunktionen** kan du koppla en databas som finns i ett annat kluster till Azure Data Explorer-klustret. **Följardatabasen** är ansluten i *skrivskyddat* läge, vilket gör det möjligt att visa data och köra frågor om data som förtärdes i **leader-databasen**. Efterföljaren databasen synkroniserar ändringar i leader-databaser. På grund av synkroniseringen finns det en datafördröjning på några sekunder till några minuter i datatillgänglighet. Tidsfördröjningens längd beror på den totala storleken på metadata för leader-databasen. Leader- och follower-databaserna använder samma lagringskonto för att hämta data. Lagringen ägs av leader-databasen. Efterföljaren databasen visar data utan att behöva inta den. Eftersom den bifogade databasen är en skrivskyddad databas kan data, tabeller och principer i databasen inte ändras förutom [cachelagringsprinciper,](#configure-caching-policy) [huvudnamn](#manage-principals)och [behörigheter](#manage-permissions). Det går inte att ta bort bifogade databaser. De måste tas bort av ledaren eller efterföljaren och först då kan de tas bort. 

Att koppla en databas till ett annat kluster med hjälp av följarfunktionen används som infrastruktur för att dela data mellan organisationer och team. Funktionen är användbar för att segregera beräkningsresurser för att skydda en produktionsmiljö från icke-produktionsanvändningsfall. Follower kan också användas för att associera kostnaden för Azure Data Explorer-klustret till den part som kör frågor om data.

## <a name="which-databases-are-followed"></a>Vilka databaser följs?

* Ett kluster kan följa en databas, flera databaser eller alla databaser i ett leader-kluster. 
* Ett enda kluster kan följa databaser från flera leader-kluster. 
* Ett kluster kan innehålla både efterardatabaser och leaderdatabaser

## <a name="prerequisites"></a>Krav

1. Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
1. [Skapa kluster och DB](/azure/data-explorer/create-cluster-database-portal) för ledaren och efterföljaren.
1. [Inta data](/azure/data-explorer/ingest-sample-data) till leader-databasen med hjälp av en av olika metoder som beskrivs [i inmatningsöversikten](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Ansluta en databas

Det finns olika metoder som du kan använda för att bifoga en databas. I den här artikeln diskuterar vi att bifoga en databas med C# eller en Azure Resource Manager-mall. Om du vill koppla en databas måste du ha behörighet för leader-klustret och efterföljaren klustret. Mer information om behörigheter finns i [Hantera behörigheter](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Bifoga en databas med C #

#### <a name="needed-nugets"></a>Behövs NuGets

* Installera [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installera [Microsoft.Rest.ClientRuntime.Azure.Authentication för autentisering](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="code-example"></a>Exempel på kod

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
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
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

### <a name="attach-a-database-using-python"></a>Bifoga en databas med Python

#### <a name="needed-modules"></a>Nödvändiga moduler

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Exempel på kod

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
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Bifoga en databas med en Azure Resource Manager-mall

I det här avsnittet lär du dig att bifoga en databas till en befintlig cluser med hjälp av en [Azure Resource Manager-mall](../azure-resource-manager/management/overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
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
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
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
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
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

Du kan distribuera Azure Resource Manager-mallen med hjälp av [Azure-portalen](https://portal.azure.com) eller med powershell.

   ![malldistribution](media/follower/template-deployment.png)


|**Inställning**  |**Beskrivning**  |
|---------|---------|
|Namn på efterföljarenskluster     |  Namnet på efterföljaren klustret; där mallen ska distribueras.  |
|Namn på bifogade databaskonfigurationer    |    Namnet på det bifogade databaskonfigurationsobjektet. Namnet kan vara vilken sträng som helst som är unik på klusternivå.     |
|Databasnamn     |      Namnet på den databas som ska följas. Om du vill följa alla ledarens databaser använder du '*'.   |
|Resurs-ID för Leader-kluster    |   Resurs-ID för leaderklustret.      |
|Standardhuvudnamn Ändringssort    |   Standardinställningsregeln för ändring. Kan `Union`vara `Replace` `None`, eller . Mer information om standardnamnsändringssort finns i [kommandot för huvudändringsroll](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Location   |   Platsen för alla resurser. Ledaren och efterföljaren måste vara på samma plats.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Kontrollera att databasen har anslutits

Om du vill kontrollera att databasen har anslutits hittar du dina bifogade databaser i [Azure-portalen](https://portal.azure.com). 

1. Navigera till efterarklustret och välj **Databaser**
1. Sök efter nya skrivskyddade databaser i databaslistan.

    ![Skrivskyddad följardatabas](media/follower/read-only-follower-database.png)

Du kan också:

1. Navigera till leader-klustret och välj **Databaser**
2. Kontrollera att relevanta databaser är markerade som **DELADE MED ANDRA** > **Ja**

    ![Läsa och skriva bifogade databaser](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Koppla bort följardatabasen med C # 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Koppla bort den bifogade följardatabasen från efterföljaren

Efterföljaren klustret kan koppla från alla bifogade databaser på följande sätt:

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
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Koppla bort den bifogade följardatabasen från leader-klustret

Leader-klustret kan koppla från alla bifogade databaser enligt följande:

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
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Koppla bort följardatabasen med Python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Koppla bort den bifogade följardatabasen från efterföljaren

Efterföljaren klustret kan koppla från alla bifogade databaser på följande sätt:

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
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Koppla bort den bifogade följardatabasen från leader-klustret

Leader-klustret kan koppla från alla bifogade databaser enligt följande:

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
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Hantera principer, behörigheter och cachelagringsprincip

### <a name="manage-principals"></a>Hantera huvudnamn

När du bifogar en databas anger du **"standardnamnsändringssort"**. Standardär att behålla leader-databassamlingen av [auktoriserade huvudmän](/azure/kusto/management/access-control/index#authorization)

|**Typ** |**Beskrivning**  |
|---------|---------|
|**Union**     |   De bifogade databashuvudnamnen kommer alltid att innehålla de ursprungliga databashuvudnamnen plus ytterligare nya huvudmän som läggs till i efterföljarens databas.      |
|**Ersätt**   |    Inget arv av huvudnamn från den ursprungliga databasen. Nya huvudnamn måste skapas för den bifogade databasen.     |
|**Inget**   |   De bifogade databasobjekten omfattar endast huvudmännen i den ursprungliga databasen utan ytterligare huvudmän.      |

Mer information om hur du använder kontrollkommandon för att konfigurera de auktoriserade huvudnamnen finns i [Kontrollkommandon för hantering av ett efterföljarenskluster](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>Hantera behörigheter

Att hantera skrivskyddad databasbehörighet är samma som för alla databastyper. Se [hantera behörigheter i Azure-portalen](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Konfigurera cachelagringsprincipen

Den efterföljande databasadministratören kan ändra [cachelagringsprincipen](/azure/kusto/management/cache-policy) för den bifogade databasen eller någon av dess tabeller i värdklustret. Standardär att behålla den ledande databassamlingen för cachelagringsprinciper på databas- och tabellnivå. Du kan till exempel ha en 30 dagars cachelagringsprincip i leaderdatabasen för att köra månadsrapportering och en tre dagars cachelagringsprincip i efterföljaren-databasen för att fråga endast de senaste data för felsökning. Mer information om hur du använder kontrollkommandon för att konfigurera cachelagringsprincipen i följardatabasen eller tabellen finns i [Kontrollera kommandon för hantering av ett efterföljareskluster](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Begränsningar

* Efterföljaren och leaderkluster måste vara i samma region.
* [Inmatning av direktuppspelning](/azure/data-explorer/ingest-data-streaming) kan inte användas i en databas som följs.
* Datakryptering med [kundhanterade nycklar](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) stöds inte i både leader- och follower-kluster. 
* Du kan inte ta bort en databas som är kopplad till ett annat kluster innan du kopplar från den.
* Du kan inte ta bort ett kluster som har en databas kopplad till ett annat kluster innan du kopplar från det.
* Du kan inte stoppa ett kluster som har bifogat efterföljare eller leader-databaser. 

## <a name="next-steps"></a>Nästa steg

* Information om uppföljningsklusterkonfiguration finns i [Styra kommandon för hantering av ett efterföljarenskluster](/azure/kusto/management/cluster-follower).
