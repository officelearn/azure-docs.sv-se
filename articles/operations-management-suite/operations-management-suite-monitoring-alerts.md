---
title: Varna hantering i Microsoft monitoring produkter | Microsoft Docs
description: En avisering anger vissa problem som kräver uppmärksamhet från en administratör.  Den här artikeln beskriver skillnaderna i hur aviseringar skapas och hanteras i System Center Operations Manager (SCOM) och logganalys och ger bästa praxis i utnyttja de här två produkterna för en strategi för avisering hybrid.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7df2fd7ef838465a60e3b0ce2f889127b7487684
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865773"
---
# <a name="managing-alerts-with-microsoft-monitoring"></a>Hantera aviseringar med övervakning av Microsoft
En avisering anger vissa problem som kräver uppmärksamhet från en administratör.  Det finns specifika skillnader mellan System Center Operations Manager (SCOM) och logganalys i Operations Management Suite (OMS) när det gäller hur aviseringar skapas, hur de hanteras och analyseras och hur du meddelas att ett allvarligt problem har upptäckts.

## <a name="alerts-in-operations-manager"></a>Varningar i Operations Manager
Aviseringar i SCOM genereras av enskilda regler eller Övervakare som visar ett specifikt problem.  En Övervakare kan generera en avisering när den försätts i ett feltillstånd när en regel kan generera en avisering som indikerar att vissa viktiga problem som inte är direkt relaterade till hälsotillståndet för ett hanterat objekt.  Hanteringspaket omfattar en mängd olika arbetsflöden som skapar aviseringar för programmet eller tjänsten som de hanterar.  En del av processen att konfigurera ett nytt management pack finjustera det så att du inte får mycket aviseringar för problem som du inte anser kritiska.

![SCOM Aviseringsvy](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM ger fullständig aviseringshanteringen aviseringar som har en status som kan ändras av administratörer när de arbetar för att lösa problemet.  Administratören definierar aviseringen stängs då visas den inte längre i vyer som visar aktiva aviseringar när problemet har lösts.  Aviseringar som genereras av Övervakare kan lösas automatiskt när övervakaren återgår till felfritt tillstånd.

![Aviseringsstatus](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Aviseringar i logganalys
En avisering i logganalys skapas från en logg-fråga som körs automatiskt med jämna mellanrum.  Du kan skapa en aviseringsregel från alla log-fråga.  Om frågan returnerar resultat som matchar de villkor som du anger, skapas en avisering.  Detta kan vara en specifik fråga som skapar en avisering om en viss händelse identifieras eller du kan använda en mer allmän fråga som ser ut för fel händelser relaterade till ett visst program.

Log Analytics aviseringar skrivs till OMS-databasen som en händelse och kan hämtas med en fråga som loggen.  De har inte statusen som SCOM händelser så att du kan ange när problemet har lösts.

![OMS-avisering](media/operations-management-suite-monitoring-alerts/oms-alert.png)

När SCOM används som datakälla för Log Analytics, skrivs SCOM aviseringar i OMS-databasen som skapas och ändras.  

![SCOM-avisering](media/operations-management-suite-monitoring-alerts/scom-alert.png)

Den [avisering hanteringslösning](http://technet.microsoft.com/library/mt484092.aspx) innehåller en översikt över aktiva aviseringar och flera vanliga frågor för att hämta olika uppsättningar av aviseringar.  Detta ger dig effektivare analys av aviseringarna än en rapport i SCOM.  Kan du detaljgranska från sammanfattningarna till detaljerade data och skapa ad hoc-frågor för att hämta olika uppsättningar av aviseringar.

![Lösning för avisering](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Meddelanden
Meddelanden i SCOM skicka ett e-post eller en text som svar på aviseringar som uppfyller specifika villkor.  Du kan skapa olika meddelandeprenumerationer som har olika personer meddelande beroende på dessa kriterier som det objekt som övervakas, allvarlighetsgrad för aviseringen, vilken typ av problem som identifieras eller tid på dagen.

Några prenumerationer kan användas för att genomföra en fullständig anmälan strategi för ett stort antal hanteringspaket.

![SCOM aviseringar](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Logganalys kan meddela dig via e-post att en avisering har skapats genom att ange en e-postavisering åtgärd på varje [varningsregeln](http://technet.microsoft.com/library/mt614775.aspx).  Det har inte möjligheten för SCOM Abonnera på flera aviseringar med en enskild regel.  Du måste också skapa egna Varningsregler eftersom OMS inte innehåller några fördefinierade.

![Log Analytics-aviseringar](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Du kan inte helt hantera SCOM aviseringar i logganalys men eftersom du kan bara ändra dem i Driftkonsolen.  Logganalys är användbar som en del av en aviseringshanteringen bearbeta om för att tillhandahålla analysverktyg som enbart SCOM saknar.

## <a name="alert-remediation"></a>Aviseringen reparation
[Reparation](http://technet.microsoft.com/library/mt614775.aspx) refererar till ett försök att automatiskt korrigera problemet som identifieras av en avisering.

SCOM kan du köra diagnostik och återställningar som svar på en Övervakare som anger ett ogiltigt tillstånd.  Detta sker samtidigt i övervakaren varningen.  Diagnostik och återställningar implementeras normalt som ett skript som körs på agenten.  En diagnostik försöker samla in mer information om det upptäckta problemet medan en återställning försöker åtgärda problemet.

Log Analytics kan du starta en [Azure Automation-runbook](https://azure.microsoft.com/documentation/services/automation/) eller anropa en webhook som svar på en avisering om logganalys.  Runbooks kan innehålla avancerad logik som implementerats i PowerShell.  Skriptet körs i Azure och kan komma åt Azure-resurser eller externa resurser som är tillgängliga från molnet.  Azure Automation har möjligheten att köra runbooks på en server i ditt lokala datacenter, men den här funktionen är inte tillgänglig när du startar en runbook som svar på logganalys varningar.

Både återställningar i SCOM och runbooks i OMS kan innehålla PowerShell-skript men återställningar är svårare att skapa och hantera eftersom de måste finnas i ett hanteringspaket.  Runbooks som lagras i Azure Automation som innehåller funktioner för redigering, testning och hantering av runbooks.

Om du använder SCOM som datakälla för Log Analytics, kan du skapa en Log Analytics-avisering med en logg-fråga för att hämta SCOM-aviseringar som lagras i OMS-databasen.  Det innebär att du kan köra en Azure Automation-runbook som svar på en SCOM-avisering.  Naturligtvis eftersom runbook körs i Azure, kan detta inte en lönsam strategi för återställningar av lokala problem.

## <a name="next-steps"></a>Nästa steg
* Mer information om [aviseringar i System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).

