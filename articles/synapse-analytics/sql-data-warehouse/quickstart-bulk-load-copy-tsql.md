---
title: 'Snabbstart: Massinläsningsdata med ett enda T-SQL-uttryck'
description: Massinläsningsdata med COPY-satsen
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d39b3085a802ca0ff745ab1f63f4a8fba966ea48
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115011"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Snabbstart: Massinläsningsdata med COPY-satsen

I den här snabbstarten ska du massinläsning av data i DIN SQL-pool med hjälp av den enkla och flexibla [COPY-uttrycket](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) för datainmatning med högt dataflöde. COPY-satsen är det rekommenderade inläsningsverktyget eftersom det gör att du kan läsa in data sömlöst och flexibelt genom att tillhandahålla funktioner för:

- Tillåt att användare med lägre privilegierade kan läsas in utan att behöva strikta behörigheter för datalagret
- Utnyttja endast ett enda T-SQL-uttryck utan att behöva skapa ytterligare databasobjekt
- Utnyttja en finare behörighetsmodell utan att exponera lagringskontonycklar med Hjälp av SAS (Share Access Signatures)
- Ange ett annat lagringskonto för FELFILE-platsen (REJECTED_ROW_LOCATION)
- Anpassa standardvärden för varje målkolumn och ange källdatafält som ska läsas in i specifika målkolumner
- Ange en anpassad radavslutning för CSV-filer
- Escape-sträng, fält- och radavgränsare för CSV-filer
- Utnyttja SQL Server-datumformat för CSV-filer
- Ange jokertecken och flera filer i sökvägen till lagringsplatsen

## <a name="prerequisites"></a>Krav

Den här snabbstarten förutsätter att du redan har en SQL-pool. Om en SQL-pool inte har skapats använder du snabbstarten [Skapa och Anslut -](create-data-warehouse-portal.md) portalen.

## <a name="create-the-target-table"></a>Skapa måltabellen

I det här exemplet läser vi in data från New York-taxidatauppsättningen. Vi laddar ett bord som heter Trip som representerar taxiresor som görs inom ett enda år. Kör följande för att skapa tabellen:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>Kör COPY-satsen

Kör följande COPY-sats som läser in data från Azure blob storage-kontot i tabellen Resa.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Övervaka belastningen

Kontrollera om inläsningen gör framsteg genom att regelbundet köra följande fråga:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Nästa steg

- Bästa praxis för inläsning av data finns i [Metodtips för inläsning av data](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data).
- Information om hur du hanterar resurser för dina datainläsningar finns i [Arbetsbelastningsisolering](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
