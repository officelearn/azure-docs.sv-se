---
title: Machine Learning Services med R (för hands version)
description: Den här artikeln beskriver Azure SQL Database Machine Learning Services (med R) och förklarar hur det fungerar.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0a11e2ba820797bac8ce93517841fd37a8256d2c
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413067"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services med R (för hands version)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Machine Learning Services är en funktion i Azure SQL Database som används för att köra in-Database R-skript. Funktionen innehåller Microsoft R-paket för förutsägelse analys och maskin inlärning med höga prestanda. Relations data kan användas i R-skript genom lagrade procedurer, T-SQL-skript som innehåller R-instruktioner eller R-kod som innehåller T-SQL.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="what-you-can-do-with-r"></a>Vad du kan göra med R

Använd kraften i R-språket för att leverera avancerade analyser och Machine Learning i-Database. Den här funktionen ger beräkning och bearbetning där data finns, vilket eliminerar behovet av att hämta data över nätverket. Du kan också utnyttja kraften hos Enterprise R-paket för att leverera avancerad analys i stor skala.

Machine Learning Services innehåller en grundläggande distribution av R med överlägg med R-företagspaket från Microsoft. Microsofts R-funktioner och algoritmer är utformade för både stor skala och nytta. De levererar förutsägelseanalys, statistisk modellering, datavisualiseringar och ledande algoritmer för maskininlärning.

### <a name="r-packages"></a>R-paket

De flesta vanliga R-paket med öppen källkod är förinstallerade i Machine Learning Services. Följande R-paket från Microsoft ingår också:

| R-paket | Description|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open är den förbättrade distributionen av R från Microsoft. Det är en komplett plattform med öppen källkod för statistisk analys och data vetenskap. Den är baserad på och 100% kompatibel med R och innehåller ytterligare funktioner för bättre prestanda och reproducerbarhet. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR är det primära biblioteket för skalbar R. funktioner i det här biblioteket är bland de vanligaste. Data transformationer och manipulering, statistisk sammanfattning, visualisering och många former av modellering och analyser finns i dessa bibliotek. Dessutom distribuerar funktioner i dessa bibliotek automatiskt arbets belastningar över tillgängliga kärnor för parallell bearbetning, med möjlighet att arbeta med segment med data som koordineras och hanteras av beräknings motorn. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML lägger till Machine Learning-algoritmer för att skapa anpassade modeller för text analys, bild analys och sentiment analys. |

Förutom de förinstallerade paketen kan du [installera ytterligare paket](machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>Registrera dig för för hands versionen (stängd)

> [!IMPORTANT]
> Registrera dig för Azure SQL Database Machine Learning Services (förhands granskning) är för närvarande **stängd**.

## <a name="next-steps"></a>Nästa steg

- Se [viktiga skillnader från SQL Server Machine Learning Services](machine-learning-services-differences.md).
- Information om hur du använder R för att fråga Azure SQL Database Machine Learning Services (förhands granskning) finns i [snabb starts guiden](connect-query-r.md).
- För att komma igång med vissa enkla R-skript, se [skapa och köra enkla r-skript i Azure SQL Database Machine Learning Services (för hands version)](r-script-create-quickstart.md).
 