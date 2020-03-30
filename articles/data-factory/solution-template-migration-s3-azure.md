---
title: Migrera data från Amazon S3 till Azure Data Lake Storage Gen2
description: Lär dig hur du använder en lösningsmall för att migrera data från Amazon S3 med hjälp av en extern kontrolltabell för att lagra en partitionslista på AWS S3 med Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: e918fe01426202746f0225d25304b9c1b26cb74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927331"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrera data från Amazon S3 till Azure Data Lake Storage Gen2

Använd mallarna för att migrera petabyte data som består av hundratals miljoner filer från Amazon S3 till Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Om du vill kopiera liten datavolym från AWS S3 till Azure (till exempel mindre än 10 TB) är det effektivare och enklare att använda [azure data factory copy data-verktyget](copy-data-tool.md). Mallen som beskrivs i den här artikeln är mer än vad du behöver.

## <a name="about-the-solution-templates"></a>Om lösningsmallarna

Datapartition rekommenderas särskilt när du migrerar mer än 10 TB data. För att partitionera data, utnyttja "prefix" inställningen för att filtrera mappar och filer på Amazon S3 efter namn, och sedan varje ADF kopieringsjobb kan kopiera en partition i taget. Du kan köra flera ADF-kopieringsjobb samtidigt för bättre dataflöde.

Datamigrering kräver normalt enstaka historisk datamigrering plus att regelbundet synkronisera ändringarna från AWS S3 till Azure. Det finns två mallar nedan, där en mall täcker enstaka historisk datamigrering och en annan mall täcker synkronisering av ändringarna från AWS S3 till Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>För mallen att migrera historiska data från Amazon S3 till Azure Data Lake Storage Gen2

Den här mallen (*mallnamn: migrera historiska data från AWS S3 till Azure Data Lake Storage Gen2*) förutsätter att du har skrivit en partitionslista i en extern kontrolltabell i Azure SQL Database. Så det kommer att använda en *uppslagsaktivitet* för att hämta partitionslistan från den externa kontrolltabellen, iterera över varje partition och göra varje ADF-kopieringsjobb kopiera en partition i taget. När ett kopieringsjobb har slutförts används aktiviteten *Lagrad procedur* för att uppdatera statusen för att kopiera varje partition i kontrolltabellen.

Mallen innehåller fem aktiviteter:
- **Sökningen** hämtar de partitioner som inte har kopierats till Azure Data Lake Storage Gen2 från en extern kontrolltabell. Tabellnamnet är *s3_partition_control_table* och frågan som ska läsas in data från tabellen är *"SELECT PartitionPrefix FROM s3_partition_control_table WHERE SuccessOrFailure = 0"*.
- **ForEach** hämtar partitionslistan från *uppslagsaktiviteten* och itererar varje partition till *TriggerCopy-aktiviteten.* Du kan ange att *batchCount* ska köra flera ADF-kopieringsjobb samtidigt. Vi har satt 2 i denna mall.
- **ExecutePipeline** kör *CopyFolderPartitionFromS3-pipeline.* Anledningen till att vi skapar en annan pipeline för att göra varje kopia jobb kopiera en partition är att det kommer att göra dig lätt att köra den misslyckade kopian jobb att ladda om den specifika partitionen igen från AWS S3. Alla andra kopieringsjobb som läser in andra partitioner påverkas inte.
- **Kopiera** kopior av varje partition från AWS S3 till Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** uppdaterar statusen för att kopiera varje partition i kontrolltabellen.

Mallen innehåller två parametrar:
- **AWS_S3_bucketName** är ditt bucketnamn på AWS S3 där du vill migrera data från. Om du vill migrera data från flera buckets på AWS S3 kan du lägga till ytterligare en kolumn i den externa kontrolltabellen för att lagra bucket-namnet för varje partition och även uppdatera pipelinen för att hämta data från den kolumnen i enlighet med detta.
- **Azure_Storage_fileSystem** är ditt filSystemnamn på Azure Data Lake Storage Gen2 där du vill migrera data till.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>För mallen att kopiera ändrade filer endast från Amazon S3 till Azure Data Lake Storage Gen2

