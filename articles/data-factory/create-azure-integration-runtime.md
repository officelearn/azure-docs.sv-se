---
title: Skapa Azure integration runtime i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar med Azure integration runtime i Azure Data Factory, som används för att kopiera data och skicka transformeringsaktiviteter.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: douglasl
ms.openlocfilehash: 93777a3d9ecbb6c2749594985494975f61cc1948
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455708"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Hur du skapar och konfigurerar Azure Integration Runtime
Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla funktioner för dataintegrering i olika nätverksmiljöer. Mer information om IR finns i [integreringskörningen](concepts-integration-runtime.md).

Azure IR ger en helt hanterad beräkning om du vill utföra internt förflyttning och dispatch data datatransformeringsaktiviteter för Beräkningstjänster som HDInsight. Det finns i Azure-miljö och stöder anslutning till resurser i offentliga nätverk med offentligt tillgängliga slutpunkter.

Det här dokumentet beskriver hur du kan skapa och konfigurera Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Standard Azure IR
Som standard har alla datafabriker en Azure IR i serverdelen som har stöd för åtgärder på molnet datalager och Beräkningstjänster i offentligt nätverk. Platsen för den Azure IR är löses. Om **connectVia** egenskapen har inte angetts i den länkade tjänstdefinitionen standard Azure IR används. Du behöver bara skapa en Azure IR explicit när du vill att explicit definiera platsen för IR, eller om du vill att gruppen i stort sett aktivitet körningar på olika IRs för hantering av ändamål. 

## <a name="create-azure-ir"></a>Skapa Azure IR
Integration Runtime kan skapas med den **Set-AzDataFactoryV2IntegrationRuntime** PowerShell-cmdlet. För att skapa en Azure IR måste ange du namn, plats och typ i kommandot. Här är en exempel-kommando för att skapa en Azure IR med platsen angetts till ”Europa, västra”:

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Om Azure IR typen måste vara inställt på **hanterade**. Du behöver inte ange beräkning information eftersom den är fullständigt hanterad Elastiskt i molnet. Ange beräkning information som nodstorlek och noden antalet när du vill skapa Azure-SSIS IR. Mer information finns i [skapa och konfigurera Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Du kan konfigurera en befintlig Azure IR för att ändra dess plats med hjälp av Set-AzDataFactoryV2IntegrationRuntime PowerShell-cmdleten. Mer information om platsen för en Azure IR finns i [introduktion till integration runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Använd Azure IR

När en Azure IR har skapats kan använda du det i din länkade tjänstdefinitionen. Nedan är ett exempel på hur du kan referera till Azure Integration Runtime skapade ovan från ett Azure Storage-länkade tjänsten:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar om hur du skapar andra typer av integration Runtime:

- [Skapa Integration Runtime med egen värd](create-self-hosted-integration-runtime.md)
- [Skapa Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md)
 
