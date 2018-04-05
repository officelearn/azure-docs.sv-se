---
title: Övervakning av användning och de uppskattade kostnaderna i Azure-Monitor | Microsoft Docs
description: Översikt över processen med att använda Azure-Monitor användning och uppskattade kostnaderna sida
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: f87705ebbdd14b1bbf7cade481a7dbe7dd3d5131
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Övervakning av användning och de uppskattade kostnaderna

I övervakaren hubb för Azure-portalen på **användning och de uppskattade kostnaderna** sidor är utformad för att förstå användning från grundläggande övervakningsfunktioner som [avisering, mått, meddelanden](https://azure.microsoft.com/pricing/details/monitor/), [Logga Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) och [Programinsikter](https://azure.microsoft.com/pricing/details/application-insights/). Detta omfattar även logganalys användning som köpts via insikter för kunder på alla tillgängliga innan April 2018 prisnivå planer och Analytics erbjuder.

På den här sidan kan användare visa deras användning av dessa resurser för de senaste 31 dagarna samman per prenumeration med ökad-moduler för att visa användningsinformation trenden under denna tid. Det finns en mängd data som behövs för samman och göra den här uppskattning så att du ha tålamod när sidan läses in.
Här är ett exempel på användning av övervakning och en uppskattning av kostnaderna för resulterande:

![Användnings- och uppskattade kostnaderna portal skärmbild](./media/monitoring-usage-and-estimated-costs/001.png)

Klicka på länken i kolumnen månatlig användning öppnas ett diagram som visar användningstrender under de senaste 31 dagarna:

![Ingår per nod skärmbild 671.47 GB](./media/monitoring-usage-and-estimated-costs/002.png)

Här är en annan liknande användning och kostnad sammanfattning i det här fallet för en prenumeration i nya April 2018 förbrukningsbaserad priserna. Observera bristen på någon nod-baserade fakturering och att datapåfyllning och lagring för Log Analytics och Application Insights rapporteras på en ny vanliga mätaren.

![Användnings- och uppskattade kostnaderna portal skärmbild](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Ny prissättningsmodell

I April 2018 släpptes en ny övervakning prissättningsmodell.  Detta funktioner molnet eget, förbrukningsbaserad priser. Du betalar bara för det du använder, utan åtaganden baserat på noden. Information om nya priserna är tillgängliga för [avisering, mått, meddelanden](https://azure.microsoft.com/pricing/details/monitor/), [logganalys](https://azure.microsoft.com/pricing/details/log-analytics/) och [Programinsikter](https://azure.microsoft.com/pricing/details/application-insights/).

För kunder att logganalys eller Application Insights efter den 2 April 2018 är nya priserna det enda alternativet. För kunder som redan har använt tjänsterna flyttar till nya priserna är valfritt.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Utvärdera effekten av nya priserna

Ny prissättningsmodell har olika konsekvenser för varje kund baserat på deras övervakning användningsmönster. För kunder som redan använder logganalys eller Application Insights innan April 2 2018, den **användnings- och uppskattade kostnaden** i Azure-Monitor ger ett sätt att beräkna ändringar i kostnaderna om du flyttar till en ny prissättningsmodell och ger den sätt att flytta en prenumeration till den nya modellen. Ny prissättningsmodell blir fördelaktigt för de flesta kunder, men för kunder med särskilt stor data användningsmönster eller högre kostnad regioner, kan detta inte är fallet.

Få en uppskattning av kostnaderna för prenumerationerna som du har valt på den **användning och de uppskattade kostnaderna** klickar du på den blå banderollen längst upp på sidan. Det är bäst att göra detta en prenumeration samtidigt, eftersom nivån då nya priserna kan antas.

![Priser för modellen väljer skärmbild](./media/monitoring-usage-and-estimated-costs/004.png)

Nu visas en liknande version av den här sidan med en grön banderoll:

![Priser för modellen väljer skärmbild](./media/monitoring-usage-and-estimated-costs/005.png)

Här visas en annan uppsättning mätare – mätare som motsvarar nya priserna. Till exempel data införandet mätare som

1. Insikt och Analytics\Overage per nod
2. Insikt och Analytics\Included per nod
3. Överförbrukning programdata för Insights\Basic
4. Application Insights\Included Data

kombineras till en ny gemensamma data införandet mätare kallas **delade Services\Data införandet** eftersom nya priserna saknar nod-baserade inkluderade data allokeringar.

En annan ändring som visas är den data som inhämtas i logganalys eller Application Insights i områden med högre kostnader visas med de nya nationella mätare korrekt efter detta, exempelvis **”Datapåfyllning (USA, västra centrala)**.

> [!NOTE]
> Om du har en Operations Management Suite (OMS)-prenumeration får du logganalys och Application Insights data införandet allokeringar för varje nod som har köpt. Eftersom detta tillämpas på kontonivå (inte på prenumerationen) kan inte denna uppskattning visa effekten av dessa allokeringar. Kontakta din kontorepresentant för en mer detaljerad beskrivning av nya priserna i det här fallet.

## <a name="changes-when-moving-to-the-new-pricing-model"></a>Ändringar när du flyttar till en ny prissättningsmodell

Flytta en prenumeration till nya priserna ändrar prisnivån för varje logganalys till en ny nivå Per GB och flyttas alla (kallas ”pergb2018” i Azure Resource Manager). Flyttningen påverkar även Application Insights-resurser i Enterprise-plan för den grundläggande planen. Effekterna av att de visas på den uppskattning beskrivs ovan. 

## <a name="moving-to-the-new-pricing-model"></a>Flytta till ny prissättningsmodell

Om du har bestämt att införa nya priserna för en prenumeration, klickar du på den **priser vald modell** alternativet överst i den **användning och de uppskattade kostnaderna** sidan:

![Övervaka användningen en uppskattade kostnaderna i nya prisnivå modellen skärmbild](./media/monitoring-usage-and-estimated-costs/006.png)

Öppnas den **priser vald modell** sidan visar en lista över alla prenumerationer som du tittade på tidigare sidan:

![Priser för modellen val av skärmbild](./media/monitoring-usage-and-estimated-costs/007.png)

Om du vill flytta en prenumeration till ny prissättningsmodell du bara markera kryssrutan och klicka på **spara**.  Du kan flytta tillbaka till äldre priserna på samma sätt. Kom ihåg att prenumerationen ägare eller deltagare behörigheter som krävs för att ändra priserna.