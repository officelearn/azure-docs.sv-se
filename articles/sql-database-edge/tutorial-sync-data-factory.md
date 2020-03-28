---
title: Synkronisera data från Azure SQL Database Edge med hjälp av Azure Data Factory | Microsoft-dokument
description: Lär dig mer om synkronisering av data mellan Azure SQL Database Edge och Azure Blob storage
keywords: sql database edge,synkronisera data från sql database edge, sql database edge data factory
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: e6fd9e6431137708ba93328a8ed1359b93b4ee1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851714"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Självstudiekurs: Synkronisera data från SQL Database Edge till Azure Blob-lagring med hjälp av Azure Data Factory

I den här självstudien använder du Azure Data Factory för att stegvis synkronisera data till Azure Blob-lagring från en tabell i en instans av Azure SQL Database Edge.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte redan har skapat en databas eller tabell i azure SQL Database Edge-distributionen använder du någon av dessa metoder för att skapa en:

* Använd [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) eller Azure Data [Studio](/sql/azure-data-studio/download/) för att ansluta till SQL Database Edge. Kör ett SQL-skript för att skapa databasen och tabellen.
* Skapa en SQL-databas och -tabell med hjälp av [SQLCMD](/sql/tools/sqlcmd-utility/) genom att direkt ansluta till SQL Database Edge-modulen. Mer information finns i [Ansluta till databasmotorn med sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Använd SQLPackage.exe för att distribuera en DAC-paketfil till SQL Database Edge-behållaren. Du kan automatisera den här processen genom att ange SqlPackage-filen URI som en del av modulens önskade egenskapskonfiguration. Du kan också direkt använda sqlpackage.exe-klientverktyget för att distribuera ett DAC-paket till SQL Database Edge.

    Information om hur du hämtar SqlPackage.exe finns i [Hämta och installera sqlpackage](/sql/tools/sqlpackage-download/). Följande är några exempelkommandon för SqlPackage.exe. Mer information finns i dokumentationen till SqlPackage.exe.

    **Skapa ett DAC-paket**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Använda ett DAC-paket**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Skapa en SQL-tabell och sql-procedur för att lagra och uppdatera vattenstämpelnivåerna

En vattenstämpeltabell används för att lagra den senaste tidsstämpeln upp till vilken data redan har synkroniserats med Azure Storage. En Transact-SQL(T-SQL) lagrad procedur används för att uppdatera vattenstämpeltabellen efter varje synkronisering.

Kör dessa kommandon i SQL Database Edge-instansen:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Skapa en pipeline för datafabrik

I det här avsnittet ska du skapa en Azure Data Factory-pipeline för att synkronisera data till Azure Blob-lagring från en tabell i Azure SQL Database Edge.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Skapa en datafabrik med hjälp av datafabrikens användargränssnitt

Skapa en datafabrik genom att följa instruktionerna i den [här självstudien](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Skapa en pipeline för datafabrik

1. På sidan **Låt oss komma igång** i datafabrikens användargränssnitt väljer du Skapa **pipeline**.

    ![Skapa en pipeline för datafabrik](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. På sidan **Allmänt** i fönstret **Egenskaper** för pipelinen anger du **PeriodicSync** för namnet.

3. Lägg till uppslagsaktiviteten för att hämta det gamla vattenstämpelvärdet. Expandera **Allmänt** i fönstret **Aktiviteter** och dra **uppslagsaktiviteten** till pipelinedesignerns yta. Ändra namnet på aktiviteten till **OldWatermark**.

    ![Lägga till den gamla vattenstämpelsökningen](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Växla till fliken **Inställningar** och välj **Ny** för **källdatauppsättning**. Nu ska du skapa en datauppsättning för att representera data i vattenstämpeltabellen. Den här tabellen innehåller den gamla vattenstämpeln som användes i den tidigare kopieringen.

5. I fönstret **Ny datauppsättning** väljer du **Azure SQL Server**och väljer sedan **Fortsätt**.  

6. Ange **Vattenstämpeldatauppsättning**under **Namn**i fönstret **Ange egenskaper** för datauppsättningen.

7. För **länkad tjänst**väljer du **Ny**och slutför sedan följande steg:

    1. Under **Namn**anger du **SQLDBEdgeLinkedService**.

    2. Under **Servernamn**anger du serverinformation för SQL Database Edge.

    3. Välj **databasnamnet** i listan.

    4. Ange ditt **användarnamn** och **lösenord**.

    5. Om du vill testa anslutningen till SQL Database Edge-instansen väljer du **Testa anslutning**.

    6. Välj **Skapa**.

    ![Skapa en länkad tjänst](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Välj **OK**.

8. Välj **Redigera**på fliken **Inställningar** .

9. Välj **Connection** **[dbo].[ vattenstämpelbar]** för **tabell**. Om du vill förhandsgranska data i tabellen väljer du **Förhandsgranska data**.

10. Växla till pipelineredigeraren genom att välja pipelinefliken högst upp eller genom att välja namnet på pipelinen i trädvyn till vänster. I egenskapsfönstret för uppslagsaktiviteten bekräftar du att **WatermarkDataset** är markerat i listan **Källdatauppsättning.**

11. Expandera **Allmänt** i fönstret **Aktiviteter** och dra en annan **uppslagsaktivitet** till pipelinedesignerytan. Ange namnet till **NewWatermark** på fliken **Allmänt** i egenskapsfönstret. Den här uppslagsaktiviteten hämtar det nya vattenstämpelvärdet från tabellen som innehåller källdata så att den kan kopieras till målet.

12. I egenskapsfönstret för den andra uppslagsaktiviteten växlar du till fliken **Inställningar** och väljer **Nytt** om du vill skapa en datauppsättning som pekar på källtabellen som innehåller det nya vattenstämpelvärdet.

13. I fönstret **Ny datauppsättning** väljer du **SQL Database Edge-instans**och väljer sedan **Fortsätt**.

    1. Ange **SourceDataset**under **Namn**i fönstret **Ange egenskaper** för Ange egenskaper . Under **Länkad tjänst**väljer du **SQLDBEdgeLinkedService**.

    2. Markera den tabell som du vill synkronisera under **Tabell.** Du kan också ange en fråga för den här datauppsättningen, enligt beskrivningen senare i den här självstudien. Frågan har företräde framför den tabell som du anger i det här steget.

    3. Välj **OK**.

14. Växla till pipelineredigeraren genom att välja pipelinefliken högst upp eller genom att välja namnet på pipelinen i trädvyn till vänster. I egenskapsfönstret för uppslagsaktiviteten bekräftar du att **SourceDataset** har valts i listan **Källdatauppsättning.**

15. Välj **Fråga** under **Använd fråga**. Uppdatera tabellnamnet i följande fråga och ange frågan. Du väljer bara det maximala `timestamp` värdet för från tabellen. Var noga med att välja **Endast första raden**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![välj fråga](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Expandera Flytta & **Flytta &** och dra **kopieringsaktiviteten** från fönstret Aktiviteter till designerns yta i fönstret **Aktiviteter.** **Activities** Ange namnet på aktiviteten till **IncrementalCopy**.

17. Anslut båda sökningsaktiviteterna till kopieringsaktiviteten genom att dra den gröna knappen som är ansluten till sökningsaktiviteterna till kopieringsaktiviteten. Släpp musknappen när du ser kantfärgen för kopieringsaktiviteten ändras till blå.

18. Välj kopieringsaktiviteten och bekräfta att du ser egenskaperna för aktiviteten i fönstret **Egenskaper**.

19. Växla till fliken **Källa** i fönstret **Egenskaper** och slutför följande steg:

    1. I rutan **Källdatauppsättning** väljer du **SourceDataset**.

    2. Välj **Fråga**under **Använd fråga**.

    3. Ange SQL-frågan i rutan **Fråga.** Här är en exempelfråga:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Välj **Ny** under **Sink-datauppsättning**på fliken **Sink.**

21. I den här självstudien är sink-datalagret ett Azure Blob storage-datalager. Välj **Azure Blob storage**och välj sedan **Fortsätt** i fönstret **Ny datauppsättning.**

22. I fönstret **Välj format** markerar du formatet på dina data och väljer sedan **Fortsätt**.

23. Ange **SinkDataset**under **Namn**i fönstret **Ange egenskaper.** Under **Länkad tjänst**väljer du **Nytt**. Nu skapar du en anslutning (en länkad tjänst) till din Azure Blob-lagring.

24. I fönstret **Ny länkad tjänst (Azure Blob Storage)** gör du så här:

    1. Ange **AzureStorageLinkedService**i rutan **Namn** .

    2. Under **Lagringskontonamn**väljer du Azure-lagringskontot för din Azure-prenumeration.

    3. Testa anslutningen och välj sedan **Slutför**.

25. I fönstret **Ange egenskaper** bekräftar du att **AzureStorageLinkedService** har valts under **Länkad tjänst**. Välj **Skapa** och **OK**.

26. Välj **Redigera**på fliken **Sänd.**

27. Gå till fliken **Anslutning** i SinkDataset och gör så här:

    1. Under **Filsökväg**anger du *asdedatasync/incrementalcopy*, där *asdedatasync* är blob-behållarnamnet och *inkrementell kopia* är mappnamnet. Skapa behållaren om den inte finns eller använd namnet på en befintlig. Azure Data Factory skapar automatiskt *inkrementellkopia för* utdatamappen om den inte finns. Du kan också använda knappen **Bläddra** för **Filsökväg** för att navigera till en mapp i en blobcontainer.

    2. För **fildelen** av **sökvägen Arkiv**väljer du **Lägg till dynamiskt innehåll [Alt+P]** och anger ** @CONCATsedan (Inkrementell-', pipeline(). RunId, '.txt')** i fönstret som öppnas. Välj **Slutför**. Filnamnet genereras dynamiskt av uttrycket. Varje pipelinekörning har ett unikt ID. Kopieringsaktiviteten använder körnings-ID för att generera filnamnet.

28. Växla till pipelineredigeraren genom att välja pipelinefliken högst upp eller genom att välja namnet på pipelinen i trädvyn till vänster.

29. Expandera General **i** fönstret **Aktiviteter** och dra aktiviteten **Lagrad procedur** från fönstret **Aktiviteter** till pipelinedesignerytan. Anslut den gröna (lyckade) utdata för kopieringsaktiviteten till aktiviteten Lagrad procedur.

30. Välj **Lagrad proceduraktivitet** i pipelinedesignern och ändra dess namn till **SPtoUpdateWatermarkActivity**.

31. Växla till fliken **SQL-konto** och välj ***QLDBEdgeLinkedService** under **Länkad tjänst**.

32. Växla till fliken **Lagrad procedur** och slutför följande steg:

    1. Under **Namnet Lagrad procedur**väljer du **[dbo].[ usp_write_watermark]**.

    2. Om du vill ange värden för de lagrade procedurparametrarna väljer du **Importparameter** och anger dessa värden för parametrarna:

    |Namn|Typ|Värde|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|String|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Om du vill validera pipelineinställningarna väljer du **Validera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Om du vill stänga fönstret **>>** **Pipeline-valideringsrapport** väljer du .

34. Publicera entiteterna (länkade tjänster, datauppsättningar och pipelines) till Azure Data Factory-tjänsten genom att välja knappen **Publicera alla.** Vänta tills du ser ett meddelande som bekräftar att publiceringsåtgärden har slutförts.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Utlösa en pipeline baserat på ett schema

1. Välj Lägg till **utlösare**i verktygsfältet pipeline, välj **Ny/Redigera**och välj sedan **Nytt**.

2. Namnge utlösaren **HourlySync**. Under **Typ**väljer du **Schemalägg**. Ställ in **upprepningen** på var 1 timme.

3. Välj **OK**.

4. Välj **Publicera alla**.

5. Välj **Utlösare nu**.

6. Växla till fliken **Övervaka** till vänster. Du kan se status för den pipelinekörning som utlöstes av den manuella utlösaren. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera).

## <a name="next-steps"></a>Nästa steg

Azure Data Factory-pipelinen i den här självstudien kopierar data från en tabell på en SQL Database Edge-instans till en plats i Azure Blob-lagring en gång i timmen. Mer information om hur du använder Data Factory i andra scenarier finns i de här [självstudierna](../data-factory/tutorial-copy-data-portal.md).
