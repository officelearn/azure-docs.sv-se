---
title: Logg frågans omfång i Azure Monitor Log Analytics | Microsoft Docs
description: Beskriver omfånget och tidsintervallet för en logg fråga i Azure Monitor Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 2840e5b8ff16d44f76aaafcf68264c65e4401ff7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83199017"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Logg frågans omfång och tidsintervall i Azure Monitor Log Analytics
När du kör en [logg fråga](log-query-overview.md) i [Log Analytics i Azure Portal](get-started-portal.md), beror den uppsättning data som utvärderas av frågan på omfattningen och tidsintervallet som du väljer. I den här artikeln beskrivs omfattning och tidsintervall och hur du kan ställa in dem beroende på dina behov. Det beskriver också beteendet för olika typer av omfång.


## <a name="query-scope"></a>Frågeomfång
Fråge omfånget definierar de poster som utvärderas av frågan. Detta omfattar vanligt vis alla poster i en enskild Log Analytics arbets yta eller Application Insights program. Med Log Analytics kan du också ange ett omfång för en viss övervakad Azure-resurs. På så sätt kan en resurs ägare bara fokusera på sina data, även om den resursen skriver till flera arbets ytor.

Omfattningen visas alltid längst upp till vänster i Log Analyticss fönstret. En ikon visar om omfånget är en Log Analytics arbets yta eller ett Application Insights program. Ingen ikon indikerar en annan Azure-resurs.

![Omfång](media/scope/scope.png)

Omfattningen bestäms av den metod som du använder för att starta Log Analytics, och i vissa fall kan du ändra omfattningen genom att klicka på den. I följande tabell visas olika typer av omfång som används och olika Detaljer för var och en.

> [!IMPORTANT]
> Om du använder APM 2,1 lagras Application Insights program i en Log Analytics arbets yta med alla andra loggdata, och Application Insights omfattningen är inte tillgänglig. Om du väljer **loggar** på Application Insights-menyn fungerar det på samma sätt som de **andra Azure-resursernas** omfattning, och endast data från det programmet i Application Insights-tabellerna är tillgängliga.

| Frågeomfång | Poster i omfånget | Så här väljer du | Ändra omfång |
|:---|:---|:---|:---|
| Log Analytics-arbetsyta | Alla poster i Log Analytics-arbetsytan. | Välj **loggar** på **Azure Monitor** -menyn eller **Log Analytics arbets ytans** meny.  | Kan ändra omfång till någon annan resurs typ. |
| Application Insights program | Alla poster i Application Insights programmet. | Välj **Analytics** från **översikts** sidan för Application Insights. | Det går bara att ändra omfattningen till ett annat Application Insights-program. |
| Resursgrupp | Poster som skapats av alla resurser i resurs gruppen. Kan innehålla data från flera Log Analytics arbets ytor. | Välj **loggar** i menyn resurs grupp. | Det går inte att ändra omfång.|
| Prenumeration | Poster som skapats av alla resurser i prenumerationen. Kan innehålla data från flera Log Analytics arbets ytor. | Välj **loggar** på menyn prenumeration.   | Det går inte att ändra omfång. |
| Andra Azure-resurser | Poster som skapats av resursen. Kan innehålla data från flera Log Analytics arbets ytor.  | Välj **loggar** på resurs-menyn.<br>ELLER<br>Välj **loggar** på **Azure Monitor** -menyn och välj sedan ett nytt omfång. | Det går bara att ändra omfattningen till samma resurs typ. |

### <a name="limitations-when-scoped-to-a-resource"></a>Begränsningar vid omfattning av en resurs

När fråge omfånget är en Log Analytics arbets yta eller ett Application Insights program är alla alternativ i portalen och alla frågor tillgängliga. När den är begränsad till en resurs kan följande alternativ i portalen inte vara tillgängliga eftersom de är kopplade till en enda arbets yta eller ett program:

- Spara
- Query Explorer
- Ny varnings regel

Du kan inte använda följande kommandon i en fråga som är begränsad till en resurs eftersom fråge omfånget redan innehåller arbets ytor med data för resursen eller en uppsättning resurser:

