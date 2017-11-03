---
title: "PowerShell-skript-omvandla data i molnet med hjälp av Data Factory | Microsoft Docs"
description: "Detta PowerShell-skript transformerar data i molnet genom att köra Spark-program på Azure HDInsight Spark-kluster."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: spelluru
ms.openlocfilehash: 195b7276346827479fbbe10dfaaaa9ed1d754967
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell-skript - Transformera data i molnet med Azure Data Factory

PowerShell-exempelskriptet skapar en pipeline som omvandlar data i molnet genom att köra Spark-program på Azure HDInsight Spark-kluster. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Krav
* **Azure Storage-konto**. Skapa en python-skript och en indatafil och överför dem till Azure-lagring. Spark-programmets utdata lagras på det här lagringskontot. Spark-klustret på begäran använder samma lagringskonto som den primära lagringen.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Överföra Python-skriptet till ditt Blob Storage-konto
1. Skapa en Python-fil med namnet **WordCount_Spark.py** med följande innehåll: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Ersätt **&lt;storageAccountName&gt;** med namnet på ditt Azure-konto. Spara sedan filen. 
3. Skapa en behållare med namnet **adftutorial** i Azure Blob Storage om den inte finns. 
4. Skapa en mapp med namnet **spark**.
5. Skapa en undermapp med namnet **script** under mappen **spark**. 
6. Överför filen **WordCount_Spark.py** till undermappen **script**. 


### <a name="upload-the-input-file"></a>Överför indatafilen
1. Skapa en fil med namnet **minecraftstory.txt** med lite text. Spark-programmet räknar antalet ord i texten. 
2. Skapa en undermapp som heter `inputfiles` i den `spark` för blob-behållaren. 
3. Överför `minecraftstory.txt` till mappen `inputfiles`. 

## <a name="sample-script"></a>Exempelskript
> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory-enheter (länkade tjänsten dataset och pipeline) på din hårddisk i mappen c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Rensa distribution

Du kan använda följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den när du har kört exempelskript:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Ta bort datafabriken från resursgruppen genom att köra följande kommando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Ange AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2) | Skapa en datafabrik. |
| [Ange AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedservice) | Skapar en länkad tjänst i datafabriken. En länkad tjänst länkar en datalagret eller beräkning till en data factory. |
| [Ange AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactorv2ypipeline) | Skapar en pipeline i datafabriken. En pipeline innehåller en eller flera aktiviteter som utför en viss åtgärd. I den här pipelinen omvandlar en spark-aktivitet data genom att köra ett program på Azure HDInsight Spark-kluster. |
| [Anropa AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2pipelinerun) | Skapar en körning för pipeline. Med andra ord körs pipeline. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Hämtar information om körning av aktiviteten (aktivitet kör) i pipelinen. 
| [Ta bort AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns [Azure PowerShell dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Factory PowerShell skriptexempel finns i den [Azure Data Factory PowerShell-exempel](../samples-powershell.md).