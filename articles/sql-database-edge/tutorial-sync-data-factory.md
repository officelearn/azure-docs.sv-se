---
title: Synkronisera data från Azure SQL Database Edge med Azure Data Factory | Microsoft Docs
description: Lär dig mer om att synkronisera data mellan Azure SQL Database Edge och Azure Blob Storage
keywords: SQL Database Edge, synkronisera data från SQL Database Edge, SQL Database Edge Data Factory
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501325"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>Självstudie: synkronisera data från SQL Database Edge till Azure Blob Storage med hjälp av Azure Data Factory

I den här självstudien använder du Azure Data Factory för att stegvis synkronisera data från en tabell i en instans av Azure SQL Database Edge till Azure Blob Storage.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte redan har skapat en databas eller tabell i Azure SQL Database Edge-distribution kan du använda någon av följande metoder för att skapa en:

* Använd [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) eller [Azure Data Studio](/sql/azure-data-studio/download/) för att ansluta till SQL Database Edge och köra ett SQL-skript för att skapa databasen och tabellen.
* Skapa en SQL-databas och-tabell med [SQLCMD](/sql/tools/sqlcmd-utility/) genom att ansluta direkt till SQL Database Edge-modulen. Mer information finns i [ansluta till databas motorn med SQLCMD](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Använd SQLPackage. exe för att distribuera en dacpac-fil till SQL Database Edge-behållaren. Detta kan automatiseras genom att ange SQLPackage-filuri som en del av den önskade egenskaperna för moduler, eller genom att direkt använda klient verktyget SqlPackage. exe för att distribuera en DACPAC till SQL Database Edge.

    Information om hur du hämtar sqlpackage finns i [Hämta och installera sqlpackage](/sql/tools/sqlpackage-download/). Följande exempel kommandon för SqlPackage. exe har angetts, men du hittar mer information i SqlPackage-dokumentationen.

    **Skapa DACPAC**:

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **Använd DACPAC**:

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Skapa en SQL-tabell och procedur för att lagra och uppdatera vatten märkes nivåerna

Tabellen vattenstämpel används för att lagra den sista tidsstämpeln som data redan har synkroniserats med Azure Storage. Den lagrade proceduren Transact-SQL (T-SQL) används för att uppdatera vatten märkes tabellen efter varje synkronisering. 

Kör följande kommandon på SQL Database Edge-instansen:

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

## <a name="create-a-data-factory-workflow"></a>Skapa ett arbets flöde för Data Factory

I det här avsnittet skapar du en Azure Data Factory pipeline för att synkronisera data från en tabell inom Azure SQL Database Edge till Azure Blob Storage.

### <a name="create-data-factory-using-the-data-factory-ui"></a>Skapa Data Factory med hjälp av användar gränssnittet för Data Factory

Skapa en Data Factory med hjälp av anvisningarna i den här [självstudien](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Skapa en Data Factory pipeline

1. På sidan **Kom igång** i Data Factory användar gränssnittet väljer du panelen **skapa pipeline** .

    ![Data Factory – skapa pipeline](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. På sidan **Allmänt** i fönstret **Egenskaper** för pipelinen anger du **PeriodicSync** namn.

3. Lägg till **Lookup** -aktiviteten för att hämta det gamla värdet för vatten märket. I **verktygs lådan aktiviteter**expanderar du **allmänt**och drar & ta bort **söknings** aktiviteten till pipelinens designer-yta. Ändra namnet på aktiviteten till *OldWatermark*.

    ![gammal vattenstämpel-sökning](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Växla till fliken **Inställningar** och välj **+ nytt** för **käll data uppsättning**. I det här steget skapar du en data uppsättning som representerar data i watermarktable. Den här tabellen innehåller den gamla vattenstämpeln som användes i den tidigare kopieringen.

5. I fönstret **ny data uppsättning** väljer du **Azure SQL Server**och väljer **Fortsätt**.  

6. I fönstret **Ange egenskaper** för data uppsättningen anger du *WatermarkDataset* som namn.

7. För **länkad tjänst**väljer du **ny**och utför sedan följande steg:

    1. Ange *SQLDBEdgeLinkedService* som **namn**.

    2. Mata in SQL Database Edge Server-information för **Server namn**.

    3. Ange **databas namnet** från List rutan.

    4. Ange ditt **användar namn** och **lösen ord**.

    5. Om du vill testa anslutningen till SQL Database Edge-instansen väljer du **Testa anslutning**.

    6. Välj **Skapa**.

    ![Skapa länkad tjänst](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Välj **OK**

8. På fliken **Inställningar** väljer du **Redigera**.

9. På fliken **anslutning** väljer du *[dbo]. [ watermarktable]* för **tabell**. Om du vill förhandsgranska data i tabellen väljer du **Förhandsgranska data**.

10. Växla till pipeline-redigeraren genom att välja fliken pipeline överst eller genom att välja namnet på pipelinen i trädvyn till vänster. I fönstret Egenskaper för **söknings aktiviteten**bekräftar du att **WatermarkDataset** har valts för fältet för **käll data uppsättning** .

11. I verktygs lådan **aktiviteter** expanderar **du allmänt**, drar och släpper en annan **sökning** -aktivitet till pipelinens design yta och anger namnet **NewWatermark** på fliken **Allmänt** i fönstret Egenskaper. Med den här sökningsaktiviteten kopieras det nya vattenstämpelvärdet från tabellen med källdata till målet.

12. I fönstret Egenskaper för den andra **söknings** aktiviteten växlar du till fliken **Inställningar** och väljer **ny** för att skapa en data uppsättning som pekar på den käll tabell som innehåller det nya värdet för vattenstämpeln.

13. I fönstret **ny data uppsättning** väljer du SQL Database Edge instance och väljer **Fortsätt**.

    1. I fönstret **Ange egenskaper** anger du **SourceDataset** som **namn**. Välj *SQLDBEdgeLinkedService* för länkad tjänst.

    2. Välj ***den tabell som du vill synkronisera*** för tabellen. Du kan också ange en fråga för den här data uppsättningen, som nämns senare i självstudien. Frågan åsidosätter den tabell som du anger i det här steget.

    3. Välj **OK**.

14. Växla till pipeline-redigeraren genom att välja fliken pipeline överst eller genom att välja namnet på pipelinen i trädvyn till vänster. I egenskapsfönstret för **sökningsaktiviteten** bekräftar du att **SourceDataset** är valt för fältet **Source Dataset** (Källdatauppsättning).

15. Välj **fråga** för fältet **Använd fråga** och ange följande fråga efter uppdatering av tabell namn i frågan: du väljer bara det maximala värdet för tidsstämpel från tabellen. Kontrol lera att du också har markerat **enbart första raden**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![Välj fråga](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. I verktygs lådan **aktiviteter** expanderar du **Flytta & Transform**, drar och släpper **kopierings** aktiviteten i aktivitets verktygs lådan och anger namnet till **IncrementalCopy**.

17. Anslut båda **uppslags** aktiviteterna till **kopierings** aktiviteten genom att dra den **gröna knappen** som är kopplad till **Sök** aktiviteterna till **kopierings** aktiviteten. Släpp mus knappen när du ser att kant linje färgen för kopierings aktiviteten ändras till blå.

18. Välj **kopierings** aktiviteten och bekräfta att du ser egenskaperna för aktiviteten i fönstret **Egenskaper** .

19. Växla till fliken **Källa** i fönstret **Egenskaper** och utför följande steg:

    1. Markera **SourceDataset** för fältet för **källdatauppsättning**.

    2. Välj **Fråga** i fältet **Använd fråga**.

    3. Ange SQL-frågan för fältet **fråga** . Exempel fråga nedan

    4. SQL-fr åga:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Växla till fliken **mottagare** och välj **+ ny** för fältet Sink- **datauppsättning** .

21. I den här självstudien är mottagar data lagret av typen **Azure Blob Storage**. Välj **Azure Blob Storage**och välj **Fortsätt** i fönstret **ny data uppsättning** .

22. I fönstret **Välj format** väljer du format typ för dina data och väljer **Fortsätt**.

23. I fönstret **Ange egenskaper** anger du **SinkDataset** som namn. För länkad tjänst väljer du **+ ny**. I det här steget skapar du en anslutning (länkad tjänst) till **Azure Blob Storage**.

24. Utför följande steg i fönstret **ny länkad tjänst (Azure Blob Storage)** :

    1. Ange *AzureStorageLinkedService* som namn.

    2. Välj ditt Azure Storage konto för **lagrings konto namn** med din Azure-prenumeration.

    3. Testa **anslutning** och välj sedan **Slutför**.

25. I fönstret **Ange egenskaper** bekräftar du att *AzureStorageLinkedService* har valts för den **länkade tjänsten**. Välj sedan **skapa** och **OK**.

26. I fliken **mottagare** väljer du **Redigera**.

27. Gå till fliken **anslutning** i *SinkDataset* och utför följande steg:

    1. I fältet **fil Sök väg** anger du *asdedatasync/incrementalcopy*, där **adftutorial** är BLOB-containerns namn och **incrementalcopy** är mappnamnet. Skapa containern om den inte finns, eller ställ in den för namnet på en befintlig. Azure Data Factory skapar automatiskt utdatamappen *incrementalcopy* om den inte finns. Du kan också använda knappen **Bläddra** för **Filsökväg** för att navigera till en mapp i en blobcontainer.

    2. För **fil** delen av fältet **fil Sök väg** väljer du **Lägg till dynamiskt innehåll [ALT + P]** och *anger @CONCAT(' incremental ', pipeline (). RunId, '. txt ')* i det öppnade fönstret. Välj sedan **Slutför**. Filnamnet genereras dynamiskt med uttrycket. Varje pipelinekörning har ett unikt ID. Kopieringsaktiviteten använder körnings-ID för att generera filnamnet.

28. Växla till **pipeline** -redigeraren genom att välja fliken pipeline överst eller genom att välja namnet på pipelinen i trädvyn till vänster.

29. I verktygslådan **Aktiviteter** expanderar du **Allmänt** och drar och släpper aktiviteten **Lagrad procedur** från verktygslådan **Aktiviteter** till pipelinedesignerytan. **Anslut** gröna utdata (lyckades) från aktiviteten **Kopiera** till den **lagrade proceduraktiviteten**.

30. Välj **aktivitet för lagrad procedur** i pipeline-designern, ändra dess namn till *SPtoUpdateWatermarkActivity*.

31. Växla till fliken **SQL-konto** och välj *SQLDBEdgeLinkedService* för **länkad tjänst**.

32. Växla till fliken **Lagrad procedur** och gör följande:

    1. För **lagrad procedur namn**väljer du *[dbo]. [ usp_write_watermark]* .

    2. Om du vill ange värden för parametrarna för den lagrade proceduren väljer du importera parameter och anger följande värden för parametrarna:

    |Namn|Typ|Värde|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {Activity (' NewWaterMark '). output. firstRow. NewWatermarkvalue}|
    |TableName|Sträng|@ {Activity (' OldWaterMark '). output. firstRow. TableName}|

33. Verifiera inställningarna för pipelinen genom att välja **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Stäng fönstret **validerings rapport för pipeline** genom att välja **>>** .

34. Publicera entiteter (länkade tjänster, datauppsättningar och pipeliner) till Azure Data Factory-tjänsten genom att välja knappen **Publicera alla**. Vänta tills du ser ett meddelande om att publiceringen är klar.

## <a name="trigger-a-pipeline-on-schedule"></a>Utlös en pipeline enligt schema

1. I verktygsfältet pipeline väljer du **Lägg till utlösare**och sedan **ny/redigera**, Välj **+ ny**.

2. Namnge utlösaren med *HourlySync*, Välj **typ** som schema och Ställ in **upprepningen** på varannan timme.

3. Välj **OK**.

4. Välj **Publicera alla**.

5. Välj **utlösare nu**.

6. Växla till fliken **Övervaka** till vänster. Du kan se status för den pipelinekörning som utlöstes av den manuella utlösaren. Klicka på **Uppdatera** om du vill uppdatera listan.

## <a name="next-steps"></a>Nästa steg

Azure Data Factory pipelinen i den här självstudien kopierar data från en tabell på SQL Database Edge-instansen till en plats i Azure Blob Storage med Tim frekvens. Om du vill veta mer om hur du använder Data Factory i fler scenarier går du igenom de här [självstudierna](../data-factory/tutorial-copy-data-portal.md).
