---
title: Felsöka Problem med Azure Data Factory
description: Lär dig hur du felsöker problem med att använda Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
robots: noindex
ms.openlocfilehash: 81ae5c3c702108d854e4dfde93001d5c99875666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931588"
---
# <a name="troubleshoot-data-factory-issues"></a>Felsök Data Factory-problem
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. 

Den här artikeln innehåller felsökningstips för problem när du använder Azure Data Factory. Den här artikeln innehåller inte alla möjliga problem när du använder tjänsten, men den täcker vissa problem och allmänna felsökningstips.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Felsökningstips
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Fel: Prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory
Om du får det här felmeddelandet har inte Azure Data Factory-resursprovidern registrerats på datorn. Gör följande:

1. Starta Azure PowerShell.
2. Logga in på ditt Azure-konto med följande kommando.

    ```powershell
    Connect-AzAccount
    ```
3. Kör följande kommando för att registrera Azure Data Factory-providern.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problem: Obehörigt fel när du kör en Data Factory-cmdlet
Du använder förmodligen inte rätt Azure-konto eller -prenumeration med Azure PowerShell. Använd följande cmdlets för att välja rätt Azure-konto och -prenumeration för Azure PowerShell.

1. Connect-AzAccount - Använd rätt användar-ID och lösenord
2. Get-AzSubscription - Visa alla prenumerationer för kontot.
3. Prenumerationsnamn &lt;&gt; för Select-AzSubscription - Välj rätt prenumeration. Använd samma som du använder för att skapa en datafabrik på Azure-portalen.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problem: Det gick inte att starta datahanteringsgateway expressinställningar från Azure-portalen
Expressinstallationen för Data Management Gateway kräver Internet Explorer eller en Microsoft ClickOnce-kompatibel webbläsare. Om det inte går att starta expressinstallationen, gör du något av följande:

* Använd Internet Explorer eller en Microsoft ClickOnce-kompatibel webbläsare.

    Om du använder Chrome, går du till [Chrome Web Store](https://chrome.google.com/webstore/), söker efter nyckelordet ”ClickOnce”, väljer ett av ClickOnce-tilläggen och installerar det.

    Gör samma sak för Firefox (installera tillägg). Klicka på knappen Öppna menyn i verktygsfältet (tre horisontella linjer högst upp till höger), klicka på Tillägg, sök efter nyckelordet ”ClickOnce”, välj ett av ClickOnce-tilläggen och installera det.
* Använd länken **Manuell installation** som visas på samma blad i portalen. Du använder den här metoden för att hämta installationsfilen och köra den manuellt. När installationen har slutförts visas dialogrutan Konfiguration av datahanteringsgateway. Kopiera **nyckeln** från portalskärmen och använd den i konfigurationshanteraren för att manuellt registrera gatewayen i tjänsten.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problem: Det gick inte att ansluta till lokala SQL Server
Starta **Konfigurationshanteraren för datahanteringsgateway** på gateway-datorn och använd fliken **Felsökning** för att testa anslutningen till SQL Server från gateway-datorn. Se [Felsöka gatewayproblem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) för tips om felsökning av anslutnings-/gatewayrelaterade problem.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problem: Indatasegment är i vänteläge för alltid
Segmenten kan vara i **vänteläge** på grund av olika orsaker. En av de vanligaste orsakerna är att den **externa** egenskapen inte är inställd på **true**. Alla datauppsättningar som produceras utanför Azure Data Factorys omfattning bör markeras med **extern** egenskap. Den här egenskapen anger att data är externa och inte backas upp av några pipelines i datafabriken. Datasektorerna markeras som **Klar** när datauppsättningen är tillgänglig i respektive lager.

I följande exempel kan du se hur egenskapen **external** används. Du kan också ange **externaData*** när du ställer in externt till true.

Mer info om den här egenskapen finns i artikeln om [datauppsättningar](data-factory-create-datasets.md).

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Lös problemet genom att lägga till egenskapen **external** och det valfria avsnittet **externalData** i indatatabellens JSON-definition. Återskapa sedan tabellen.

### <a name="problem-hybrid-copy-operation-fails"></a>Problem: Hybridkopieringsåtgärden misslyckas
Se [Felsöka gatewayproblem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) för felsökning av problem med kopiering till/från ett lokalt datalager med hjälp av Data Management Gateway.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problem: HDInsight-etablering på begäran misslyckas
När du använder en länkad tjänst av typen HDInsightOnDemand, måste du ange ett linkedServiceName som pekar på en Azure-blobblagring. Data Factory-tjänsten använder den här lagringen för att lagra loggar och stödfiler för HDInsight-klustret på begäran.  Ibland går det inte att etablera ett HDInsight-kluster på begäran på grund av följande fel:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Det här felet innebär oftast att lagringskontots plats som anges i linkedServiceName inte är på samma datacenterplats där HDInsight-etableringen utförs. Exempel: om din datafabrik finns i västra USA och Azure-lagringen finns i östra USA misslyckas etableringen på begäran i västra USA.

Dessutom finns det en andra JSON-egenskap, additionalLinkedServiceNames, där ytterligare lagringskonton kan anges i HDInsight på begäran. Dessa ytterligare länkade lagringskonton bör vara på samma plats som HDInsight-klustret, eller så misslyckas det med samma fel.

### <a name="problem-custom-net-activity-fails"></a>Problem: Anpassad .NET-aktivitet misslyckas
Se [Felsöka en pipeline med anpassad aktivitet](data-factory-use-custom-activities.md#troubleshoot-failures) för detaljerade steg.

## <a name="use-azure-portal-to-troubleshoot"></a>Använda Azure-portalen för felsökning
### <a name="using-portal-blades"></a>Använda portalblad
Se [Övervaka pipeline](data-factory-monitor-manage-pipelines.md) för steg.

### <a name="using-monitor-and-manage-app"></a>Övervaka och hantera app
Mer information [finns i Övervaka och hantera pipelines för datafabriker med hjälp av Monitor och Manage App.](data-factory-monitor-manage-app.md)

## <a name="use-azure-powershell-to-troubleshoot"></a>Använd Azure PowerShell för felsökning
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Använda Azure PowerShell för att felsöka ett fel
Mer information [finns i Pipelines för bildskärmsdatafabrik med Azure PowerShell.](data-factory-monitor-manage-pipelines.md)

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: https://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
