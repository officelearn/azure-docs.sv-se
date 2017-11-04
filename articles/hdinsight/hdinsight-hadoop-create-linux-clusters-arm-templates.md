---
title: "Skapa Hadoop-kluster med hjälp av mallar - Azure HDInsight | Microsoft Docs"
description: "Lär dig att skapa kluster för HDInsight med hjälp av Resource Manager-mallar"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: jgao
ms.openlocfilehash: 25a45a380db06808db352fa26b88235d6e4b8fd4
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Skapa Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

I den här artikeln får du lära dig flera olika sätt att skapa Azure HDInsight-kluster med Azure Resource Manager-mallar. Mer information finns i [distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/resource-group-template-deploy.md). Mer information om andra verktyg för att skapa klustret och funktioner, klickar du på flikväljaren överst i den här sidan eller se [kluster metoder för att skapa](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Krav
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Om du vill följa anvisningarna i den här artikeln behöver du:

* En [Azure-prenumeration](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell och/eller Azure CLI.

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren
En mall för Resource Manager gör det enkelt att skapa följande för ditt program i en enda, samordnad åtgärd:
* HDInsight-kluster och deras beroende resurser (till exempel standardkontot för lagring)
* Andra resurser (till exempel Azure SQL-databas att använda Apache Sqoop)

I mallen kan du definiera de resurser som krävs för programmet. Du kan också ange distribution parametrar för att ange värden för olika miljöer. Mallen består av JSON och uttryck som används för att skapa värden för din distribution.

Du kan hitta HDInsight mall prov på [Azure Quickstart mallar](https://azure.microsoft.com/resources/templates/?term=hdinsight). Använda plattformsoberoende [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) med den [Resource Manager tillägget](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) eller en textredigerare för att spara mallen till en fil på din arbetsstation. Du lär dig hur du anropar mallen med hjälp av olika metoder.

Mer information om Resource Manager-mallar finns i följande artiklar:

* [Redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)
* [Distribuera ett program med Azure Resource Manager-mallar](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Skapa mallar

Med hjälp av Azure portal, kan du konfigurera alla egenskaper i ett kluster och spara mallen innan du distribuerar den. Du kan sedan återanvända mallen.

**Att skapa en mall med hjälp av Azure portal**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **skapar du en resurs** på den vänstra menyn klickar du på **Data + analys**, och klicka sedan på **HDInsight**.
4. På den **sammanfattning** klickar du på **ladda ned mall och parametrar**:

    ![HDInsight Hadoop skapa klustret Resource Manager hämtar en mall](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-download.png)

    Du kan se en lista över mallfil, parameterfilen och kodexempel som används för att distribuera mallen:

    ![HDInsight Hadoop Skapa kluster Hämtningsalternativ för Resource Manager-mall](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-download-options.png)

    Härifrån kan du hämta mallen, Spara mall-biblioteket eller distribuera mallen.

    En mall i biblioteket, klicka på **fler tjänster** från den vänstra menyn och klicka sedan på **mallar** (under den **andra** kategori).

    > [!Note]
    > Filen mall och parametrar måste användas tillsammans. Annars kan du få oväntade resultat. Till exempel standard **clusterKind** egenskapsvärdet är alltid **hadoop**, trots vad du ange innan du hämtar mallen.



## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

Den här proceduren skapar ett Hadoop-kluster i HDInsight.

1. Spara JSON-filen i den [bilaga](#appx-a-arm-template) till din arbetsstation. I PowerShell-skriptet är filnamnet `C:\HDITutorials-ARM\hdinsight-arm-template.json`.
2. Ange parametrar och variabler vid behov.
3. Kör sedan mallen med hjälp av följande PowerShell-skript:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>"
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and variables
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage account
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

    PowerShell-skriptet konfigurerar klustrets namn. Lagringskontonamnet är hårdkodat i mallen. Du uppmanas att ange klustret användarens lösenord. (Standardanvändarnamnet är **admin**.) Du uppmanas också att ange lösenord för SSH-användare. (SSH Standardanvändarnamnet är **sshuser**.)  

Mer information finns i [distribuera med PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template).

## <a name="deploy-with-cli"></a>Distribuera med CLI
I följande exempel används Azure-kommandoradsgränssnittet (CLI). Den skapar ett kluster och dess beroende lagringskontot och behållare genom att anropa en Resource Manager-mall:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"

Du uppmanas att ange:
* Klusternamnet.
* Klustret användarens lösenord. (Standardanvändarnamnet är **admin**.)
* SSH användarens lösenord. (SSH Standardanvändarnamnet är **sshuser**.)

Följande kod innehåller infogade parametrar:

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{\"clusterName\":{\"value\":\"hdi1229\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"},\"sshPassword\":{\"value\":\"Pass@word1\"}}'

## <a name="deploy-with-the-rest-api"></a>Distribuera med REST API
Se [distribuera med REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Distribuera med Visual Studio
 Du kan använda Visual Studio för att skapa en resursgruppsprojektet och distribuera den till Azure via användargränssnittet. Du kan välja vilken typ av resurser som ska ingå i projektet. Resurserna läggs automatiskt till Resource Manager-mallen. Projektet innehåller också ett PowerShell-skript för att distribuera mallen.

En introduktion till Visual Studio med resursgrupper, se [skapa och distribuera Azure-resursgrupper via Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig att skapa ett HDInsight-kluster på flera olika sätt. Mer information finns i följande artiklar:

* Flera HDInsight relaterade mallar finns i [Azure Quickstart mallar](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Ett exempel för att distribuera resurser via .NET-klientbiblioteket finns [distribuera resurser med hjälp av .NET-bibliotek och en mall](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* En detaljerad exempel för att distribuera ett program finns i [etablera och distribuera mikrotjänster förutsägbart i Azure](../app-service/app-service-deploy-complex-application-predictably.md).
* Information om hur du distribuerar lösningen till olika miljöer finns i [Utvecklings- och testmiljöer i Microsoft Azure](../solution-dev-test-environments.md).
* Läs om avsnitt i Azure Resource Manager-mall i [Webbsidemallar](../azure-resource-manager/resource-group-authoring-templates.md).
* En lista över de funktioner som du kan använda i en Azure Resource Manager-mall finns [Mallfunktioner](../azure-resource-manager/resource-group-template-functions.md).

## <a name="appendix-resource-manager-template-to-create-a-hadoop-cluster"></a>Bilaga: Resource Manager-mall för att skapa ett Hadoop-kluster
Följande mall för Azure Resource Manager skapar ett Hadoop-kluster med beroende Azure storage-konto.

> [!NOTE]
> Det här exemplet innehåller konfigurationsinformation för Hive metastore och Oozie metastore. Ta bort avsnittet eller konfigurera avsnittet innan du använder mallen.
>
>

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
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
        "defaultValue": "sshuser",
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
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.6",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).keys[0].value]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
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
                }
            ]
            }
        }
        }
    ],
    "outputs": {
        "cluster": {
        "type": "object",
        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
        }
    }
    }

## <a name="appendix-resource-manager-template-to-create-a-spark-cluster"></a>Bilaga: Resource Manager-mall för att skapa ett Spark-kluster

Det här avsnittet innehåller en Resource Manager-mall som du kan använda för att skapa ett HDInsight Spark-kluster. Den här mallen innehåller konfigurationer för `spark-defaults` och `spark-thrift-sparkconf` (för 1.6 Spark-kluster) och `spark2-defaults` och `spark2-thrift-sparkconf` (för 2 Spark-kluster). Utöver detta, HDInsight beräknas och anger konfigurationer som `spark.executor.instances`, `spark.executor.memory`, och `spark.executor.cores` utifrån klusterstorleken. 

Om du ställer in alla en parameter i ett avsnitt som en del av själva mallen inte HDInsight beräkna och ange andra parametrar i samma avsnitt. Till exempel parametern `spark.executor.instances` i den `spark-defaults` konfiguration. Om du anger en annan parametern (till exempel `spark.yarn.exector.memoryOverhead`) i den `spark-defaults` konfiguration, HDInsight inte beräkna och ange den `spark.executor.instances` samt parametern.

    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of the HDInsight cluster to create."
            }
        },
        "clusterLoginUserName": {
            "type": "string",
            "defaultValue": "admin",
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
        "location": {
            "type": "string",
            "defaultValue": "southcentralus",
            "metadata": {
                "description": "The location where all azure resources will be deployed."
            }
        },
        "clusterVersion": {
            "type": "string",
            "defaultValue": "3.6",
            "metadata": {
                "description": "HDInsight cluster version."
            }
        },
        "clusterWorkerNodeCount": {
            "type": "int",
            "defaultValue": 4,
            "metadata": {
                "description": "The number of nodes in the HDInsight cluster."
            }
        },
        "clusterKind": {
            "type": "string",
            "defaultValue": "SPARK",
            "metadata": {
                "description": "The type of the HDInsight cluster to create."
            }
        },
        "sshUserName": {
            "type": "string",
            "defaultValue": "sshuser",
            "metadata": {
                "description": "These credentials can be used to remotely access the cluster."
            }
        },
        "sshPassword": {
            "type": "securestring",
            "metadata": {
                "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
            }
        }
    },
    "variables": {
        "defaultApiVersion": "2017-06-01",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
    {
            "apiVersion": "2015-03-01-preview",
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "dependsOn": [],
            "properties": {
                "clusterVersion": "[parameters('clusterVersion')]",
                "osType": "Linux",
                "tier": "standard",
                "clusterDefinition": {
                    "kind": "[parameters('clusterKind')]",
                    "configurations": {
                        "gateway": {
                            "restAuthCredential.isEnabled": true,
                            "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                            "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                        },
                        "spark-defaults": {
                            "spark.executor.cores": "2"
                        },
                        "spark-thrift-sparkconf": {
                            "spark.yarn.executor.memoryOverhead": "896"
                        }
                    }
                },
                "storageProfile": {
                    "storageaccounts": [
                        {
                            "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                            "isDefault": true,
                            "container": "[parameters('clusterName')]",
                            "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).keys[0].value]"
                        }
                    ]
                },
                "computeProfile": {
                    "roles": [
                        {
                            "name": "headnode",
                            "minInstanceCount": 1,
                            "targetInstanceCount": 2,
                            "hardwareProfile": {
                                "vmSize": "Standard_D12"
                            },
                            "osProfile": {
                                "linuxOperatingSystemProfile": {
                                    "username": "[parameters('sshUserName')]",
                                    "password": "[parameters('sshPassword')]"
                                }
                            },
                            "virtualNetworkProfile": null,
                            "scriptActions": []
                        },
                        {
                            "name": "workernode",
                            "minInstanceCount": 1,
                            "targetInstanceCount": 4,
                            "hardwareProfile": {
                                "vmSize": "Standard_D4"
                            },
                            "osProfile": {
                                "linuxOperatingSystemProfile": {
                                    "username": "[parameters('sshUserName')]",
                                    "password": "[parameters('sshPassword')]"
                                    }
                                },
                                "virtualNetworkProfile": null,
                                "scriptActions": []
                            }
                        ]
                    }
                }
            }
        ]
    }
