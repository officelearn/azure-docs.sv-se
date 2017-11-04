---
title: Utforska HockeyApp data i Azure Application Insights | Microsoft Docs
description: "Analysera användnings- och prestanda för din Azure-app med Application Insights."
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: a925241d10b068e377fa9a11fc854db34c808343
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Utforska HockeyApp data i Application Insights

> [!NOTE]
> Visual Studio Mobile Center är nu den rekommenderade tjänsten från Microsoft för att övervaka ny mobila appar. [Lär dig hur du ställer in dina appar via Mobile Center och Application Insights](app-insights-mobile-center-quickstart.md).
> 
> 

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) är en tjänst för övervakning av live stationära och mobila appar. Du kan skicka anpassade från HockeyApp och spåra telemetri för att övervaka användningen och hjälpa diagnostisera (förutom komma kraschdata). Dataströmmen telemetri kan efterfrågas med hjälp av den kraftfulla [Analytics](app-insights-analytics.md) funktion i [Azure Application Insights](app-insights-overview.md). Dessutom kan du [exportera ett anpassat och spåra telemetri](app-insights-export-telemetry.md). Om du vill aktivera de här funktionerna kan du ställa in en brygga som vidarebefordrar HockeyApp anpassade data till Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>HockeyApp Bridge appen
HockeyApp Bridge appen är core-funktion som gör att du kan komma åt dina anpassade HockeyApp och spåra telemetri i Application Insights via analyser och löpande Export funktioner. Anpassad och spåra händelser som samlas in av HockeyApp efter skapandet av HockeyApp Bridge appen är tillgänglig från dessa funktioner. Låt oss se hur du ställer in en av apparna Bridge.

Öppna kontoinställningar, i HockeyApp, [API token](https://rink.hockeyapp.net/manage/auth_tokens). Skapa en ny token eller återanvända en befintlig. Den minimala behörigheten som krävs är ”skrivskyddade”. Ta en kopia av API-token.

![Hämta ett HockeyApp API-token](./media/app-insights-hockeyapp-bridge-app/01.png)

Öppna Microsoft Azure-portalen och [skapa Application Insights-resurs](app-insights-create-new-resource.md). Ange typ av program ”HockeyApp bridge program”:

![Ny Application Insights-resurs](./media/app-insights-hockeyapp-bridge-app/02.png)

Du behöver inte ange ett namn - detta in automatiskt från HockeyApp namn.

Fälten HockeyApp bridge visas. 

![Ange bridge fält](./media/app-insights-hockeyapp-bridge-app/03.png)

Ange HockeyApp-token som du antecknade tidigare. Den här åtgärden fyller listrutan ”HockeyApp-program” med alla HockeyApp-program. Väljer du det alternativ som du vill använda och Slutför resten av fälten. 

Öppna den nya resursen. 

Observera att data tar en stund att börjar flöda.

![Application Insights-resurs som väntar på data](./media/app-insights-hockeyapp-bridge-app/04.png)

Klart! Anpassade och spåra data som samlas in i appen HockeyApp instrumenterats från och med nu är nu också tillgängliga i funktionerna för analys och löpande Export av Application Insights.

Nu ska vi gå igenom de olika funktionerna är nu tillgängliga för dig.

## <a name="analytics"></a>Analys
Analytics är ett kraftfullt verktyg för ad hoc-frågor till dina data så att du kan felsöka och analysera dina telemetri och snabbt identifiera rotorsaker och mönster.

![Analys](./media/app-insights-hockeyapp-bridge-app/05.png)

* [Mer information om Analytics](app-insights-analytics-tour.md)

## <a name="continuous-export"></a>Löpande export
Löpande Export kan du exportera dina data till en Azure Blob Storage-behållare. Detta är användbart om du vill bevara data längre än kvarhållningsperioden för närvarande erbjuds av Application Insights. Du kan behålla data i blob storage kan bearbeta till en SQL-databas eller din önskade datalagerlösning.

[Mer information om löpande Export](app-insights-export-telemetry.md)

## <a name="next-steps"></a>Nästa steg
* [Tillämpa Analytics till dina data](app-insights-analytics-tour.md)

