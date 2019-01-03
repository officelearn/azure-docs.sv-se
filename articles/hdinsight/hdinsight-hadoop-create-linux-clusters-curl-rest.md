---
title: Skapa Apache Hadoop-kluster med Azure REST-API – Azure
description: Lär dig hur du skapar HDInsight-kluster genom att skicka in Azure Resource Manager-mallar till Azure REST-API.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: hrasheed
ms.openlocfilehash: fec87bf3af7f215b69a891c2d75cdd979a7b5a52
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789220"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Skapa Apache Hadoop-kluster med Azure REST-API

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Lär dig hur du skapar ett HDInsight-kluster med hjälp av en Azure Resource Manager-mall och Azure REST-API.

Azure REST-API kan du utföra hanteringsåtgärder på tjänster som finns i Azure-plattformen, inklusive att skapa nya resurser, till exempel HDInsight-kluster.

> [!IMPORTANT]  
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!NOTE]  
> Stegen i det här dokumentet används de [curl (https://curl.haxx.se/) ](https://curl.haxx.se/) verktyg för att kommunicera med Azure REST-API.

## <a name="create-a-template"></a>Skapa en mall

Azure Resource Manager-mallar är JSON-dokument som beskriver en **resursgrupp** och alla resurser i den (t.ex HDInsight). Mallbaserade så sätt kan du definiera de resurser som du behöver för HDInsight i en mall.

Följande JSON-dokumentet är en sammanslagning av mall och parametrar filerna från [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), vilket skapar ett Linux-baserade kluster som använder ett lösenord för att skydda SSH-användarkontot.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Det här exemplet används i stegen i det här dokumentet. Ersätta exemplet *värden* i den **parametrar** med värdena för ditt kluster.

> [!IMPORTANT]  
> Mallen använder standardvärdet för antal arbetsnoder (4) för ett HDInsight-kluster. Om du planerar att mer än 32 arbetsnoder, måste du välja en head nodstorlek med minst 8 kärnor och 14 GB RAM-minne.
>
> Mer information om nodstorlekar och relaterade kostnader finns i [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="log-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration

Följ stegen i [Kom igång med Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) och ansluta till din prenumeration med hjälp av den `az login` kommando.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

> [!NOTE]  
> De här stegen är en förkortad version av den *skapa tjänstens huvudnamn med lösenord* delen av den [används Azure CLI för att skapa ett huvudnamn för tjänsten för resursåtkomst](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) dokumentet. De här stegen skapar ett huvudnamn för tjänsten som används för att autentisera till Azure REST-API.

1. Använd kommandot för att lista dina Azure-prenumerationer från en kommandorad.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    I listan, Välj den prenumeration som du vill använda och anteckna den **Subscription_ID** och __Tenant_ID__ kolumner. Spara dessa värden.

2. Använd följande kommando för att skapa ett program i Azure Active Directory.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Ersätt värdena för den `--display-name`, `--homepage`, och `--identifier-uris` med dina egna värden. Ange ett lösenord för den nya Active Directory-posten.

   > [!NOTE]  
   > Den `--home-page` och `--identifier-uris` värden behöver inte referera till en webbsida som finns på internet. De måste vara unik URI: er.

   Värdet som returneras från det här kommandot är den __App-ID__ för det nya programmet. Spara det här värdet.

3. Använd följande kommando för att skapa ett huvudnamn för tjänsten med hjälp av den **App-ID**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Värdet som returneras från det här kommandot är den __objekt-ID__. Spara det här värdet.

4. Tilldela den **ägare** roll till tjänstens huvudnamn med den **objekt-ID** värde. Använd den **prenumerations-ID** du fick tidigare.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Få en autentiseringstoken

Använd följande kommando för att hämta någon autentiseringstoken:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Ange `$TENANTID`, `$APPID`, och `$PASSWORD` till värdena som anskaffats eller använts tidigare.

Om den här begäran lyckas får du svaret 200 serien och svarstexten innehåller ett JSON-dokument.

JSON-dokumentet som returneras av den här begäran innehåller ett element med namnet **access_token**. Värdet för **access_token** används för att autentiseringsbegäranden till REST-API.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Använd följande för att skapa en resursgrupp.

* Ange `$SUBSCRIPTIONID` till prenumerationen ID fick när du skapar tjänstens huvudnamn.
* Ange `$ACCESSTOKEN` till den åtkomst-token som tas emot i föregående steg.
* Ersätt `DATACENTERLOCATION` med datacenter som du vill skapa resursgrupp och resurser i. Till exempel ”USA, södra centrala”.
* Ange `$RESOURCEGROUPNAME` till namn som du vill använda för den här gruppen:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Om den här begäran lyckas får du svaret 200 serien och svarstexten innehåller ett JSON-dokument som innehåller information om gruppen. Den `"provisioningState"` elementet innehåller ett värde av `"Succeeded"`.

## <a name="create-a-deployment"></a>Skapa en distribution

Använd följande kommando för att distribuera mallen till resursgruppen.

* Ange `$DEPLOYMENTNAME` till namn som du vill använda för den här distributionen.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Om du har sparat mallen till en fil kan du använda följande kommando i stället för `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Om den här begäran lyckas får du svaret 200 serien och svarstexten innehåller ett JSON-dokument som innehåller information om distributionen igen.

> [!IMPORTANT]  
> Distributionen har skickats, men har inte slutförts. Det kan ta flera minuter, vanligen cirka 15 för distributionen har slutförts.

## <a name="check-the-status-of-a-deployment"></a>Kontrollera status för en distribution

Om du vill kontrollera status för distributionen, använder du följande kommando:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Det här kommandot returnerar ett JSON-dokument som innehåller information om distributionen igen. Den `"provisioningState"` elementet innehåller statusen för distributionen. Om det här elementet innehåller ett värde av `"Succeeded"`, och sedan distributionen har slutförts.

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett HDInsight-kluster, kan du använda följande för att lära dig hur du arbetar med ditt kluster.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-kluster

* [Utveckla Java-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
