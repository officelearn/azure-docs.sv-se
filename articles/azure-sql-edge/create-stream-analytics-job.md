---
title: Skapa ett T-SQL-strömmande jobb i Azure SQL Edge (för hands version)
description: Lär dig mer om att skapa Stream Analytics jobb i Azure SQL Edge (för hands version)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7db7f9548a3daa86a53dd37fbe088661e8b7b17e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685170"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Skapa Stream Analytics jobb i Azure SQL Edge (för hands version) 

Den här artikeln beskriver hur du skapar ett T-SQL streaming-jobb i Azure SQL Edge (för hands version). Om du vill skapa ett strömmande jobb i SQL Edge behövs följande steg

1. Skapa indata och utdata för det externa Stream-objektet
2. Definiera frågan för strömnings jobb som en del av direkt uppspelnings jobbet.

> [!NOTE]
> Aktivera funktionen T-SQL-direktuppspelning i Azure SQL Edge genom att aktivera TF 11515 som ett start alternativ eller använda [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) -kommandot. Mer information om hur du aktiverar spårnings flaggor med hjälp av MSSQL. conf-filen finns i [Konfigurera med hjälp av MSSQL. conf-filen](configure.md#configure-using-mssqlconf-file). Detta krav kommer att tas bort i framtida uppdateringar av Azure SQL Edge (för hands version).

## <a name="configure-an-external-stream-input-and-output-object"></a>Konfigurera ett indata-och utgående objekt för extern ström

T-SQL streaming använder funktionen för externa data källor i SQL Server för att definiera de data källor som är associerade med de externa data strömmarnas indata och utdata från strömnings jobbet. Följande T-SQL-kommandon krävs för att skapa ett externt data ström-eller utdata-objekt.

[Skapa externt filformat (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[Skapa extern datakälla (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[Skapa extern ström (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

Om SQL Edge (eller SQL Server, Azure SQL) används som en utdataström, krävs T-SQL [-kommandot CREATE Database (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) T-SQL för att definiera autentiseringsuppgifterna för åtkomst till SQL Database.

### <a name="supported-input-and-output-stream-data-sources"></a>Data källor för indata och utdataström som stöds

Azure SQL Edge stöder för närvarande endast följande data källor som indata och utdata.

| Typ av datakälla | Indata | Resultat | Description |
|------------------|-------|--------|------------------|
| Azure IoT Edge hubb | Y | Y | Data källa för att läsa/skriva strömmande data till en Azure IoT Edge hubb. Mer information om Azure IoT Edge Hub finns [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| SQL Database | N | Y | Anslutning till data källa för att skriva strömmande data till SQL Database. SQL Database kan vara en lokal SQL Edge-databas eller en fjärran sluten SQL Server eller Azure SQL Database|
| Azure Blob Storage | N | Y | Data källa för att skriva data till en BLOB på ett Azure Storage-konto. |
| Kafka | Y | N | Data källa för att läsa strömmande data från ett Kafka-ämne. Det här kortet är för närvarande endast tillgängligt för Intel/AMD-versionen av Azure SQL Edge och är inte tillgänglig för ARM64-versionen av SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Exempel: skapa ett externt Stream-indata/utdata-objekt för Azure IoT Edge hubb

Exemplet nedan skapar ett externt Stream-objekt för Edge Hub. Om du vill skapa en extern data källa för data ström/utdata för Azure IoT Edge hubb måste du först skapa ett externt fil format för SQL för att förstå layouten för data som läses/skrivs.

1. Skapa ett externt fil format med format typen JSON.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Skapa en extern data källa för IoT Edge hubben. T-SQL-skriptet nedan skapar en anslutning till en data källa till en Edge-hubb som körs på samma Docker-värd som SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Skapa det externa Stream-objektet för IoT Edge Hub. T-SQL-skript nedan skapar ett Stream-objekt för Edge Hub. Om det rör sig om ett Stream-objekt i Edge, är parametern LOCATION namnet på det kant nav ämne/-kanal som läses eller skrivs till.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>Exempel: skapa ett externt Stream-objekt SQL Database

Exemplet nedan skapar ett externt Stream-objekt till den lokala SQL Edge-databasen. 

1. Skapa en huvud nyckel för databasen. Detta krävs för att kryptera hemligheten för autentiseringsuppgiften.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Skapa en databas med begränsade autentiseringsuppgifter för att komma åt SQL Server källan. I följande exempel skapas en autentiseringsuppgift för den externa data källan med identitet = ' username ' och SECRET = ' Password '.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Skapa en extern data källa med skapa extern DATA källa. Följande exempel:

    * Skapar en extern data källa med namnet LocalSQLOutput
    * Identifierar den externa data källan (LOCATION = ' <vendor> :// <server> [: <port> ] '). I exemplet pekar den på en lokal instans av SQL Edge.
    * Slutligen använder exemplet de autentiseringsuppgifter som skapades tidigare.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Skapa det externa Stream-objektet. Exemplet nedan skapar ett externt Stream-objekt som pekar på en tabell *dbo. TemperatureMeasurements* i databasen *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Skapa direkt uppspelnings jobbet och strömmande frågor

Använd den **sys. sp_create_streaming_job** system-lagrade proceduren för att definiera direkt uppspelnings frågorna och skapa direkt uppspelnings jobbet. Den lagrade proceduren **sp_create_streaming_job** tar två parametrar

- job_name-namnet på direkt uppspelnings jobbet. Strömmande jobb namn är unika i instansen.
- Statement- [Stream Analytics frågespråk för frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?) som baseras på frågor.

Exemplet nedan skapar ett enkelt direkt uppspelnings jobb med en direkt uppspelnings fråga. Den här frågan läser indata från Edge Hub och skriver till *dbo. TemperatureMeasurements* i databasen.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Exemplet nedan skapar ett mer komplext strömnings jobb med flera olika frågor, inklusive en fråga som använder den inbyggda AnomalyDetection_ChangePoint funktionen för att identifiera avvikelser i temperatur data.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Starta, stoppa, släppa och övervaka strömmande jobb

Starta ett strömmande jobb i SQL Edge genom att köra den lagrade proceduren **sys. sp_start_streaming_job** . Den lagrade proceduren kräver att samma strömmande jobb startas, som indata.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Om du vill stoppa ett strömmande jobb i SQL Edge kör du den lagrade proceduren **sys. sp_stop_streaming_job** . Den lagrade proceduren kräver att samma strömnings jobb stoppas, som indata.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Om du vill släppa (eller ta bort) ett strömmande jobb i SQL Edge, kör du den lagrade proceduren **sys. sp_drop_streaming_job** . Den lagrade proceduren kräver att samma strömmande jobb släpps som indata.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Kör den lagrade proceduren **sys. sp_get_streaming_job** om du vill hämta aktuell status för ett strömmande jobb i SQL Edge. Den lagrade proceduren kräver att samma strömnings jobb är samma som för att ta bort, som indata och matar ut namnet och den aktuella statusen för strömnings jobbet.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

Direkt uppspelnings jobbet kan ha någon av följande statusar

| Status | Beskrivning |
|--------| ------------|
| Skapad | Direkt uppspelnings jobbet skapades men har ännu inte startats |
| Startar | Streaming-jobbet är i start fasen |
| Inaktiv | Direkt uppspelnings jobbet körs, men det finns inga indata att bearbeta |
| Bearbetar | Direkt uppspelnings jobbet körs och bearbetar indata. Det här tillståndet anger ett felfritt tillstånd för strömnings jobbet |
| Degraderad | Direkt uppspelnings jobbet körs, men det fanns några icke allvarliga fel vid indata/utdata-serialisering/-serialisering under bearbetning av indata. Inmatnings jobbet kommer att fortsätta köras, men kommer att släppa indata som stöter på fel |
| Stoppad | Direkt uppspelnings jobbet har stoppats |
| Misslyckades | Streaming-jobbet misslyckades. Detta är vanligt vis ett tecken på ett allvarligt fel under bearbetningen |

## <a name="next-steps"></a>Nästa steg

- [Visa metadata som är kopplade till strömmande jobb i Azure SQL Edge (för hands version)](streaming-catalog-views.md) 
- [Skapa en extern ström](create-external-stream-transact-sql.md)
