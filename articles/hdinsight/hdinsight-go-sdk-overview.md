---
title: Azure HDInsight SDK för go
description: Referensmaterial för användning av Azure HDInsight SDK for Go- och Apache Hadoop-kluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 01/03/2020
ms.openlocfilehash: 292496c4d458621213fe62105149ac845d78891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479594"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK för go (förhandsgranskning)

## <a name="overview"></a>Översikt
HDInsight SDK för go innehåller klasser och funktioner som gör att du kan hantera dina HDInsight-kluster. Den innehåller åtgärder för att skapa, ta bort, uppdatera, lista, ändra storlek, köra skriptåtgärder, övervaka, få egenskaper för HDInsight-kluster med mera.

> [!NOTE]  
>GoDoc referensmaterial för denna SDK finns också [här](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

* Ett [ `go get` verktyg](https://github.com/golang/go/wiki/GoGetTools).
* [Gå, gå.](https://golang.org/dl/)

## <a name="sdk-installation"></a>SDK-installation

Från gopath-platsen kör du`go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Autentisering

SDK måste först autentiseras med din Azure-prenumeration.  Följ exemplet nedan för att skapa ett huvudnamn för tjänsten och använda det för att autentisera. När detta är gjort har du en `ClustersClient`instans av en , som innehåller många funktioner (beskrivs i nedanstående avsnitt) som kan användas för att utföra hanteringsåtgärder.

> [!NOTE]  
> Det finns andra sätt att autentisera förutom nedanstående exempel som potentiellt skulle kunna vara bättre lämpade för dina behov. Alla funktioner beskrivs här: [Autentiseringsfunktioner i Azure SDK for Go](https://docs.microsoft.com/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Exempel på autentisering med hjälp av ett tjänsthuvudnamn

Logga först in på [Azure Cloud Shell](https://shell.azure.com/bash). Kontrollera att du använder prenumerationen där du vill att tjänstens huvudnamn ska skapas.

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

Om du inte är inloggad på rätt prenumeration väljer du rätt genom att köra: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Om du inte redan har registrerat HDInsight Resource Provider av en annan funktion (till exempel genom att skapa ett HDInsight-kluster via Azure-portalen) måste du göra detta en gång innan du kan autentisera. Detta kan göras från [Azure Cloud Shell](https://shell.azure.com/bash) genom att köra följande kommando:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Välj sedan ett namn för tjänstens huvudnamn och skapa det med följande kommando:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Tjänstens huvudinformation visas som JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Kopiera nedanstående kodavsnitt och `TENANT_ID` `CLIENT_ID`fyll `CLIENT_SECRET`i `SUBSCRIPTION_ID` , , och med strängarna från JSON som returnerades efter att ha kört kommandot för att skapa tjänstens huvudnamn.

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
> Det här avsnittet förutsätter att du `ClusterClient` redan har autentiserat `client`och konstruerat en instans och lagra den i en variabel som kallas . Instruktioner för att autentisera och erhålla en `ClusterClient` finns i avsnittet Autentisering ovan.

### <a name="create-a-cluster"></a>Skapa ett kluster

Ett nytt kluster kan `client.Create()`skapas genom att anropa . 

#### <a name="example"></a>Exempel

Det här exemplet visar hur du skapar ett [Apache Spark-kluster](https://spark.apache.org/) med två huvudnoder och en arbetsnod.

> [!NOTE]  
> Du måste först skapa ett resursgrupps- och lagringskonto, som förklaras nedan. Om du redan har skapat dessa kan du hoppa över dessa steg.

##### <a name="creating-a-resource-group"></a>Skapa resursgruppen

Du kan skapa en resursgrupp med [Azure Cloud Shell](https://shell.azure.com/bash) genom att köra

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Skapa ett lagringskonto

Du kan skapa ett lagringskonto med [Azure Cloud Shell](https://shell.azure.com/bash) genom att köra:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Kör nu följande kommando för att hämta nyckeln för ditt lagringskonto (du behöver detta för att skapa ett kluster):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
Nedanstående Go-utdrag skapar ett Spark-kluster med två huvudnoder och en arbetsnod. Fyll i de tomma variablerna som förklaras i kommentarerna och känn dig fri att ändra andra parametrar för att passa dina specifika behov.

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

Så här hämtar du egenskaper för ett visst kluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exempel

Du kan `get` använda för att bekräfta att du har skapat klustret.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Utdata ska se ut så här:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Lista kluster

#### <a name="list-clusters-under-the-subscription"></a>Lista kluster under prenumerationen

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Lista kluster efter resursgrupp

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Både `List()` `ListByResourceGroup()` och `ClusterListResultPage` returnera en struct. Om du vill hämta nästa `Next()`sida kan du ringa . Detta kan upprepas `false`tills returen returneras `ClusterListResultPage.NotDone()` , vilket visas i exemplet nedan.

#### <a name="example"></a>Exempel

I följande exempel skrivs egenskaperna för alla kluster för den aktuella prenumerationen ut:

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

Så här tar du bort ett kluster:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Uppdatera klustertaggar

Du kan uppdatera taggarna för ett visst kluster så här:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Exempel

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Ändra storlek på kluster

Du kan ändra storlek på ett visst klusters antal arbetsnoder genom att ange en ny storlek på samma sätt:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Klusterövervakning

HDInsight Management SDK kan också användas för att hantera övervakning på dina kluster via Operations Management Suite (OMS).

På samma sätt `ClusterClient` som du skapade för att använda `ExtensionClient` för hanteringsåtgärder måste du skapa en att använda för övervakningsåtgärder. När du har slutfört avsnittet Autentisering ovan `ExtensionClient` kan du skapa så här:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Nedanstående övervakningsexempel förutsätter `ExtensionClient` att `extClient` du `Authorizer` redan har initierat ett anropat och ställt in det som visas ovan.

### <a name="enable-oms-monitoring"></a>Aktivera OMS-övervakning

> [!NOTE]  
> Om du vill aktivera OMS-övervakning måste du ha en befintlig Log Analytics-arbetsyta. Om du inte redan har skapat en kan du läsa om hur du gör det här: [Skapa en Log Analytics-arbetsyta i Azure-portalen](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Så här aktiverar du OMS-övervakning i klustret:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Visa status för OMS-övervakning

Så här hämtar du statusen FÖR OMS i klustret:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Inaktivera OMS-övervakning

Så här inaktiverar du OMS i klustret:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Skriptåtgärder

HDInsight tillhandahåller en konfigurationsfunktion som kallas skriptåtgärder som anropar anpassade skript för att anpassa klustret.

> [!NOTE]  
> Mer information om hur du använder skriptåtgärder finns här: [Anpassa Linux-baserade HDInsight-kluster med hjälp av skriptåtgärder](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Utföra skriptåtgärder

Du kan köra skriptåtgärder på ett visst kluster så här:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

För åtgärderna Ta bort skriptåtgärder och lista beständiga skriptåtgärder `ScriptActionsClient`måste du skapa en `ClusterClient` , på samma sätt som du skapade för att använda för hanteringsåtgärder. När du har slutfört avsnittet Autentisering ovan `ScriptActionsClient` kan du skapa så här:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> I exemplen nedanstående skriptåtgärder förutsätter `scriptActionsClient` att `Authorizer` du redan har initierat ett `ScriptActionsClient` anropat och angett dess som visas ovan.

### <a name="delete-script-action"></a>Ta bort skriptåtgärd

Så här tar du bort en angiven beständig skriptåtgärd i ett visst kluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Lista beständiga skriptåtgärder

> [!NOTE]  
> Båda `ListByCluster()` returnerar en `ScriptActionsListPage` struct. Om du vill hämta nästa `Next()`sida kan du ringa . Detta kan upprepas `false`tills returen returneras `ClusterListResultPage.NotDone()` , vilket visas i exemplet nedan.

Så här listar du alla beständiga skriptåtgärder för det angivna klustret:
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

### <a name="list-all-scripts-execution-history"></a>Lista alla skripts körningshistorik

För den här åtgärden måste `ScriptExecutionHistoryClient`du skapa en `ClusterClient` , på samma sätt som du skapade för att använda för hanteringsåtgärder. När du har slutfört avsnittet Autentisering ovan `ScriptActionsClient` kan du skapa så här:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Nedanstående förutsätter att du `ScriptExecutionHistoryClient` redan `scriptExecutionHistoryClient` har `Authorizer` initierat ett anropat och ställt in det som visas ovan.

Så här listar du alla skripts körningshistorik för det angivna klustret:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Exempel

I det här exemplet skrivs all information för alla tidigare skriptkörningar.

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

Utforska [GoDoc referensmaterial](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). GoDocs tillhandahåller referensdokumentation för alla funktioner i SDK.
