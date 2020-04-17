---
title: Skriva avancerade R-funktioner
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Lär dig hur du skriver en R-funktion för avancerad statistisk beräkning i Azure SQL Database med Machine Learning Services (förhandsversion).
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
ms.openlocfilehash: ba78267b1c6dc8f0e1bd25bb8ecdb1d8d344d03e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453122"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)

I den här artikeln beskrivs hur du bäddar in R-matematiska funktioner och nyttofunktioner i en SQL-lagrad procedur. Avancerade statistiska funktioner som är komplicerade att implementera i T-SQL kan göras i R med endast en enda kodrad.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Krav

- Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

- Om du vill köra exempelkoden i dessa övningar måste du först ha [Azure SQL Database med Machine Learning Services (med R)](sql-database-machine-learning-services-overview.md) aktiverat.

- Kontrollera att du har installerat den senaste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Du kan köra R-skript med andra databashanterings- eller frågeverktyg, men i den här snabbstarten använder du SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Skapa en lagrad procedur för att generera slumptal

För enkelhetens skull, `stats` låt oss använda R-paketet som installeras och laddas som standard med Azure SQL Database med Machine Learning Services (preview). Paketet innehåller hundratals funktioner för gemensamma statistiska uppgifter, bland annat `rnorm` funktionen. Denna funktion genererar ett angivet antal slumptal med hjälp av normalfördelningen, med tanke på en standardavvikelse och medel.

Följande R-kod returnerar till exempel 100 tal på ett medelvärde av 50, med en standardavvikelse på 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Om du vill anropa den här `sp_execute_external_script` raden med R från T-SQL kör och lägger du till R-funktionen i R-skriptparametern, så här:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Vad händer om du vill göra det enklare att generera en annan uppsättning slumptal?

Det är enkelt i kombination med SQL. Du definierar en lagrad procedur som hämtar argumenten från användaren och skickar sedan dessa argument till R-skriptet som variabler.

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

- Raden som `@params` börjar med definierar alla variabler som används av R-koden och motsvarande SQL-datatyper.

- Raderna som omedelbart följer mappa SQL-parameternamnen till motsvarande R-variabelnamn.

Nu när du har slagit in R-funktionen i en lagrad procedur kan du enkelt anropa funktionen och skicka in olika värden, så här:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Använda R-verktygsfunktioner för felsökning

Paketet, `utils` som installeras som standard, innehåller en mängd olika verktygsfunktioner för att undersöka den aktuella R-miljön. Dessa funktioner kan vara användbara om du hittar avvikelser i hur din R-kod fungerar i SQL och i externa miljöer. Du kan till exempel `memory.limit()` använda R-funktionen för att hämta minne för den aktuella R-miljön.

Eftersom `utils` paketet är installerat men inte läst som `library()` standard måste du använda funktionen för att läsa in det först.

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
> Många användare gillar att använda systemets tidsinställningsfunktioner i R, till exempel och `system.time` `proc.time`för att fånga den tid som används av R-processer och analysera prestandaproblem.
