---
title: Få åtkomst till filer på lagrings platsen med SQL på begäran (för hands version) i Synapse SQL
description: Beskriver hur du frågar lagrings filer med hjälp av SQL on-demand-resurser (för hands version) i Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4b6331977cc2237801b84647e4edeb5d789cb9e8
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482470"
---
# <a name="accessing-external-storage-in-synapse-sql"></a>Åtkomst till extern lagring i Synapse SQL

I det här dokumentet beskrivs hur kan användare läsa data från filerna som lagras på Azure Storage i Synapse SQL (på begäran och i pooler). Användare har följande alternativ för åtkomst till lagring:

- [OpenRowSet](develop-openrowset.md) -funktionen som aktiverar ad hoc-frågor över filerna i Azure Storage.
- [Extern tabell](develop-tables-external-tables.md) som är en fördefinierad data struktur som skapats ovanpå en uppsättning externa filer.

Användaren kan använda [olika autentiseringsmetoder](develop-storage-files-storage-access-control.md) , till exempel Azure AD passthrough-autentisering (standard för Azure AD-huvudobjekt) och SAS-autentisering (standard för SQL-huvudobjekt).

## <a name="openrowset"></a>OPENROWSET

Funktionen [OpenRowSet](develop-openrowset.md) gör att användaren kan läsa filerna från Azure Storage.

### <a name="query-files-using-openrowset"></a>Köra frågefiler med OpenRowSet

OpenRowSet gör det möjligt för användare att fråga externa filer på Azure Storage om de har åtkomst till lagringen. Användare som är ansluten till Synapse SQL-slutpunkt på begäran ska använda följande fråga för att läsa innehållet i filerna i Azure Storage:

```sql
SELECT * FROM
 OPENROWSET(BULK 'http://storage...com/container/file/path/*.csv', format= 'parquet') as rows
```

Användaren kan komma åt lagringen med följande åtkomst regler:

- Azure AD User-OpenRowSet kommer att använda Azure AD-identitet för anroparen för att få åtkomst till Azure Storage eller åtkomst till lagring med anonym åtkomst.
- SQL User – OpenRowSet kommer att få åtkomst till lagring med anonym åtkomst.

SQL-huvudobjekt kan också använda OpenRowSet för att direkt fråga filer som skyddas med SAS-token eller hanterad identitet på arbets ytan. Om en SQL-användare kör den här funktionen måste en privilegie rad användare med ALTER all CREDENTIAL-behörighet skapa en server begränsad autentiseringsuppgift som matchar URL i funktionen (med hjälp av lagrings namn och behållare) och beviljar behörighet för den här autentiseringsuppgiften till anroparen i OpenRowSet-funktionen:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [http://storage.dfs.com/container]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[http://storage.dfs.com/container] TO sqluser
```

Om det inte finns några AUTENTISERINGSUPPGIFTER på server nivå som matchar URL: en eller SQL-användare saknar referens behörighet för den här autentiseringsuppgiften returneras felet. SQL-huvudobjekt kan inte personifiera med en viss Azure AD-identitet.

> [!NOTE]
> Den här versionen av OpenRowSet är utformad för snabb och enkel data utforskning med standardautentisering. Om du vill utnyttja personifiering eller hanterad identitet använder du OpenRowSet med DATASOURCE enligt beskrivningen i nästa avsnitt.

### <a name="querying-data-sources-using-openrowset"></a>Fråga data källor med OpenRowSet

OpenRowSet gör det möjligt för användaren att fråga filerna som placerats på en extern data Källa:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.csv',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'csv') as rows
```

Privilegierade användare med kontroll databas behörighet måste skapa databas OMFATTNINGs referenser som ska användas för åtkomst till lagring och extern DATA källa som anger URL: en till data källan och autentiseringsuppgiften som ska användas:

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

DATABASens begränsade AUTENTISERINGSUPPGIFTER anger hur du kommer åt filer på den refererade data källan (för närvarande SAS och hanterad identitet).

Anroparen måste ha någon av följande behörigheter för att köra OpenRowSet-funktionen:

- En av behörigheterna för att köra OpenRowSet:
  - ADMINISTRERA Mass åtgärd möjliggör inloggning för att köra OpenRowSet-funktionen.
  - ADMINISTRERA DATABASens Mass åtgärd gör det möjligt för databas omfattnings användare att köra OpenRowSet-funktionen.
- REFERERAR till DATABASens begränsade AUTENTISERINGSUPPGIFTER till autentiseringsuppgiften som refereras i den externa DATA källan

#### <a name="accessing-anonymous-data-sources"></a>Åtkomst till anonyma data källor

Användaren kan skapa en extern DATA källa utan AUTENTISERINGSUPPGIFTER som ska hänvisa till offentlig åtkomst lagring eller använda Azure AD passthrough-autentisering:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3') ;
```

## <a name="external-table"></a>EXTERN TABELL

Användare med behörighet att läsa tabell har åtkomst till externa filer med hjälp av en extern tabell som skapats ovanpå Azure Storage mappar och filer.

Användare som har [behörighet att skapa en extern tabell](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions) (till exempel CREATE TABLE och ändra AUTENTISERINGSUPPGIFTER eller referenser till databasens begränsade autentiseringsuppgifter) kan använda följande skript för att skapa en tabell ovanpå Azure Storage data Källa:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

Användaren med behörigheten kontrol lera databas skulle behöva skapa en databas med begränsade AUTENTISERINGSUPPGIFTER som ska användas för åtkomst till lagring och extern DATA källa som anger URL: en till data källan och autentiseringsuppgiften som ska användas:

```sql
CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://samples.blob.core.windows.net/products' ,
 CREDENTIAL = cred
 ) ;
