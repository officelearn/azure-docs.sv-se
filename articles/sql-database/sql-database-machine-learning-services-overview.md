---
title: Machine Learning-tjänster (med R) i översikt över Azure SQL Database (förhandsversion)
description: Det här avsnittet beskrivs Azure SQL Database Machine Learning Services (med R) och hur det fungerar.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: e2159e7cc59830c3d0d10f1c5b9697ab5b45b666
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824292"
---
# <a name="machine-learning-services-with-r-in-azure-sql-database-preview"></a>Machine Learning-tjänster (med R) i Azure SQL Database (förhandsversion)

Machine Learning Services är en funktion i Azure SQL-databas som används för att köra R-skript i databasen. Funktionen innehåller Microsoft R-paket för högpresterande förutsägande analys och maskininlärning. Relationella data kan användas i R-skript via lagrade procedurer, T-SQL-skript som innehåller R-uttryck eller R-kod som innehåller T-SQL.

> [!NOTE]
> Machine Learning-tjänster (med R) i Azure SQL Database är för närvarande i offentlig förhandsversion. [Registrera dig för förhandsversionen av](#signup) nedan.

## <a name="what-you-can-do-with-r"></a>Vad du kan göra med R

Använd kraften i R-språket för att leverera avancerad analys och maskininlärning i databasen. Den här möjligheten ger beräkningar och bearbetning till där data finns, vilket eliminerar behovet av att hämta data över nätverket. Dessutom dra nytta av enterprise R-paket att tillhandahålla avancerade analyser i stor skala.

Machine Learning Services innehåller en grundläggande distribution av R, ett överlägg med enterprise R-paket från Microsoft. Microsoft R-funktioner och algoritmer är utformad för både skalning och verktyg, leverera förutsägande analys, statistisk modellering, datavisualiseringar och ledande machine learning-algoritmer.

### <a name="r-packages"></a>R-paket

De vanligaste R med öppen källkod-paket är förinstallerade i Machine Learning Services. Följande R-paket från Microsoft ingår också:

| R-paket | Beskrivning|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open är de förbättrade R från Microsoft-distributionen. Det är en plattform för fullständig öppen källkod för statistiska analyser och datavetenskap. Den är baserad på och 100% kompatibel med R och omfattar ytterligare funktioner för bättre prestanda och reproducerbarhet. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR är primärt bibliotek för skalbar R. funktioner i det här biblioteket är bland de mest använda. Dataomvandlingar och manipulering, Statistisk sammanfattning, visualisering och modellering och analyser på många sätt finns i dessa bibliotek. Dessutom distribuera funktioner i dessa bibliotek automatiskt arbetsbelastningar över tillgängliga kärnor för parallell bearbetning, med möjlighet att arbeta med datasegment som samordnas och hanteras av beräkningsmotorn för. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML lägger till machine learning-algoritmer för att skapa anpassade modeller för textanalys, bildanalys och attitydanalys. |

Förutom de förinstallerade paket kan du [installera ytterligare paket](sql-database-connect-query-r.md#add-package).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registrera dig för förhandsversionen

Om du vill registrera dig för den offentliga förhandsversionen kan du följa dessa steg:

1. Om du inte har någon Azure-prenumeration [skapa ett konto](https://azure.microsoft.com/free/) innan du börjar.

2. Skicka ett e-postmeddelande till Microsoft på [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) om du vill registrera dig för den offentliga förhandsversionen. Den offentliga förhandsversionen av Machine Learning Services (med R) i SQL Database är inte aktiverad som standard.

När du är registrerad i programmet Microsoft publicerar du så att den allmänna förhandsgranskningen och aktivera R din befintliga eller nya databas.

Machine Learning-tjänster (med R) i SQL-databas är för närvarande endast tillgängliga i den vCore-baserade inköpsmodellen i den **generella** och **affärskritisk** tjänstnivåer för fristående och distributionsalternativ för elastisk pool. I den här första offentliga förhandsversionen kan den **hyperskala** tjänstnivå och **Managed Instance** distributionsalternativ stöds inte.

R är för närvarande det enda språk som stöds. Det finns inget stöd för Python just nu. Förhandsgranskningen är tillgängliga i den följande regioner: västra Europa, Norra Europa, västra USA 2, östra USA, södra centrala USA, norra centrala USA, Kanada, centrala, Sydostasien, södra och Australien, sydöstra Australien. Fler regioner kommer att läggas till senare.

Använd inte Machine Learning-tjänster med R för produktionsarbetsbelastningar den offentliga förhandsversionen.

## <a name="next-steps"></a>Nästa steg

- Se den [viktiga skillnader från SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md)
- Läs hur du använder Machine Learning-tjänster (med R) i Azure SQL Database i [Snabbstartsguide](sql-database-connect-query-r.md).
- Lär dig mer med [självstudier för SQL Server R-språk](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)