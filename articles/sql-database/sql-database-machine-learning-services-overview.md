---
title: Machine Learning Services med R (för hands version)
description: Den här artikeln beskriver Azure SQL Database Machine Learning Services (med R) och förklarar hur det fungerar.
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
ms.openlocfilehash: ca223de2bc0b26e4968d400ea418761a399dacae
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364606"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services med R (för hands version)

Machine Learning Services är en funktion i Azure SQL Database som används för att köra in-Database R-skript. Funktionen innehåller Microsoft R-paket för förutsägelse analys och maskin inlärning med höga prestanda. Relations data kan användas i R-skript genom lagrade procedurer, T-SQL-skript som innehåller R-instruktioner eller R-kod som innehåller T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> För hands versionen är tillgänglig för enskilda databaser och elastiska pooler med hjälp av den vCore-baserade inköps modellen i de **allmänna** och **affärs kritiska** tjänst nivåerna. I den här inledande för hands versionen stöds inte den **storskaliga** tjänst nivån och distributions alternativet för **hanterad instans** . R är för närvarande det enda språk som stöds. Det finns inget stöd för Python just nu.
>
> För hands versionen är för närvarande tillgänglig i följande regioner: Västeuropa, norra Europa, västra USA 2, östra USA, södra centrala USA, norra centrala USA, centrala Kanada, Sydostasien, Indien, södra och Australien, sydöstra.

## <a name="what-you-can-do-with-r"></a>Vad du kan göra med R

Använd kraften hos R-språket för att leverera avancerad analys och maskininlärning i databasen. Den här funktionen ger beräkning och bearbetning där data finns, vilket eliminerar behovet av att hämta data över nätverket. Du kan också utnyttja kraften hos Enterprise R-paket för att leverera avancerad analys i stor skala.

Machine Learning Services innehåller en grundläggande distribution av R med överlägg med R-företagspaket från Microsoft. Microsofts R-funktioner och algoritmer är utformade för både stor skala och nytta. De levererar förutsägelseanalys, statistisk modellering, datavisualiseringar och ledande algoritmer för maskininlärning.

### <a name="r-packages"></a>R-paket

De flesta vanliga R-paket med öppen källkod är förinstallerade i Machine Learning Services. Följande R-paket från Microsoft ingår också:

| R-paket | Beskrivning|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open är den förbättrade distributionen av R från Microsoft. Det är en komplett plattform med öppen källkod för statistisk analys och data vetenskap. Den är baserad på och 100% kompatibel med R och innehåller ytterligare funktioner för bättre prestanda och reproducerbarhet. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR är det primära biblioteket för skalbar R. funktioner i det här biblioteket är bland de vanligaste. Data transformationer och manipulering, statistisk sammanfattning, visualisering och många former av modellering och analyser finns i dessa bibliotek. Dessutom distribuerar funktioner i dessa bibliotek automatiskt arbets belastningar över tillgängliga kärnor för parallell bearbetning, med möjlighet att arbeta med segment med data som koordineras och hanteras av beräknings motorn. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML lägger till Machine Learning-algoritmer för att skapa anpassade modeller för text analys, bild analys och sentiment analys. |

Förutom de förinstallerade paketen kan du [installera ytterligare paket](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registrera dig för förhandsversionen

> [!IMPORTANT]
> Registrera dig för Azure SQL Database Machine Learning Services (förhands granskning) är för närvarande stängd.

Machine Learning Services med R rekommenderas inte för produktions arbets belastningen under för hands versionen.

## <a name="next-steps"></a>Nästa steg

- Se [viktiga skillnader från SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Information om hur du använder R för att fråga Azure SQL Database Machine Learning Services (förhands granskning) finns i [snabb starts guiden](sql-database-connect-query-r.md).
- För att komma igång med vissa enkla R-skript, se [skapa och köra enkla r-skript i Azure SQL Database Machine Learning Services (för hands version)](sql-database-quickstart-r-create-script.md).