```

DATABASens begränsade AUTENTISERINGSUPPGIFTER anger hur du kommer åt filer på den refererade data källan.

### <a name="reading-external-files-with-external-table"></a>Läser externa filer med extern tabell

Med den externa tabellen kan du läsa data från filer som refereras via data källa med standard SQL SELECT-instruktionen:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

Anroparen måste ha följande behörigheter för att kunna läsa data:
- Välj behörighet för extern tabell
- REFERERAR behörigheten för DATABASens begränsade AUTENTISERINGSUPPGIFTER om DATA källan har AUTENTISERINGSUPPGIFTER

## <a name="permissions"></a>Behörigheter

I följande tabell visas de behörigheter som krävs för de åtgärder som anges ovan.

| Söka i data | Nödvändiga behörigheter|
| --- | --- |
| OpenRowSet (BULK) utan DataSource | ADMINISTRERA Mass administratör SQL-inloggning måste ha referenser till AUTENTISERINGSUPPGIFTER:: \<URL> för SAS-skyddad lagring |
| OpenRowSet (BULK) med DataSource utan autentiseringsuppgifter | ADMINISTRERA MASS ADMINISTRATÖR |
| OpenRowSet (BULK) med data källa med autentiseringsuppgifter | ADMINISTRERA MASS ADMINISTRATÖR REFERERAR TILL DATABASENS BEGRÄNSADE AUTENTISERINGSUPPGIFTER |
| SKAPA EXTERN DATA KÄLLA | ÄNDRA EN EXTERN DATA KÄLLA REFERERAR TILL DATABASENS BEGRÄNSADE AUTENTISERINGSUPPGIFTER |
| SKAPA EXTERN TABELL | CREATE TABLE, ÄNDRA SCHEMAN, ÄNDRA ALLA EXTERNA FIL FORMAT, ÄNDRA ALLA EXTERNA DATA KÄLLOR |
| VÄLJ FRÅN EXTERN TABELL | VÄLJ TABELL |
| CETAS | Om du vill skapa tabell-CREATE TABLE ändrar du alla DATA källor och ändrar alla externa fil FORMAT. Läsa data: ADMIN Mass åtgärder + referenser till AUTENTISERINGSUPPGIFT eller Välj Tabell per varje tabell/vy/funktion i fråga + R/W-behörighet för lagring |

## <a name="next-steps"></a>Nästa steg

Nu är du redo att fortsätta med följande artiklar:

- [Fråga efter data på lagring](query-data-storage.md)

- [Efterfråga CSV-fil](query-single-csv-file.md)

- [Efterfråga mappar och flera filer](query-folders-multiple-csv-files.md)

- [Fråga efter vissa filer](query-specific-files.md)

- [Efterfråga Parquet-filer](query-parquet-files.md)

- [Fråga kapslade typer](query-parquet-nested-types.md)

- [Efterfråga JSON-filer](query-json-files.md)

- [Skapa och använda vyer](create-use-views.md)
