---
title: Skapa Azure integration runtime i Azure Data Factory
description: Lär dig hur du skapar Azure integration runtime i Azure Data Factory, som används för att kopiera data och skicka Transform-aktiviteter.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387736"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Så här skapar och konfigurerar du Azure Integration Runtime
Integration Runtime (IR) är den beräknings infrastruktur som används av Azure Data Factory för att tillhandahålla funktioner för data integrering i olika nätverks miljöer. Mer information om IR finns i [integration runtime](concepts-integration-runtime.md).

Azure IR ger en fullständigt hanterad beräkning för att internt utföra data förflyttning och omvandlings aktiviteter för att skicka data till beräknings tjänster som HDInsight. Den finns i Azure-miljön och har stöd för att ansluta till resurser i en offentlig nätverks miljö med offentliga tillgängliga slut punkter.

Det här dokumentet beskriver hur du kan skapa och konfigurera Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Standard Azure IR
Som standard har varje data fabrik en Azure IR i Server delen som stöder åtgärder i moln data lager och beräknings tjänster i offentliga nätverk. Platsen för Azure IR löses automatiskt. Om egenskapen **connectVia** inte anges i den länkade tjänst definitionen används standard Azure IR. Du behöver bara skapa en Azure IR när du uttryckligen vill definiera platsen för IR, eller om du vill gruppera aktivitets körningarna i olika IRs i hanterings syfte. 

## <a name="create-azure-ir"></a>Skapa Azure IR
Integration Runtime kan skapas med PowerShell-cmdleten **set-AzDataFactoryV2IntegrationRuntime** . Om du vill skapa en Azure IR anger du namnet, platsen och typen för kommandot. Här är ett exempel kommando för att skapa en Azure IR med plats inställt på "Västeuropa":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
För Azure IR måste typen anges som **hanterad**. Du behöver inte ange beräknings information eftersom den hanteras helt elastiskt i molnet. Ange beräknings information som Node-storlek och antal noder när du vill skapa Azure-SSIS IR. Mer information finns i [skapa och konfigurera Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Du kan konfigurera en befintlig Azure IR att ändra dess plats med hjälp av cmdleten Set-AzDataFactoryV2IntegrationRuntime PowerShell. Mer information om platsen för en Azure IR finns i [Introduktion till integration runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Använd Azure IR

När en Azure IR har skapats kan du referera till den i den länkade tjänst definitionen. Nedan visas ett exempel på hur du kan referera till Azure Integration Runtime som skapats ovan från en Azure Storage länkad tjänst:  

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
Se följande artiklar om hur du skapar andra typer av integrerings körningar:

- [Skapa Integration Runtime med egen värd](create-self-hosted-integration-runtime.md)
- [Skapa Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md)
 
