---
title: Skapa Azure-integreringskörning i Azure Data Factory
description: Lär dig hur du skapar Azure-integreringskörning i Azure Data Factory, som används för att kopiera data och skicka transformeringsaktiviteter.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 87633abaaae1f6034709c6e552be6647533115ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260766"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Så här skapar och konfigurerar du Azure Integration Runtime
Integration Runtime (IR) är den beräkningsinfrastruktur som används av Azure Data Factory för att tillhandahålla dataintegrationsfunktioner i olika nätverksmiljöer. Mer information om IR finns i [Integrationskörning](concepts-integration-runtime.md).

Azure IR tillhandahåller en fullständigt hanterad beräkning för att utföra dataförflyttning och skicka dataomvandlingsaktiviteter till beräkningstjänster som HDInsight. Den finns i Azure-miljö och stöder anslutning till resurser i offentlig nätverksmiljö med offentliga slutpunkter.

Det här dokumentet introducerar hur du kan skapa och konfigurera Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Standard Azure IR
Som standard har varje datafabrik en Azure IR i serveringen som stöder åtgärder på molndatalager och beräkningstjänster i det offentliga nätverket. Platsen för den Azure IR-er är automatisk lösning. Om **connectVia-egenskapen** inte anges i den länkade tjänstdefinitionen används standardvärdet för Azure IR. Du behöver bara uttryckligen skapa en Azure IR när du vill uttryckligen definiera platsen för IR, eller om du vill praktiskt taget gruppera aktivitetskörningarna på olika IR:er för hanteringsändamål. 

## <a name="create-azure-ir"></a>Skapa Azure IR
Integration Runtime kan skapas med hjälp av **Set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet. Om du vill skapa en Azure IR anger du namn, plats och typ till kommandot. Här är ett exempelkommando för att skapa en Azure IR med plats inställd på "Västeuropa":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
För Azure IR måste typen anges till **Hanterad**. Du behöver inte ange beräkningsinformation eftersom den hanteras helt elastiskt i molnet. Ange beräkningsinformation som nodstorlek och nodantal när du vill skapa Azure-SSIS IR. Mer information finns i [Skapa och konfigurera Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Du kan konfigurera en befintlig Azure IR för att ändra dess plats med hjälp av Cmdlet Set-AzDataFactoryV2IntegrationRuntime PowerShell. Mer information om platsen för en Azure IR finns i [Introduktion till integrationskörning](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Använda Azure IR

När en Azure IR har skapats kan du referera till den i definitionen av länkad tjänst. Nedan följer ett exempel på hur du kan referera till Azure Integration Runtime som skapats ovan från en Azure Storage Linked Service:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar om hur du skapar andra typer av integrationskörningar:

- [Skapa Integration Runtime med egen värd](create-self-hosted-integration-runtime.md)
- [Skapa Azure SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)
 
