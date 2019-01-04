---
title: Utforska HockeyApp-data i Azure Application Insights | Microsoft Docs
description: Analysera användning och prestanda för din Azure-app med Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: 73e1fac5ba9fbb981c92ff8d32064f7d34729ef7
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812512"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Utforska HockeyApp-data i Application Insights

> [!NOTE]
> HockeyApp är inte längre tillgängligt för nya program. Befintliga HockeyApp distributioner fortsätter att fungera. Visual Studio App Center är nu den rekommenderade tjänsten från Microsoft för att övervaka nya mobila appar. [Lär dig hur du ställer in dina appar med App Center och Application Insights](../../application-insights/app-insights-mobile-center-quickstart.md).

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) är en tjänst för att övervaka live stationära och mobila appar. Från HockeyApp kan du skicka anpassade och spåra telemetri för att övervaka användning samt hjälpa till vid diagnos (utöver komma kraschdata). Den här strömmen av telemetri kan efterfrågas med hjälp av det kraftfulla [Analytics](../../azure-monitor/app/analytics.md) funktion i [Azure Application Insights](../../application-insights/app-insights-overview.md). Dessutom kan du [exportera anpassat och spåra telemetri](export-telemetry.md). Om du vill aktivera dessa funktioner måste ställa du in en brygga som vidarebefordrar anpassade HockeyApp-data till Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>HockeyApp Bridge-appen
HockeyApp Bridge-appen är core-funktion som gör att du kan komma åt dina anpassade HockeyApp och spårningstelemetri i Application Insights via funktioner för analys och löpande Export. Anpassade och spåra händelser som samlas in av HockeyApp efter skapandet av HockeyApp Bridge-appen kommer att vara tillgänglig från de här funktionerna. Nu ska vi se hur du ställer in en av dessa Bryggappar.

Öppna i HockeyApp, kontoinställningar, [API-token](https://rink.hockeyapp.net/manage/auth_tokens). Skapa en ny token eller återanvända en befintlig. De minsta nödvändiga rättigheterna är ”skrivskyddad”. Ta en kopia av API-token.

![Hämta ett HockeyApp-API-token](./media/hockeyapp-bridge-app/01.png)

Öppna Microsoft Azure-portalen och [skapar en Application Insights-resurs](../../application-insights/app-insights-create-new-resource.md). Ange programtypen till ”HockeyApp bridge-program”:

![Ny Application Insights-resurs](./media/hockeyapp-bridge-app/02.png)

Du behöver inte ange ett namn – detta ställs automatiskt från HockeyApp-namn.

HockeyApp bridge-fält som visas. 

![Ange brygga fält](./media/hockeyapp-bridge-app/03.png)

Ange HockeyApp-token som du antecknade tidigare. Den här åtgärden fylls listrutan ”HockeyApp-program” med alla dina HockeyApp-program. Väljer du det alternativ som du vill använda och Slutför resten av fälten. 

Öppna den nya resursen. 

Observera att data tar en stund att börjar flöda.

![Application Insights-resurs som väntar på data](./media/hockeyapp-bridge-app/04.png)

Klart! Anpassade och spåra data som samlats in i din app för HockeyApp-instrumenterats från den här tidpunkten och framåt är nu även tillgängliga i funktionerna för Analytics och löpande Export i Application Insights.

Nu ska vi gå igenom de olika funktionerna är nu tillgängliga för dig.

## <a name="analytics"></a>Analytics
Analytics är ett kraftfullt verktyg för ad hoc-frågor över dina data, så att du kan diagnostisera och analysera din telemetri och snabbt identifiera rotorsaker och mönster.

![Analytics](./media/hockeyapp-bridge-app/05.png)

* [Läs mer om Analytics](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Löpande export
Löpande Export kan du exportera data till en Azure Blob Storage-behållare. Detta är användbart om du behöver att förvara dina data under längre tid än kvarhållningsperioden för närvarande erbjuds av Application Insights. Du kan behålla data i blob storage, bearbeta dem i en SQL-databas eller din önskade datalagerlösning.

[Mer information om löpande Export](export-telemetry.md)

## <a name="next-steps"></a>Nästa steg
* [Använda Analytics på data](../../azure-monitor/log-query/get-started-portal.md)

