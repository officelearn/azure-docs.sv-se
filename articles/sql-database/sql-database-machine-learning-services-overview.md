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
ms.date: 03/01/2019
ms.openlocfilehash: a2af1fdd1ee461e3b3db613ff4a575649da2dfdc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827437"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services med R (för hands version)

Machine Learning Services är en funktion i Azure SQL Database som används för att köra in-Database R-skript. Funktionen innehåller Microsoft R-paket för förutsägelse analys och maskin inlärning med höga prestanda. Relations data kan användas i R-skript genom lagrade procedurer, T-SQL-skript som innehåller R-instruktioner eller R-kod som innehåller T-SQL.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services (med R) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Den offentliga för hands versionen är tillgänglig för enskilda databaser och elastiska pooler med hjälp av den vCore-baserade inköps modellen i tjänste nivåerna **allmän användning** och **affärs kritisk** . I den här inledande offentliga för hands versionen stöds inte den **storskaliga** tjänst nivån och distributions alternativet för **hanterad instans** . R är för närvarande det enda språk som stöds. Det finns inget stöd för Python just nu.
>
> För hands versionen är för närvarande tillgänglig i följande regioner: Västeuropa, norra Europa, västra USA 2, östra USA, södra centrala USA, norra centrala USA, centrala Kanada, Sydostasien, Indien, södra och Australien, sydöstra.
>
> [Registrera dig för för hands versionen](#signup) nedan.

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

Registrera dig för den offentliga för hands versionen genom att följa dessa steg:

1. Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

2. Skicka ett e-postmeddelande till Microsoft på [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) om du vill registrera dig för den offentliga förhandsversionen. Den offentliga förhandsversionen av Machine Learning Services (med R) i SQL Database är inte aktiverad som standard.

När du har registrerat dig i programmet kommer Microsoft att publicera dig till den offentliga för hands versionen och aktivera R för din befintliga eller nya databas.

Machine Learning Services med R rekommenderas inte för produktions arbets belastningen under den offentliga för hands versionen.

## <a name="next-steps"></a>Nästa steg

- Se [viktiga skillnader från SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Information om hur du använder R för att fråga Azure SQL Database Machine Learning Services (förhands granskning) finns i [snabb starts guiden](sql-database-connect-query-r.md).
- För att komma igång med vissa enkla R-skript, se [skapa och köra enkla r-skript i Azure SQL Database Machine Learning Services (för hands version)](sql-database-quickstart-r-create-script.md).