Den här*mallen (mallnamn: kopiera deltadata från AWS S3 till Azure Data Lake Storage Gen2*) använder LastModifiedTime för varje fil för att kopiera de nya eller uppdaterade filerna endast från AWS S3 till Azure. Tänk på om dina filer eller mappar redan har tidspartitionerats med timeslice-information som en del av filen eller mappnamnet på AWS S3 (till exempel /yyyy/mm/dd/file.csv), kan du gå till den här [självstudien](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) för att få den mer högpresterande metoden för inkrementell inläsning av nya filer. Den här mallen förutsätter att du har skrivit en partitionslista i en extern kontrolltabell i Azure SQL Database. Så det kommer att använda en *uppslagsaktivitet* för att hämta partitionslistan från den externa kontrolltabellen, iterera över varje partition och göra varje ADF-kopieringsjobb kopiera en partition i taget. När varje kopieringsjobb börjar kopiera filerna från AWS S3, förlitar den sig på Egenskapen LastModifiedTime för att identifiera och kopiera de nya eller uppdaterade filerna. När ett kopieringsjobb har slutförts används aktiviteten *Lagrad procedur* för att uppdatera statusen för att kopiera varje partition i kontrolltabellen.

Mallen innehåller sju aktiviteter:
- **Sökningen** hämtar partitionerna från en extern kontrolltabell. Tabellnamnet är *s3_partition_delta_control_table* och frågan som ska läsas in data från tabellen är *"välj distinkt PartitionPrefix från s3_partition_delta_control_table".*
- **ForEach** hämtar partitionslistan från *uppslagsaktiviteten* och itererar varje partition till *TriggerDeltaCopy-aktiviteten.* Du kan ange att *batchCount* ska köra flera ADF-kopieringsjobb samtidigt. Vi har satt 2 i denna mall.
- **ExecutePipeline** kör *DeltaCopyFolderPartitionFromS3-pipeline.* Anledningen till att vi skapar en annan pipeline för att göra varje kopia jobb kopiera en partition är att det kommer att göra dig lätt att köra den misslyckade kopian jobb att ladda om den specifika partitionen igen från AWS S3. Alla andra kopieringsjobb som läser in andra partitioner påverkas inte.
- **Sökningen** hämtar den senaste kopieringsjobbtiden från den externa kontrolltabellen så att de nya eller uppdaterade filerna kan identifieras via LastModifiedTime. Tabellnamnet är *s3_partition_delta_control_table* och frågan som ska läsas in data från tabellen är *"select max(JobRunTime) as LastModifiedTime from s3_partition_delta_control_table where PartitionPrefix = '@{pipeline().parameters.prefixStr}' och SuccessOrFailure = 1"*.
- **Kopiera** kopior av nya eller ändrade filer endast för varje partition från AWS S3 till Azure Data Lake Storage Gen2. Egenskapen *för modifiedDatetimeStart* är inställd på den senaste kopieringsjobbkörningstiden. Egenskapen *för modifiedDatetimeEnd* är inställd på den aktuella kopieringsjobbkörningstiden. Tänk på att tiden tillämpas på UTC-tidszonen.
- **SqlServerStoredProcedure** uppdaterar statusen för att kopiera varje partition och kopiera körningstid i kontrolltabellen när den lyckas. Kolumnen SuccessOrFailure är inställd på 1.
- **SqlServerStoredProcedure** uppdaterar statusen för att kopiera varje partition och kopiera körningstid i kontrolltabellen när den misslyckas. Kolumnen SuccessOrFailure är inställd på 0.

Mallen innehåller två parametrar:
- **AWS_S3_bucketName** är ditt bucketnamn på AWS S3 där du vill migrera data från. Om du vill migrera data från flera buckets på AWS S3 kan du lägga till ytterligare en kolumn i den externa kontrolltabellen för att lagra bucket-namnet för varje partition och även uppdatera pipelinen för att hämta data från den kolumnen i enlighet med detta.
- **Azure_Storage_fileSystem** är ditt filSystemnamn på Azure Data Lake Storage Gen2 där du vill migrera data till.

## <a name="how-to-use-these-two-solution-templates"></a>Så här använder du dessa två lösningsmallar

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>För mallen att migrera historiska data från Amazon S3 till Azure Data Lake Storage Gen2 

