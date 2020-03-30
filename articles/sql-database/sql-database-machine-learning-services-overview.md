---
title: Machine Learning Services med R (förhandsgranskning)
description: I den här artikeln beskrivs Azure SQL Database Machine Learning Services (med R) och hur det fungerar.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ms.openlocfilehash: 2a2cd4bfc3d393543b41eea776f02723d94054b1
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345832"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services med R (förhandsgranskning)

Machine Learning Services är en funktion i Azure SQL Database, som används för att köra R-skript i databasen. Funktionen innehåller Microsoft R-paket för högpresterande prediktiv analys och maskininlärning. Relationsdata kan användas i R-skript genom lagrade procedurer, T-SQL-skript som innehåller R-satser eller R-kod som innehåller T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> Förhandsgranskningen är tillgänglig för enskilda databaser och elastiska pooler med hjälp av den vCore-baserade inköpsmodellen på de **allmänna och** **affärskritiska** tjänstnivåerna. I den här inledande förhandsversionen stöds inte **tjänstnivån hyperskala** och distributionsalternativet **för hanterade instanser.** R är för närvarande det enda språk som stöds. Det finns inget stöd för Python just nu.
>
> Förhandsversionen är för närvarande tillgänglig i följande regioner: Västeuropa, Norra Europa, västra USA 2, östra USA, södra centrala USA, norra centrala USA, Centrala Kanada, Sydostasien, Södra Indien och Sydöstra Australien.

## <a name="what-you-can-do-with-r"></a>Vad du kan göra med R

Använd kraften hos R-språket för att leverera avancerad analys och maskininlärning i databasen. Den här funktionen ger beräkning och bearbetning där data finns, vilket eliminerar behovet av att hämta data över nätverket. Du kan också utnyttja kraften i företagets R-paket för att leverera avancerad analys i stor skala.

Machine Learning Services innehåller en grundläggande distribution av R med överlägg med R-företagspaket från Microsoft. Microsofts R-funktioner och algoritmer är utformade för både stor skala och nytta. De levererar förutsägelseanalys, statistisk modellering, datavisualiseringar och ledande algoritmer för maskininlärning.

### <a name="r-packages"></a>R-paket

De vanligaste R-paketen med öppen källkod är förinstallerade i Machine Learning Services. Följande R-paket från Microsoft ingår också:

| R-paket | Beskrivning|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open är den förbättrade distributionen av R från Microsoft. Det är en komplett plattform med öppen källkod för statistisk analys och datavetenskap. Den är baserad på och 100% kompatibel med R, och innehåller ytterligare funktioner för förbättrad prestanda och reproducerbarhet. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR är det primära biblioteket för skalbara R. Funktioner i det här biblioteket är bland de mest använda. Dataomvandlingar och manipulering, statistisk summering, visualisering och många former av modellering och analyser finns i dessa bibliotek. Dessutom distribuerar funktioner i dessa bibliotek automatiskt arbetsbelastningar över tillgängliga kärnor för parallell bearbetning, med möjlighet att arbeta med datasegment som koordineras och hanteras av beräkningsmotorn. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML lägger till maskininlärningsalgoritmer för att skapa anpassade modeller för textanalys, bildanalys och sentimentanalys. |

Förutom de förinstallerade paketen kan du [installera ytterligare paket](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>Registrera dig för förhandsgranskningen (stängd)

> [!IMPORTANT]
> Registrering för Azure SQL Database Machine Learning Services (förhandsversion) är för närvarande **stängd**.

## <a name="next-steps"></a>Nästa steg

- Se de [viktigaste skillnaderna från SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Mer information om hur du använder R för att fråga Azure SQL Database Machine Learning Services (förhandsversion) finns i [snabbstartsguiden](sql-database-connect-query-r.md).
- Information om hur du kommer igång med några enkla R-skript finns i [Skapa och köra enkla R-skript i Azure SQL Database Machine Learning Services (förhandsversion).](sql-database-quickstart-r-create-script.md)
