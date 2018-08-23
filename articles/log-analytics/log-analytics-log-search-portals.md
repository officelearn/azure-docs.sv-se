---
title: Visa och analysera data i Azure Log Analytics | Microsoft Docs
description: Den här artikeln beskriver portaler som du kan använda i Azure Log Analytics för att skapa och redigera loggsökningar.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 386aad94461fa3f2ceafb7564342797eefa2f086
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42061135"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Visa och analysera data i Log Analytics
Det finns två alternativ som är tillgängliga i Azure-portalen för att analysera data som lagras i Log analytics och för att skapa frågor för ad hoc-analyser. Frågor som du skapar med hjälp av dessa portaler kan användas för andra funktioner, till exempel aviseringar och instrumentpaneler.

## <a name="log-analytics-page-preview"></a>Sidan för log Analytics (förhandsversion)
Öppna sidan Log Analytics från **loggar (förhandsversion)** i Log Analytics-menyn. Det här är en ny upplevelse för att arbeta med loggdata och skapa frågor. Du kan få en introduktion till den här portalen och granska dess funktioner på [Kom igång med Log Analytics-sidan i Azure-portalen](query-language/get-started-analytics-portal.md).

Sidan Log Analytics innehåller följande förbättringar över den [loggsökning](#log-search) upplevelse.

* Flera flikar – skapa separata flikar för att arbeta med flera frågor.
* Visualiseringar – olika diagramalternativ.
* Förbättrad Intellisense och språk automatisk komplettering.
* Syntaxmarkering – förbättrar läsbarheten för frågor. 
* Frågeutforskaren – åtkomst sparade frågor och fungerar.
* Schemat visa – granska strukturen för dina data för att hjälpa skriva frågor.
* Dela – skapa länkar till frågor eller PIN-kod frågor till en delad instrumentpanel i Azure.
* Smart Analytics – identifierar toppar i dina diagram och en snabb analys av orsaken.
* Kolumnurval – sortera och gruppera kolumner i resultatet av frågan.

> [!NOTE]
> Sidan Log Analytics har samma funktioner som Advanced Analytics-portalen som är något externt verktyg utanför Azure-portalen. Advanced Analytics-portalen finns kvar, men länkar och andra referenser till den i Azure-portalen ersätts med den nya sidan.

![Avancerade analysportalen](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


### <a name="firewall-requirements"></a>Brandväggskrav
Din webbläsare kräver åtkomst till följande adresser för åtkomst till Log Analytics-sidan och Advanced Analytics-portalen.  Om webbläsaren har åtkomst till Azure-portalen via en brandvägg, måste du aktivera åtkomst till dessa adresser.

| URI | IP-adress | Portar |
|:---|:---|:---|
| portal.loganalytics.io | Dynamisk | 80,443 |
| api.loganalytics.io    | Dynamisk | 80,443 |
| docs.loganalytics.io   | Dynamisk | 80,443 |


## <a name="log-search"></a>Loggsökning
Öppna sidan Log search från **loggar** i Log Analytics-menyn eller från **Log Analytics** i Azure Monitor-menyn. Detta är lämpligt för att analysera loggdata med hjälp av grundläggande frågor. Det finns flera funktioner för redigering frågor utan fullständiga kunskaper om frågespråket.  Du kan få en sammanfattning av dessa funktioner i [skapa loggsökningar i Azure Log Analytics med hjälp av Loggsökning](log-analytics-log-search-log-search-portal.md). 


![Logga söksidan](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>Nästa steg

- Gå igenom en [självstudier med Loggsökning](log-analytics-tutorial-viewdata.md) att lära dig hur du skapar frågor med frågespråket
- Gå igenom en [lektion med hjälp av Advanced Analytics-portalen](query-language/get-started-analytics-portal.md) som ger samma användarupplevelse som Log Analytics-sida.

