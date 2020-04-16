---
title: Skapa Azure-integreringskörning i Azure Data Factory
description: Lär dig hur du skapar Azure-integreringskörning i Azure Data Factory, som används för att kopiera data och skicka transformeringsaktiviteter.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/13/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: e32530ece3626807b199850a2b4af5461ff51cde
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414066"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Så här skapar och konfigurerar du Azure Integration Runtime
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) är den beräkningsinfrastruktur som används av Azure Data Factory för att tillhandahålla dataintegrationsfunktioner i olika nätverksmiljöer. Mer information om IR finns i [Integrationskörning](concepts-integration-runtime.md).

Azure IR tillhandahåller en fullständigt hanterad beräkning för att utföra dataförflyttning och skicka dataomvandlingsaktiviteter till beräkningstjänster som HDInsight. Den finns i Azure-miljö och stöder anslutning till resurser i offentlig nätverksmiljö med offentliga slutpunkter.

Det här dokumentet introducerar hur du kan skapa och konfigurera Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Standard Azure IR
Som standard har varje datafabrik en Azure IR i serveringen som stöder åtgärder på molndatalager och beräkningstjänster i det offentliga nätverket. Platsen för den Azure IR-er är automatisk lösning. Om **connectVia-egenskapen** inte anges i den länkade tjänstdefinitionen används standardvärdet för Azure IR. Du behöver bara uttryckligen skapa en Azure IR när du vill uttryckligen definiera platsen för IR, eller om du vill praktiskt taget gruppera aktivitetskörningarna på olika IR:er för hanteringsändamål. 

## <a name="create-azure-ir"></a>Skapa Azure IR

Om du vill skapa och konfigurera en Azure IR kan du använda följande procedurer.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Skapa en Azure IR via Azure PowerShell
Integration Runtime kan skapas med hjälp av **Set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet. Om du vill skapa en Azure IR anger du namn, plats och typ till kommandot. Här är ett exempelkommando för att skapa en Azure IR med plats inställd på "Västeuropa":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
För Azure IR måste typen anges till **Hanterad**. Du behöver inte ange beräkningsinformation eftersom den hanteras helt elastiskt i molnet. Ange beräkningsinformation som nodstorlek och nodantal när du vill skapa Azure-SSIS IR. Mer information finns i [Skapa och konfigurera Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Du kan konfigurera en befintlig Azure IR för att ändra dess plats med hjälp av Cmdlet Set-AzDataFactoryV2IntegrationRuntime PowerShell. Mer information om platsen för en Azure IR finns i [Introduktion till integrationskörning](concepts-integration-runtime.md).

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Skapa ett Azure IR via Azure Data Factory UI
Följ följande steg för att skapa en Azure IR med Azure Data Factory UI.

1. På sidan **Låt oss komma igång i** Azure Data Factory-användargränssnittet väljer du fliken **Författare** i den vänstra rutan.

   ![Knappen Författarsida på startsidan](media/doc-common-process/get-started-page-author-button.png)

1. Välj **Anslutningar** längst ned i den vänstra rutan och välj **Integrationskörningar** i fönstret **Anslutningar.** Välj **+Nytt**.

   ![Skapa Integration Runtime](media/create-azure-integration-runtime/new-integration-runtime.png)

1. På **inställningssidan för integreringskörning** väljer du **Azure, Självvärd och**väljer sedan **Fortsätt**. 

1. På följande sida väljer du **Azure** för att skapa en Azure IR och väljer sedan **Fortsätt**.
   ![Skapa Integration Runtime](media/create-azure-integration-runtime/new-azure-ir.png)

1. Ange ett namn för din Azure IR och välj **Skapa**.
   ![Skapa en Azure IR](media/create-azure-integration-runtime/create-azure-ir.png)

1. Du ser ett popup-meddelande när skapandet är klart. På sidan **Integration runtimes** kontrollerar du att du ser den nyskapade IR:et i listan.

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
 