- [mobilappar](app-expression.md)
- [platsen](workspace-expression.md)
 

## <a name="query-limits"></a>Frågebegränsningar
Du kan ha verksamhets krav för en Azure-resurs för att skriva data till flera Log Analytics-arbetsytor. Arbets ytan behöver inte finnas i samma region som resursen, och en enda arbets yta kan samla in data från resurser i flera olika regioner.  

Att ange ett omfång till en resurs eller en uppsättning resurser är en särskilt kraftfull funktion i Log Analytics eftersom det gör det möjligt att automatiskt konsolidera distribuerade data i en enda fråga. Det kan påverka prestanda avsevärt om data behöver hämtas från arbets ytor i flera Azure-regioner.

Log Analytics skyddar mot onödig omkostnader från frågor som omfattar arbets ytor i flera regioner genom att skicka en varning eller ett fel när ett visst antal regioner används. Frågan får en varning om omfånget omfattar arbets ytor i 5 eller flera regioner. den kommer fortfarande att köras, men det kan ta lång tid att slutföra den.

![Fråga-varning](media/scope/query-warning.png)

Frågan kommer att blockeras från att köras om omfattningen omfattar arbets ytor i 20 eller flera regioner. I det här fallet uppmanas du att minska antalet regioner för arbets ytor och försöka köra frågan igen. I list rutan visas alla regioner i frågans omfång och du bör minska antalet regioner innan du försöker köra frågan igen.

![Frågan misslyckades](media/scope/query-failed.png)


## <a name="time-range"></a>Tidsintervall
Tidsintervallet anger den uppsättning poster som utvärderas för frågan baserat på när posten skapades. Detta definieras av en standard egenskap på varje post i arbets ytan eller programmet som anges i följande tabell.

| Location | Egenskap |
|:---|:---|
| Log Analytics-arbetsyta          | TimeGenerated |
| Application Insights program | timestamp     |

Ange tidsintervallet genom att välja det från tids väljaren överst i Log Analyticss fönstret.  Du kan välja en fördefinierad period eller välja **anpassad** om du vill ange ett tidsintervall.

![Tids väljare](media/scope/time-picker.png)

Om du anger ett filter i frågan som använder egenskapen standard tid som visas i tabellen ovan, ändras tids väljaren till **set i Query**och tids väljare är inaktive rad. I det här fallet är det mest effektivt att ställa in filtret överst i frågan så att all efterföljande bearbetning bara behöver arbeta med de filtrerade posterna.

![Filtrerad fråga](media/scope/query-filtered.png)

Om du använder [arbets ytan](workspace-expression.md) eller [appens](app-expression.md) kommando för att hämta data från en annan arbets yta eller ett annat program kan tids väljaren bete sig annorlunda. Om omfånget är en Log Analytics arbets yta och du använder **appen**, eller om omfånget är ett Application Insights program och du använder **arbets ytan**, kan Log Analytics inte förstå att den egenskap som används i filtret bör fastställa tids filtret.

I följande exempel är omfånget inställt på en Log Analytics-arbetsyta.  Frågan använder **arbets ytan** för att hämta data från en annan Log Analytics-arbetsyta. Tids väljaren ändras till **set i frågan** eftersom den ser ett filter som använder den förväntade **TimeGenerated** -egenskapen.

![Fråga med arbets yta](media/scope/query-workspace.png)

Om frågan använder **appen** för att hämta data från ett Application Insights program, kan Log Analytics inte identifiera **timestamp** -egenskapen i filtret och tid väljaren förblir oförändrad. I det här fallet tillämpas båda filtren. I exemplet ingår endast poster som skapats under de senaste 24 timmarna i frågan även om det anger 7 dagar i **WHERE** -satsen.

![Fråga med app](media/scope/query-app.png)

## <a name="next-steps"></a>Nästa steg

- Gå igenom en [själv studie kurs om hur du använder Log Analytics i Azure Portal](get-started-portal.md).
- Gå igenom en [själv studie kurs om att skriva frågor](get-started-queries.md).