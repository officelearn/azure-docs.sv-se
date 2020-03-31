---
title: Använda Azure Data Explorer-kontrollkommandon i Azure Data Factory
description: I det här avsnittet använder du Azure Data Explorer-kontrollkommandon i Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264488"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Använda kommandoaktiviteten i Azure Data Factory för att köra Azure Data Explorer-kontrollkommandon

[Azure Data Factory](/azure/data-factory/) (ADF) är en molnbaserad dataintegrationstjänst som gör att du kan utföra en kombination av aktiviteter på data. Använd ADF för att skapa datadrivna arbetsflöden för att dirigera och automatisera dataförflyttning och dataomvandling. Med **kommandoaktiviteten för Azure Data Explorer** i Azure Data Factory kan du köra Azure Data [Explorer-kontrollkommandon](/azure/kusto/concepts/#control-commands) i ett ADF-arbetsflöde. I den här artikeln får du lära dig hur du skapar en pipeline med en uppslagsaktivitet och ForEach-aktivitet som innehåller en kommandoaktivitet för Azure Data Explorer.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Data Explorer-kluster och -databas](create-cluster-database-portal.md)
* En datakälla.
* [En datafabrik](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Skapa en ny pipeline

1. Välj **Author** verktyget Författarpenna. 
1. Skapa en ny pipeline **+** genom att välja och välj sedan **Pipeline** i listrutan.

   ![skapa ny pipeline](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Skapa en uppslagsaktivitet

En [uppslagsaktivitet](/azure/data-factory/control-flow-lookup-activity) kan hämta en datauppsättning från alla Azure Data Factory-stödda datakällor. Utdata från uppslagsaktiviteten kan användas i en ForEach eller annan aktivitet.

1. Välj **uppslagsaktiviteten** under **Allmänt**i fönstret **Aktiviteter.** Dra och släpp den i huvudduken till höger.
 
    ![välj uppslagsaktivitet](media/data-factory-command-activity/select-activity.png)

1. Arbetsytan innehåller nu den uppslagsaktivitet som du har skapat. Använd flikarna under arbetsytan för att ändra eventuella relevanta parametrar. Byt namn på aktiviteten i **allmänhet.** 

    ![redigera uppslagsaktivitet](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Klicka på det tomma arbetsytan för att visa pipeline-egenskaperna. Använd fliken **Allmänt** för att byta namn på pipelinen. Vår pipeline heter *pipeline-4-docs.*

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Skapa en Azure Data Explorer-datauppsättning i uppslagsaktivitet

1. I **Inställningar**väljer du den förskapade Azure Data Explorer **Source-datauppsättningen**eller väljer **+ Ny** för att skapa en ny datauppsättning.
 
    ![lägga till datauppsättning i uppslagsinställningar](media/data-factory-command-activity/lookup-settings.png)

1. Välj **Data Explorer -datauppsättningen (Azure Data Explorer)** från fönstret **Ny datauppsättning.** Välj **Fortsätt** om du vill lägga till den nya datauppsättningen.

   ![välj ny datauppsättning](media/data-factory-command-activity/select-new-dataset.png) 

1. De nya Azure Data Explorer-datauppsättningsparametrarna visas i **Inställningar**. Om du vill uppdatera parametrarna väljer du **Redigera**.

    ![uppslagsinställningar med Azure Data Explorer-datauppsättning](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Fliken **AzureDataExplorerTable** öppnas på huvudarbetsytan. 
    * Välj **Allmänt** och redigera datauppsättningsnamnet. 
    * Välj **Anslutning** om du vill redigera datauppsättningsegenskaperna. 
    * Välj den **länkade tjänsten** i listrutan eller välj **+ Ny** om du vill skapa en ny länkad tjänst.

    ![Redigera datauppsättningsegenskaper för Azure Data Explorer](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. När du skapar en ny länkad tjänst öppnas sidan **Ny länkad tjänst (Azure Data Explorer):**

    ![ADX ny länkad tjänst](media/data-factory-command-activity/adx-new-linked-service.png)

   * Välj **Namn** för azure data explorer-länkad tjänst. Lägg till **beskrivning** om det behövs.
   * Ändra aktuella inställningar om det behövs i **Anslut via integreringskörning.** 
   * I **Kontovalsmetod** väljer du ditt kluster med en av två metoder: 
        * Välj knappen **Från Azure-prenumerationsradio** och välj ditt **Azure-prenumerationskonto.** Välj sedan ditt **kluster**. Listrutan listar bara kluster som tillhör användaren.
        * Välj i stället alternativknappen **Ange manuellt** och ange **slutpunkten** (kluster-URL: en).
    * Ange **klienten**.
    * Ange **tjänstens huvud-ID**. Huvud-ID:t måste ha tillräcklig behörighet, enligt den behörighetsnivå som krävs av kommandot som används.
    * Välj **knappen Huvudnyckel** för Tjänsten och ange **Nyckel till tjänstens huvudnamn**.
    * Välj **databasen** på rullgardinsmenyn. Du kan också markera **kryssrutan Redigera** och ange databasnamnet.
    * Välj **Testa anslutning** om du vill testa den länkade tjänstanslutning som du skapade. Om du kan ansluta till konfigurationen visas en grön bock **Anslutning.**
    * Välj **Slutför** om du vill skapa länkade tjänster.

1. När du har konfigurerat en länkad tjänst lägger du till **tabellnamn** i **AzureDataExplorerTable-anslutning.** > **Connection** Välj **Förhandsgranska data**för att kontrollera att data visas korrekt.

   Datauppsättningen är nu klar och du kan fortsätta redigera pipelinen.

### <a name="add-a-query-to-your-lookup-activity"></a>Lägga till en fråga i uppslagsaktiviteten

1. I **pipeline-4-docs-inställningar** > **Settings** lägger du till en fråga **i** frågetextrutan, till exempel:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Ändra egenskaperna **Fråga timeout** eller **Inga trunkering** och **Första raden bara,** efter behov. I det här flödet behåller vi **standardtidsgränsen för fråga** och avmarkerar kryssrutorna. 

    ![Slutliga inställningar för uppslagsaktivitet](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Skapa en för varje aktivitet 

[För-varje-aktiviteten](/azure/data-factory/control-flow-for-each-activity) används för att iterera över en samling och utföra angivna aktiviteter i en loop. 

1. Nu kan du lägga till en för varje aktivitet i pipelinen. Den här aktiviteten bearbetar data som returneras från uppslagsaktiviteten. 
    * Markera **aktiviteten ForEach** under **Iteration & villkor i**fönstret **Aktiviteter** och dra och släppa den i arbetsytan.
    * Rita en linje mellan utdata för uppslagsaktiviteten och indata för forEach-aktiviteten på arbetsytan för att ansluta dem.

        ![ForEach-aktivitet](media/data-factory-command-activity/for-each-activity.png)

1.  Välj forEach-aktiviteten på arbetsytan. På fliken **Inställningar** nedan:
    * Markera kryssrutan **Sekventiell** för en sekventiell bearbetning av uppslagsresultaten eller lämna den avmarkerad för att skapa parallell bearbetning.
    * Ange **antal batchar**.
    * I **Objekt**anger du följande referens till utdatavärdet: * @activity('Lookup1').output.value*

       ![ForEach-aktivitetsinställning](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Skapa en kommandoaktivitet för Azure Data Explorer i forEach-aktiviteten

1. Dubbelklicka på forEach-aktiviteten på arbetsytan för att öppna den på en ny arbetsyta för att ange aktiviteterna i ForEach.
1. Välj kommandoaktiviteten för Azure Data **Explorer** under **Azure**Data Explorer i fönstret **Aktiviteter** och dra och släppa den på arbetsytan.

    ![Kommandoaktivitet i Azure Data Explorer](media/data-factory-command-activity/adx-command-activity.png)

1.  Välj samma länkade tjänst som tidigare skapats på fliken **Anslutning.**

    ![fliken kommandoaktivitetsanslutning för azure-datautforskare](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Ange följande kommando på fliken **Kommando:**

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **Kommandot** instruerar Azure Data Explorer att exportera resultaten av en viss fråga till en blob-lagring, i ett komprimerat format. Den körs asynkront (med hjälp av async modifieraren).
    Frågan behandlar databaskolumnen för varje rad i uppslagsaktivitetsresultatet. **Tidsgränsen för kommandot** kan lämnas oförändrad.

    ![kommandoaktivitet](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > Kommandoaktiviteten har följande gränser:
    > * Storleksgräns: 1 MB svarsstorlek
    > * Tidsgräns: 20 minuter (standard), 1 timme (max).
    > * Om det behövs kan du lägga till en fråga i resultatet med [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands)för att minska den resulterande storleken/tiden.

1.  Nu är pipelinen klar. Du kan gå tillbaka till huvudpipelinen genom att klicka på pipelinenamnet.

    ![Kommandopipeline för Azure Data Explorer](media/data-factory-command-activity/adx-command-pipeline.png)

1. Välj **Felsöka** innan du publicerar pipelinen. Pipelinens förlopp kan övervakas på fliken **Utdata.**

    ![kommandoaktivitetsutdatadatadatadata för azure data explorer](media/data-factory-command-activity/command-activity-output.png)

1. Du kan **publicera alla** och sedan lägga **till utlösare** för att köra pipelinen. 

## <a name="control-command-outputs"></a>Utdata för kontrollkommando

Strukturen för kommandoaktivitetsutdata beskrivs nedan. Den här utdata kan användas av nästa aktivitet i pipelinen.

### <a name="returned-value-of-a-non-async-control-command"></a>Returnerat värde för ett icke-asynkron kontrollkommando

I ett icke-asynkron kontrollkommando liknar strukturen för det returnerade värdet strukturen för uppslagsaktivitetsresultatet. Fältet `count` anger antalet returnerade poster. Ett fast `value` matrisfält innehåller en lista med poster. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Returnerat värde för ett asynkronkontrollkommando

I ett async-kontrollkommando avföljer aktivitetstabellen bakom kulisserna tills asynkron åtgärden har slutförts eller time-out. Därför kommer det returnerade värdet `.show operations OperationId` att innehålla resultatet av för den angivna **OperationId-egenskapen.** Kontrollera värdena för **statusegenskaper** och **status** för att kontrollera att åtgärden har slutförts.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du [kopierar data till Azure Data Explorer med Azure Data Factory](data-factory-load-data.md).
* Lär dig mer om hur du använder [Azure Data Factory-mall för masskopia från databas till Azure Data Explorer](data-factory-template.md).
