---
title: Skriva avancerade R-funktioner
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Lär dig hur du skriver en R-funktion för avancerad statistisk beräkning i Azure SQL Database att använda Machine Learning Services (för hands version).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7d06b08ce5047c24dd09278a16b9646dcb4e1f7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048219"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Skriv avancerade R-funktioner i Azure SQL Database att använda Machine Learning Services (förhands granskning)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln beskriver hur du bäddar in R-matematiska och verktygs funktioner i en lagrad SQL-procedur. Avancerade statistiska funktioner som är komplicerade att implementera i T-SQL kan bara göras i R med en enda rad kod.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

- Om du vill köra exempel koden i de här övningarna måste du först ha [Azure SQL Database med Machine Learning Services (med R)](machine-learning-services-overview.md) aktiverat.

- Kontrol lera att du har installerat den senaste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Du kan köra R-skript med hjälp av andra databas hanterings-eller specialverktyg, men i den här snabb starten ska du använda SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Skapa en lagrad procedur för att generera slumpmässiga tal

För enkelhetens skull ska vi använda R `stats` -paketet som installeras och läses in som standard med Azure SQL Database med Machine Learning Services (för hands version). Paketet innehåller hundratals funktioner för vanliga statistiska uppgifter, bland annat `rnorm` funktionen. Den här funktionen genererar ett angivet antal slumptal med hjälp av normal distribution, baserat på en standard avvikelse och innebär.

Följande R-kod returnerar till exempel 100 siffror på ett genomsnitt av 50, baserat på en standard avvikelse på 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Om du vill anropa raden R från T-SQL kör `sp_execute_external_script` du och lägger till r-funktionen i r-skript parametern, så här:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Vad gör du om du vill göra det enklare att skapa en annan uppsättning slumpmässiga tal?

Det är enkelt i kombination med SQL. Du definierar en lagrad procedur som hämtar argumenten från användaren och skickar sedan argumenten till R-skriptet som variabler.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- På den första raden definieras var och en av SQL-indataparametrarna som krävs när den lagrade proceduren körs.

- Raden som börjar med `@params` definierar alla variabler som används av R-koden och motsvarande SQL-datatyper.

- De rader som omedelbart följer mappar SQL-parameter namnen till motsvarande R-variabel namn.

Nu när du har lagt till R-funktionen i en lagrad procedur kan du enkelt anropa funktionen och skicka in olika värden, så här:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Använd R-verktyg för fel sökning

`utils`Paketet som installeras som standard innehåller en mängd olika verktyg funktioner för att undersöka den aktuella R-miljön. Dessa funktioner kan vara användbara om du hittar skillnader i hur din R-kod presterar i SQL och i externa miljöer. Du kan till exempel använda R- `memory.limit()` funktionen för att hämta minne för aktuell R-miljö.

Eftersom `utils` paketet är installerat men inte läses in som standard måste du använda `library()` funktionen för att läsa in den först.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Många användare vill använda systemets tids inställnings funktioner i R, till exempel `system.time` och `proc.time` , för att avbilda tiden som används av r-processer och analysera prestanda problem.
