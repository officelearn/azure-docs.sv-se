---
title: Azure HDInsight Go SDK
description: Referens för Azure HDInsight Go SDK
services: hdinsight
author: tylerfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 9/21/2018
ms.author: tyfox
ms.openlocfilehash: f018130ca94c7efb7a9c6c873c150dcc382dbc4c
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498298"
---
# <a name="hdinsight-go-management-sdk-preview"></a>Förhandsversionen av HDInsight Go Management SDK

## <a name="overview"></a>Översikt
HDInsight Go SDK innehåller klasser och funktioner som gör att du kan hantera dina HDInsight-kluster. Det omfattar åtgärder för att skapa, ta bort, uppdatera, lista, ändra storlek, köra skriptåtgärder, övervaka och hämta egenskaper för HDInsight-kluster och mycket mer.

> [!NOTE]
>GoDoc referensmaterial för denna SDK är också [tillgänglig här](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto. Om du inte har någon, [skaffa en kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
* [Go](https://golang.org/dl/)

## <a name="sdk-installation"></a>SDK-installation

Kör från din GOPATH-plats `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Autentisering

SDK: N måste först autentiseras med Azure-prenumerationen.  Följ stegen i exemplet nedan för att skapa ett huvudnamn för tjänsten och använda den för att autentisera. När det är klart, har du en instans av en `ClustersClient`, som innehåller många funktioner (se nedan avsnitt) som kan användas för att utföra hanteringsåtgärder.

> [!NOTE]
> Det finns andra sätt att autentisera förutom den nedan exempel som kan potentiellt bättre passar för dina behov. Alla funktioner har utfört: [autentiseringsfunktioner i Azure SDK för Go](https://docs.microsoft.com/go/azure/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Autentisering-exempel som använder ett huvudnamn för tjänsten

Logga först in på [Azure Cloud Shell](https://shell.azure.com/bash). Kontrollera att du använder den prenumeration där du vill att tjänstens huvudnamn skapas. 

```azurecli-interactive
az account show
```

Din prenumerationsinformation visas som JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Om du inte är inloggad i rätt prenumeration, väljer du rätta genom att köra: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]
> Om du inte redan har registrerat Resursprovidern HDInsight med en annan funktion (till exempel genom att skapa ett HDInsight-kluster via Azure Portal), du behöver göra detta när innan du kan autentisera. Detta kan göras från den [Azure Cloud Shell](https://shell.azure.com/bash) genom att köra följande kommando:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Sedan väljer du ett namn för din tjänst och skapa den med följande kommando:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Information om tjänstens huvudnamn visas som JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Kopiera nedanstående kodutdrag och Fyll i `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET`, och `SUBSCRIPTION_ID` med strängar från JSON-filen som returnerades när du har kört kommandot för att skapa tjänstens huvudnamn.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"    
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Klusterhantering

> [!NOTE]
> Det här avsnittet förutsätter att du redan har autentiserats och skapas en `ClusterClient` instans och lagra den i en variabel med namnet `client`. Instruktioner för att autentisera och hämta en `ClusterClient` finns i avsnittet autentisering.

### <a name="create-a-cluster"></a>Skapa ett kluster

Ett nytt kluster kan skapas genom att anropa `client.Create()`. 

#### <a name="example"></a>Exempel

Det här exemplet visar hur du skapar en [Apache Spark](https://spark.apache.org/) kluster med 2 huvudnoder och 1 arbetsnoden.

> [!NOTE]
> Du måste först skapa en resursgrupp och Storage-konto, som beskrivs nedan. Om du redan har skapat dessa kan du hoppa över de här stegen.

##### <a name="creating-a-resource-group"></a>Skapa en resursgrupp

Du kan skapa en resurs med det [Azure Cloud Shell](https://shell.azure.com/bash) genom att köra
```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```
##### <a name="creating-a-storage-account"></a>Skapa ett lagringskonto

Du kan skapa ett storage-konto med den [Azure Cloud Shell](https://shell.azure.com/bash) genom att köra:
```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```
Nu kör följande kommando för att hämta nyckeln för ditt lagringskonto (du behöver detta för att skapa ett kluster):
```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```
---
Den nedan Go kodavsnitt skapar ett Spark-kluster med 2 huvudnoder och 1 arbetsnoden. Fyll i variablerna tom enligt beskrivningen i kommentarerna och passa på att ändra andra parametrar för att passa dina specifika behov.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Hämta klusterinformation

Hämta egenskaper för ett kluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exempel

Du kan använda `get` att bekräfta att du har skapat klustret.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Utdata bör se ut:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>En lista över kluster

#### <a name="list-clusters-under-the-subscription"></a>Lista över kluster under prenumerationen
```golang
client.List()
```
#### <a name="list-clusters-by-resource-group"></a>En lista över kluster per resursgrupp
```golang
client.ListByResourceGroup("<Resource Group Name>")
```
> [!NOTE]
> Båda `List()` och `ListByResourceGroup()` returnera en `ClusterListResultPage` Struct-datatypen. Om du vill hämta nästa sida, kan du anropa `Next()`. Detta kan upprepas tills `ClusterListResultPage.NotDone()` returnerar `false`, enligt exemplet nedan.

#### <a name="example"></a>Exempel
Följande exempel skriver egenskaperna för alla kluster för den aktuella prenumerationen:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Ta bort ett kluster

Ta bort ett kluster:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Uppdatera klustret taggar

Du kan uppdatera taggar för ett kluster som detta:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```
#### <a name="example"></a>Exempel

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Ändra storlek på klustret

Du kan ändra storlek på ett specifikt kluster antalet arbetarnoder genom att ange en ny storlek för t.ex:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Klusterövervakning

HDInsight Management SDK kan också användas för att hantera övervakning på ditt kluster via Operations Management Suite (OMS).

På samma sätt som hur du skapade `ClusterClient` om du vill använda för hanteringsåtgärder, måste du skapa en `ExtensionClient` ska användas för att övervaka åtgärder. När du har slutfört avsnittet autentisering kan du skapa en `ExtensionClient` t.ex:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> Den förutsätter att du har redan initierats under övervakning exempel en `ExtensionClient` kallas `extClient` och Ställ in dess `Authorizer` enligt ovan.

### <a name="enable-oms-monitoring"></a>Aktivera OMS-övervakning

> [!NOTE]
> Om du vill aktivera OMS-övervakning, måste du ha en befintlig Log Analytics-arbetsyta. Om du inte redan har skapat en så, du kan lära dig hur du gör här: [skapa en Log Analytics-arbetsyta i Azure-portalen](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Så här aktiverar du OMS-övervakning i klustret:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Visa status för OMS-övervakning

Hämta status för OMS i klustret:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Inaktivera OMS-övervakning

Inaktivera OMS i klustret:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Skriptåtgärder

HDInsight ger en konfiguration anropade funktionen skriptåtgärder som anropar anpassade skript för att anpassa klustret.
> [!NOTE]
> Mer information om hur du använder skriptåtgärder finns här: [anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Köra skriptåtgärder

Du kan köra skriptåtgärder i ett kluster som detta:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

För de ”ta bort skriptet Action” och ”listan beständiga skriptåtgärder', måste du skapa en `ScriptActionsClient`, på samma sätt som hur du skapade `ClusterClient` för hanteringsåtgärder. När du har slutfört avsnittet autentisering kan du skapa en `ScriptActionsClient` t.ex:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> Den nedan skriptåtgärder exemplen antar vi att du har redan initierats en `ScriptActionsClient` kallas `scriptActionsClient` och Ställ in dess `Authorizer` enligt ovan.

### <a name="delete-script-action"></a>Ta bort skriptåtgärd

Ta bort en angiven bestående skriptåtgärd på ett kluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Lista över beständiga skriptåtgärder

> [!NOTE]
> Båda `ListByCluster()` returnerar en `ScriptActionsListPage` Struct-datatypen. Om du vill hämta nästa sida, kan du anropa `Next()`. Detta kan upprepas tills `ClusterListResultPage.NotDone()` returnerar `false`, enligt exemplet nedan.

Visa en lista över alla beständiga skriptåtgärder för det angivna klustret:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exempel

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {          
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }    
}
```

### <a name="list-all-scripts-execution-history"></a>Lista över alla skript körningshistorik

För den här åtgärden måste du skapa en `ScriptExecutionHistoryClient`, på samma sätt som hur du skapade `ClusterClient` för hanteringsåtgärder. När du har slutfört avsnittet autentisering kan du skapa en `ScriptActionsClient` t.ex:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> Den nedan förutsätter att du har redan initierats en `ScriptExecutionHistoryClient` kallas `scriptExecutionHistoryClient` och Ställ in dess `Authorizer` enligt ovan.

Visa en lista över alla skript körningstiden för det angivna klustret:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exempel

Det här exemplet skriver ut all information för alla tidigare skriptkörningar.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {          
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }       
}
```

## <a name="next-steps"></a>Nästa steg

* Utforska den [GoDoc referensmaterial](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). GoDocs ger referensdokumentation för alla funktioner i SDK.