1. Skapa en kontrolltabell i Azure SQL Database för att lagra partitionslistan för AWS S3. 

    > [!NOTE]
    > Tabellnamnet är s3_partition_control_table.
    > Schemat för kontrolltabellen är PartitionPrefix och SuccessOrFailure, där PartitionPrefix är prefixinställningen i S3 för att filtrera mappar och filer i Amazon S3 med namn, och SuccessOrFailure är status för att kopiera varje partition: 0 betyder att den här partitionen har inte kopierats till Azure och 1 innebär att den här partitionen har kopierats till Azure.
    > Det finns 5 partitioner definierade i kontrolltabellen och standardstatusen för att kopiera varje partition är 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Skapa en lagrad procedur på samma Azure SQL-databas för kontrolltabell. 

    > [!NOTE]
    > Namnet på den lagrade proceduren är sp_update_partition_success. Den anropas av SqlServerStoredProcedure-aktiviteten i ADF-pipelinen.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Gå till mallen **Migrera historiska data från AWS S3 till Azure Data Lake Storage Gen2.** Mata in anslutningarna till din externa kontrolltabell, AWS S3 som datakälllagre och Azure Data Lake Storage Gen2 som mållag. Tänk på att den externa kontrolltabellen och den lagrade proceduren är referens till samma anslutning.

    ![Skapa en ny anslutning](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Du ser de 2 pipelines och 3 datauppsättningar skapades, som visas i följande exempel:

    ![Granska pipelinen](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Välj **Felsökning,** ange **parametrar**och välj sedan **Slutför**.

    ![Klicka på **Felsökning**](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Du ser resultat som liknar följande exempel:

    ![Granska resultatet](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>För mallen att kopiera ändrade filer endast från Amazon S3 till Azure Data Lake Storage Gen2

1. Skapa en kontrolltabell i Azure SQL Database för att lagra partitionslistan för AWS S3. 

    > [!NOTE]
    > Tabellnamnet är s3_partition_delta_control_table.
    > Schemat för kontrolltabellen är PartitionPrefix, JobRunTime och SuccessOrFailure, där PartitionPrefix är prefixinställningen i S3 för att filtrera mappar och filer i Amazon S3 efter namn, JobRunTime är datetime-värdet när kopieringsjobb körs och SuccessOrFailure är status för att kopiera varje partition: 0 innebär att den här partitionen inte har kopierats till Azure och 1 innebär att den här partitionen har kopierats till Azure.
    > Det finns 5 partitioner definierade i kontrolltabellen. Standardvärdet för JobRunTime kan vara den tid då enstaka historisk datamigrering startar. ADF-kopieringsaktivitet kopierar filerna på AWS S3 som senast har ändrats efter den tiden. Standardstatusen för kopiering av varje partition är 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Skapa en lagrad procedur på samma Azure SQL-databas för kontrolltabell. 

    > [!NOTE]
    > Namnet på den lagrade proceduren är sp_insert_partition_JobRunTime_success. Den anropas av SqlServerStoredProcedure-aktiviteten i ADF-pipelinen.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Gå till **copy delta-data från AWS S3 till Azure Data Lake Storage** Gen2-mallen. Mata in anslutningarna till din externa kontrolltabell, AWS S3 som datakälllagre och Azure Data Lake Storage Gen2 som mållag. Tänk på att den externa kontrolltabellen och den lagrade proceduren är referens till samma anslutning.

    ![Skapa en ny anslutning](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Du ser de 2 pipelines och 3 datauppsättningar skapades, som visas i följande exempel:

    ![Granska pipelinen](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Välj **Felsökning,** ange **parametrar**och välj sedan **Slutför**.

    ![Klicka på **Felsökning**](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Du ser resultat som liknar följande exempel:

    ![Granska resultatet](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Du kan också kontrollera resultaten från kontrolltabellen med en fråga *"välj * från s3_partition_delta_control_table"* visas utdata som liknar följande exempel:

    ![Granska resultatet](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Nästa steg

- [Kopiera filer från flera containrar](solution-template-copy-files-multiple-containers.md)
- [Flytta filer](solution-template-move-files.md)