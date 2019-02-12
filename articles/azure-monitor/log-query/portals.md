---
title: Visa och analysera loggdata i Azure Monitor | Microsoft Docs
description: Den här artikeln beskriver hur du använder Log Analytics i Azure-portalen att skapa och redigera loggfrågor i Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/22/2018
ms.author: bwren
ms.openlocfilehash: 9567f8a6b581d7c246ebaa8eb8d72ad201bf2641
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990419"
---
# <a name="viewing-and-analyzing-log-data-in-azure-monitor"></a>Visa och analysera loggdata i Azure Monitor
Log Analytics är den primära upplevelsen för att arbeta med loggdata och skapa frågor i Azure Monitor. Öppna Log Analytics från **loggar** i den **Azure Monitor** menyn. Du kan få en introduktion till den här portalen och granska dess funktioner på [Kom igång med Log Analytics i Azure-portalen](get-started-portal.md).

Log Analytics tillhandahåller följande funktioner för att arbeta med loggfrågor.

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
> Log Analytics har samma funktioner som Advanced Analytics-portalen som är något externt verktyg utanför Azure-portalen. Advanced Analytics-portalen finns kvar, men länkar och andra referenser till den i Azure-portalen ersätts med den nya sidan.

![Log Analytics](media/portals/log-analytics.png)

### <a name="resource-logs"></a>Resurs-loggar
Log Analytics integreras med olika Azure-resurser som virtuella datorer. Det innebär att du kan öppna Log Analytics direkt via övervakning resursmenyn utan att växla till Azure Monitor och att förlora resurs-kontext. **Loggar** har ännu inte aktiverats för alla Azure-resurser, men den börjar visas i portalmenyn för olika resurser typer.

När du öppnar Log Analytics från en specifik resurs, begränsas den automatiskt för att logga poster för den resursen endast.   Om du vill skriva en fråga som innehåller andra poster, skulle du måste öppna den från Azure Monitor-menyn.

Följande alternativ är ännu inte tillgängliga via vyn av Log Analytics:

- Spara
- Ställ in en avisering
- Frågeutforskaren
- Växling till annan arbetsyta/resurs (för närvarande inte planerad)


### <a name="firewall-requirements"></a>Brandväggskrav
Din webbläsare kräver åtkomst till följande adresser åtkomst till Log Analytics.  Om webbläsaren har åtkomst till Azure-portalen via en brandvägg, måste du aktivera åtkomst till dessa adresser.

| URI | IP-adress | Portar |
|:---|:---|:---|
| portal.loganalytics.io | Dynamisk | 80,443 |
| api.loganalytics.io    | Dynamisk | 80,443 |
| docs.loganalytics.io   | Dynamisk | 80,443 |


## <a name="log-search-classic"></a>Loggsökning (klassisk)
Loggsökning är den äldre upplevelsen i Azure-portalen för att fråga och analysera loggdata i Azure Monitor. Det kommer kommer att dras tillbaka snart men är fortfarande tillgänglig för närvarande. Öppna Loggsökning från **loggar (klassisk)** i Log Analytics-menyn.



![Loggsökning](media/portals/log-search-portal.png)


## <a name="next-steps"></a>Nästa steg

- Gå igenom en [självstudier med Log Analytics](../../azure-monitor/log-query/get-started-portal.md).
- Gå igenom en [självstudier med Loggsökning](../../azure-monitor/learn/tutorial-viewdata.md).

