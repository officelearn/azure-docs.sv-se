---
title: Använd Azure Datautforskaren Control-kommandon i Azure Data Factory
description: I det här avsnittet använder du Azure Datautforskaren Control-kommandon i Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 316ddbf662a5418e54f37cb335475a86c50118c7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131446"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Använd Azure Data Factory kommando aktivitet för att köra Azure Datautforskaren Control-kommandon

[Azure Data Factory](/azure/data-factory/) (ADF) är en molnbaserad data integrerings tjänst som gör att du kan utföra en kombination av aktiviteter på data. Använd ADF för att skapa data drivna arbets flöden för att dirigera och automatisera data förflyttning och data omvandling. Med **azure datautforskaren kommando** aktiviteten i Azure Data Factory kan du köra [Azure datautforskaren kontroll kommandon](/azure/kusto/concepts/#control-commands) i ett ADF-arbetsflöde. Den här artikeln lär dig hur du skapar en pipeline med en söknings aktivitet och en förgrunds aktivitet som innehåller en Azure Datautforskaren-kommando aktivitet.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Datautforskaren-kluster och-databas](create-cluster-database-portal.md)
* En data källa.
* [En data fabrik](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Skapa en ny pipeline

1. Välj Penn verktyget **Author** . 
1. Skapa en ny pipeline genom att välja **+** och välj sedan **pipeline** i list rutan.

   ![Skapa ny pipeline](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Skapa en söknings aktivitet

1. I rutan **aktiviteter** under **Allmänt**väljer du **Lookup** -aktiviteten. Dra och släpp det i huvud arbets ytan till höger.
 
    ![Välj söknings aktivitet](media/data-factory-command-activity/select-activity.png)

1. Arbets ytan innehåller nu den uppslags aktivitet som du har skapat. Använd flikarna under arbets ytan för att ändra relevanta parametrar. I **allmänhet**byter du namn på aktiviteten. 

    ![redigera söknings aktivitet](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Klicka på det tomma arbets området om du vill visa egenskaperna för pipelinen. Använd fliken **Allmänt** för att byta namn på pipelinen. Vår pipeline heter *pipeline-4-dokument*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Skapa en data uppsättning för Azure Datautforskaren i lookup-aktivitet

1. I **Inställningar**väljer du den redan skapade **käll data uppsättningen**för Azure datautforskaren eller väljer **+ ny** för att skapa en ny data uppsättning.
 
    ![Lägg till data uppsättning i uppslags inställningar](media/data-factory-command-activity/lookup-settings.png)

1. Välj data uppsättningen **Azure datautforskaren (Kusto)** från fönstret **ny data uppsättning** . Välj **Fortsätt** för att lägga till den nya data uppsättningen.

   ![Välj ny data mängd](media/data-factory-command-activity/select-new-dataset.png) 

1. De nya parametrarna för Azure Datautforskaren data uppsättningen visas i **Inställningar**. Om du vill uppdatera parametrarna väljer du **Redigera**.

    ![Sök inställningar med Azure Datautforskaren data uppsättning](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Fliken **AzureDataExplorerTable** ny öppnas på huvud arbets ytan. 
    * Välj **Allmänt** och redigera data uppsättningens namn. 
    * Välj **anslutning** för att redigera egenskaperna för data uppsättningen. 
    * Välj den **länkade tjänsten** i list rutan eller Välj **+ ny** för att skapa en ny länkad tjänst.

    ![Redigera egenskaper för Azure Datautforskaren-datauppsättning](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. När du skapar en ny länkad tjänst öppnas sidan **ny länkad tjänst (Azure datautforskaren)** :

    ![ADX ny länkad tjänst](media/data-factory-command-activity/adx-new-linked-service.png)

   * Välj **namn** för den länkade tjänsten Azure-datautforskaren. Lägg till **Beskrivning** vid behov.
   * Ändra aktuella inställningar vid behov i **Anslut via integration runtime**. 
   * I **Val av konto** väljer du klustret med hjälp av någon av följande två metoder: 
        * Välj alternativ knappen **från Azure-prenumerationen** och välj ditt **Azure-prenumerations** konto. Välj sedan **klustret**. Obs! List rutan visar bara kluster som tillhör användaren.
        * Välj i stället **Ange** en alternativ knapp manuellt och ange din **slut punkt** (kluster-URL).
    * Ange **klient organisation**.
    * Ange **tjänstens huvud namns-ID**. Ägar-ID: t måste ha tillräcklig behörighet, enligt den behörighets nivå som krävs för det kommando som används.
    * Välj **nyckel för tjänstens huvud namn** och ange **nyckel för tjänstens huvud namn**.
    * Välj din **databas** i list menyn. Du kan också välja **Redigera** kryss rutan och ange ditt databas namn.
    * Välj **test anslutning** för att testa den länkade tjänst anslutningen som du skapade. Om du kan ansluta till installationen visas en grön bock- **anslutning** som är klar.
    * Klicka på **Slutför** om du vill slutföra skapandet av länkade tjänster.

1. När du har konfigurerat en länkad tjänst lägger du till **tabell** namn i **AzureDataExplorerTable** > -**anslutning**. Välj **Förhandsgranska data**för att se till att data visas korrekt.

   Din data uppsättning är nu klar och du kan fortsätta att redigera din pipeline.

### <a name="add-a-query-to-your-lookup-activity"></a>Lägg till en fråga till din söknings aktivitet

1. I **pipeline-4-dokument** > **Inställningar** Lägg till en fråga i text rutan **fråga** , till exempel:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Ändra **timeout för fråga** eller **ingen trunkering** och **endast första raden** , efter behov. I det här flödet behåller vi standardvärdet **för frågan** och avmarkerar kryss rutorna. 

    ![Slutgiltiga inställningar för söknings aktivitet](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Skapa en for-each-aktivitet 

1. Sedan lägger du till en for-each-aktivitet i pipelinen. Den här aktiviteten bearbetar data som returneras från söknings aktiviteten. 
    * I rutan **aktiviteter** under **iteration & villkorliger** **väljer du aktiviteten** för aktiviteter och drar och släpper den på arbets ytan.
    * Rita en linje mellan resultatet av uppslags aktiviteten och indata för den förgrunds aktiviteten på arbets ytan för att ansluta dem.

        ![Förgrunds aktivitet](media/data-factory-command-activity/for-each-activity.png)

1.  Välj aktiviteten förgrunds aktivitet på arbets ytan. På fliken **Inställningar** nedan:
    * Markera kryss rutan **sekventiell** för sekventiell bearbetning av Sök resultaten eller lämna den omarkerad för att skapa parallell bearbetning.
    * Ange **antal batchar**.
    * I **objekt**anger du följande referens till värdet output:  *@activity(' Lookup1 '). output. Value*

       ![ForEach-aktivitetsinställning](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Skapa en Azure Datautforskaren-kommando aktivitet i en förgrunds aktivitet

1. Dubbelklicka på aktiviteten aktiviteter på arbets ytan för att öppna den på en ny arbets yta för att ange aktiviteterna i förväg.
1. I rutan **aktiviteter** under **Azure datautforskaren**väljer du **kommandot Azure datautforskaren kommando** aktivitet och drar och släpper den på arbets ytan.

    ![Azure Datautforskaren kommando aktivitet](media/data-factory-command-activity/adx-command-activity.png)

1.  På fliken **anslutning** väljer du samma länkade tjänst som du skapade tidigare.

    ![fliken anslutning för Azure Data Explorer-kommando för aktivitet](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. På fliken **kommando** anger du följande kommando:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **Kommandot** instruerar Azure datautforskaren att exportera resultatet av en specifik fråga till en blob-lagring i ett komprimerat format. Den körs asynkront (med hjälp av async-modifieraren).
    Frågan behandlar databas kolumnen för varje rad i resultatet av Sök aktiviteten. **Tids gränsen för kommandot** kan lämnas oförändrad.

    ![kommando aktivitet](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > Kommando aktiviteten har följande gränser:
    > * Storleks gräns: 1 MB svars storlek
    > * Tids gräns: 20 minuter (standard), 1 timme (max).
    > * Om det behövs kan du lägga till en fråga till resultatet med hjälp av [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands), för att minska den resulterande storleken/tiden.

1.  Nu är pipelinen klar. Du kan gå tillbaka till den huvudsakliga pipeline-vyn genom att klicka på pipelinens namn.

    ![Pipeline för Azure Datautforskaren-kommandot](media/data-factory-command-activity/adx-command-pipeline.png)

1. Välj **Felsök** innan du publicerar pipelinen. Pipeline-förloppet kan övervakas på fliken **utdata** .

    ![utdata för kommando aktivitet i Azure Data Explorer](media/data-factory-command-activity/command-activity-output.png)

1. Du kan **publicera alla** och sedan **lägga till utlösare** för att köra pipelinen. 

## <a name="control-command-outputs"></a>Styr kommandoutdata

Strukturen på kommando aktivitetens utdata beskrivs nedan. Dessa utdata kan användas av nästa aktivitet i pipelinen.

### <a name="returned-value-of-a-non-async-control-command"></a>Returnerade värdet för ett icke-asynkront kontroll kommando

I ett icke-asynkront kontroll kommando är strukturen för det returnerade värdet detsamma som strukturen för Sök aktivitetens resultat. `count` Fältet visar antalet returnerade poster. Ett fast mat ris `value` fält innehåller en lista med poster. 

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
 
### <a name="returned-value-of-an-async-control-command"></a>Returnerade värdet för ett asynkront kontroll kommando

I ett kommando för asynkron kontroll avsöker aktiviteten Operations-tabellen i bakgrunden, tills den asynkrona åtgärden har slutförts eller är timeout. Det returnerade värdet kommer därför att innehålla resultatet av `.show operations OperationId` för den aktuella **OperationId** -egenskapen. Kontrol lera värdena för **tillstånd** och **status** egenskaper för att kontrol lera att åtgärden har slutförts.

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

* Lär dig hur du [kopierar data till Azure datautforskaren med hjälp av Azure Data Factory](data-factory-load-data.md).
* Lär dig mer om [att använda Azure Data Factory mall för Mass kopiering från databas till Azure datautforskaren](data-factory-template.md).