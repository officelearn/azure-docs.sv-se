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
ms.date: 09/17/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 8a73ad7c17710ab3e6e4ed41eced643ce9eb86dc
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042486"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Visa och analysera data i Log Analytics
Det finns två alternativ som är tillgängliga i Azure-portalen för att analysera data som lagras i Log analytics och för att skapa frågor för ad hoc-analyser. Frågor som du skapar med hjälp av dessa portaler kan användas för andra funktioner, till exempel aviseringar och instrumentpaneler.

## <a name="log-analytics-page"></a>Sidan för log Analytics
Öppna sidan Log Analytics från **loggar** i Log Analytics-menyn. Det här är en ny upplevelse för att arbeta med loggdata och skapa frågor. Du kan få en introduktion till den här portalen och granska dess funktioner på [Kom igång med Log Analytics-sidan i Azure-portalen](query-language/get-started-analytics-portal.md).

Sidan Log Analytics innehåller följande förbättringar över den [loggsökning (klassisk)](#log-search-classic) upplevelse.

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

### <a name="resource-logs"></a>Resurs-loggar
Den nya Log Analytics-upplevelsen kan integreras med olika Azure-resurser som virtuella datorer. Det innebär att du kan öppna sidan Log Analytics direkt via övervakning resursmenyn utan att växla till Azure Monitor eller Log Analytics och att förlora resurs-kontext. **Loggar** har ännu inte aktiverats för alla Azure-resurser, men den börjar visas i portalmenyn för olika resurser typer.

När du öppnar Log Analytics från en specifik resurs, begränsas den automatiskt för att logga poster för den resursen endast.   Om du vill skriva en fråga som innehåller andra poster, skulle du måste öppna menyn Log Analytics och Azure Monitor.

Följande alternativ är ännu inte tillgängliga via vyn av Log Analytics:

- Spara
- Ställ in en avisering
- Frågeutforskaren
- Växling till annan arbetsyta/resurs (för närvarande inte planerad)


### <a name="firewall-requirements"></a>Brandväggskrav
Din webbläsare kräver åtkomst till följande adresser för åtkomst till Log Analytics-sidan och Advanced Analytics-portalen.  Om webbläsaren har åtkomst till Azure-portalen via en brandvägg, måste du aktivera åtkomst till dessa adresser.

| URI | IP-adress | Portar |
|:---|:---|:---|
| portal.loganalytics.io | Dynamisk | 80,443 |
| api.loganalytics.io    | Dynamisk | 80,443 |
| docs.loganalytics.io   | Dynamisk | 80,443 |


## <a name="log-search-classic"></a>Loggsökning (klassisk)
Öppna sidan Log search från **loggar (klassisk)** i Log Analytics-menyn eller från **Log Analytics** i Azure Monitor-menyn. Det här är sidan klassiska används för att arbeta med Log Analytics-frågor som saknar de ytterligare funktionerna i den [sidan Log Analytics](#log-analytics-page) ovan.



![Logga söksidan](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>Nästa steg

- Gå igenom en [självstudier med Loggsökning](log-analytics-tutorial-viewdata.md) att lära dig hur du skapar frågor med frågespråket
- Gå igenom en [lektion med hjälp av Advanced Analytics-portalen](query-language/get-started-analytics-portal.md) som ger samma användarupplevelse som Log Analytics-sida.

