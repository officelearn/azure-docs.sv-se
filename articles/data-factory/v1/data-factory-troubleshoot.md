---
title: Felsök Azure Data Factory-problem
description: Lär dig mer om att felsöka problem med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 5b9e0a30658e1cb8fe0f83d55c04f120637babaf
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549212"
---
# <a name="troubleshoot-data-factory-issues"></a>Felsök Data Factory-problem
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. 

Den här artikeln innehåller felsökningstips för problem när du använder Azure Data Factory. Den här artikeln visar inte alla möjliga problem när du använder tjänsten, men omfattar vissa problem och allmänna felsökningstips.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Felsökningstips
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Fel: Prenumerationen har inte registrerats för användning av namnområdet 'Microsoft.DataFactory ”
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

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problem: Behörighetsfel när du kör en Data Factory-cmdlet
Du använder förmodligen inte rätt Azure-konto eller -prenumeration med Azure PowerShell. Använd följande cmdlets för att välja rätt Azure-konto och -prenumeration för Azure PowerShell.

1. Ansluta-AzAccount – Använd rätt användar-ID och lösenord
2. Get-AzSubscription – visa alla prenumerationer för kontot.
3. Välj AzSubscription &lt;prenumerationsnamn&gt; – Välj rätt prenumeration. Använd samma som du använder för att skapa en datafabrik i Azure-portalen.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problem: Det gick inte att starta Expressinstallationen för Data Management Gateway från Azure-portalen
Expressinstallationen för Data Management Gateway kräver Internet Explorer eller en Microsoft ClickOnce-kompatibel webbläsare. Om det inte går att starta expressinstallationen, gör du något av följande:

* Använd Internet Explorer eller en Microsoft ClickOnce-kompatibel webbläsare.

    Om du använder Chrome, går du till [Chrome Web Store](https://chrome.google.com/webstore/), söker efter nyckelordet ”ClickOnce”, väljer ett av ClickOnce-tilläggen och installerar det.

    Gör likadant för Firefox (installera tillägget). Klicka på knappen Öppna menyn i verktygsfältet (tre horisontella linjer högst upp till höger), klicka på Tillägg, sök efter nyckelordet ”ClickOnce”, välj ett av ClickOnce-tilläggen och installera det.
* Använd den **manuell installation** länken som visas på samma blad i portalen. Du kan använda den här metoden för att hämta installationsfilen och köra den manuellt. När installationen har slutförts, visas dialogrutan Data Management Gateway-konfiguration. Kopiera **nyckeln** från portalskärmen och använd den i konfigurationshanteraren för att manuellt registrera gatewayen i tjänsten.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problem: Det gick inte att ansluta till en lokal SQL Server
Starta **Data Management Gateway Configuration Manager** på gatewaydatorn och Använd den **felsökning** flik för att testa anslutningen till SQL Server från gateway-datorn. Se [felsöka problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tips om hur du felsöker anslutning/gateway-relaterade problem.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problem: Indatasektorer är i väntestatus länge
Sektorer kan befinna sig i **väntar på** tillstånd på grund av olika orsaker. En vanlig orsak är att den **externa** egenskapen inte är inställt på **SANT**. Alla datauppsättningar som skapas utanför omfånget för Azure Data Factory ska markeras med **externa** egenskapen. Den här egenskapen anger att data är extern och inte backas upp av några pipelines i datafabriken. Datasektorerna markeras som **Klar** när datauppsättningen är tillgänglig i respektive lager.

I följande exempel kan du se hur egenskapen **external** används. Alternativt kan du ange **externalData*** när du ställer in external på true.

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

### <a name="problem-hybrid-copy-operation-fails"></a>Problem: Hybrid kopieringen misslyckas
Se [felsöka problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) steg för att felsöka problem med att kopiera till/från en lokal lagra i med hjälp av Data Management Gateway.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problem: På begäran HDInsight etablering misslyckas
När du använder en länkad tjänst av typen HDInsightOnDemand, måste du ange ett linkedServiceName som pekar på en Azure Blob Storage. Data Factory-tjänsten använder den här lagringen för att lagra loggar och stödfiler för HDInsight-klustret på begäran.  Ibland går det inte att etablera ett HDInsight-kluster på begäran på grund av följande fel:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Det här felet innebär oftast att lagringskontots plats som anges i linkedServiceName inte är på samma datacenterplats där HDInsight-etableringen utförs. Exempel: om din data factory är i västra USA och Azure storage finns i USA, östra, etablering på begäran misslyckas i västra USA.

Dessutom finns det en andra JSON-egenskap, additionalLinkedServiceNames, där ytterligare lagringskonton kan anges i HDInsight på begäran. Dessa ytterligare länkade lagringskonton bör vara på samma plats som HDInsight-kluster eller misslyckas med samma fel.

### <a name="problem-custom-net-activity-fails"></a>Problem: Anpassad .NET-aktivitet misslyckas
Se [felsöka en pipeline med anpassad aktivitet](data-factory-use-custom-activities.md#troubleshoot-failures) detaljerade anvisningar.

## <a name="use-azure-portal-to-troubleshoot"></a>Felsöka med hjälp av Azure portal
### <a name="using-portal-blades"></a>Med-portalblad
Se [övervaka pipeline](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline) anvisningar.

### <a name="using-monitor-and-manage-app"></a>Övervaka och hantera app
Se [övervaka och hantera data factory-pipelines med appen övervaka och hantera](data-factory-monitor-manage-app.md) mer information.

## <a name="use-azure-powershell-to-troubleshoot"></a>Använd Azure PowerShell för att felsöka
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Använd Azure PowerShell för att felsöka ett fel
Se [övervaka Data Factory-pipelines med Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) mer information.

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
