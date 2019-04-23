---
title: Skriva avancerade R-funktioner
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Lär dig hur du skriver en R-funktion för avancerade statistiska beräkning i Azure SQL Database med Machine Learning Services (förhandsversion).
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
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014786"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)

Den här artikeln beskrivs hur du bäddar in R matematiska och hjälpfunktioner i en SQL-lagrade proceduren. Avancerade statistikfunktioner är komplicerade att implementera i T-SQL kan göras i R med bara en enda rad kod.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

- Du måste ha en Azure SQL database med Machine Learning Services (med R) aktiverad för att köra exempelkoden i den här övningen. Under den offentliga förhandsversionen introducerar Microsoft dig och aktiverar maskininlärning för din befintliga eller nya databas. Följ stegen i [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

- Kontrollera att du har installerat senast [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Du kan köra R-skript med hjälp av andra databashantering eller Frågeverktyg, men i den här snabbstarten ska du använda SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Skapa en lagrad procedur för att generera slumptal

För enkelhetens skull använder vi R `stats` paket som har installerats och läses in som standard med Azure SQL Database med Machine Learning Services (förhandsversion). Paketet innehåller funktioner för vanliga statistiska uppgifter, bland dem den `rnorm` funktion. Den här funktionen genererar ett angivet antal slumptal med normalfördelning, ett standardavvikelse och sätt.

Exempelvis returnerar följande R-kod 100 siffror på ett medelvärde på 50, får en standardavvikelse på 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

För att anropa den här raden med R från T-SQL, kör `sp_execute_external_script` och Lägg till R-funktion i parametern R-skriptet så här:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Vad händer om du vill göra det enklare att skapa en annan uppsättning slumptal?

Det är lätt när de kombineras med SQL. Du definierar en lagrad procedur som hämtar argumenten från användaren och sedan skicka argumenten till R-skriptet som variabler.

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

- Den första raden definierar var och en av de SQL-indataparametrar som krävs när den lagrade proceduren körs.

- Den rad som början med `@params` definierar alla variabler som används av R-kod och motsvarande SQL-datatyper.

- Rader som följer omedelbart mappa SQL parameternamn till motsvarande R variabelnamn.

Nu när du har omslutits R-funktionen i en lagrad procedur kan du enkelt anropa funktionen och ange olika värden, så här:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Använd R hjälpfunktioner för felsökning

Den `utils` paketet, installerade som standard innehåller en mängd funktioner för verktyg för att undersöka den aktuella R-miljön. Dessa funktioner kan vara användbart om du är att hitta avvikelser i din R-kod som utför i SQL och utanför miljöer. Du kan till exempel använda R `memory.limit()` funktionen att allokera minne för den aktuella R-miljön.

Eftersom den `utils` paketet är installerat men inte inläst som standard måste du använda den `library()` funktionen för att läsa in den först.

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
> Många användare som du använder tidsinställning systemfunktioner i R, till exempel `system.time` och `proc.time`, för att samla in tiden som används av R-processer och analysera problem med prestanda.
