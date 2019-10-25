---
title: Utforska HockeyApp-data i Azure Application Insights | Microsoft Docs
description: Analysera användning och prestanda för din Azure-app med Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/30/2017
ms.openlocfilehash: b14cd38a1db6804a00883ded0b38511fa46c3a52
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819578"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Utforska HockeyApp-data i Application Insights

> [!NOTE]
> HockeyApp är inte längre tillgängligt för nya program. Befintliga HockeyApp-distributioner kommer att fortsätta att fungera. Visual Studio App Center är nu den rekommenderade tjänsten från Microsoft för att övervaka nya mobila appar. [Lär dig hur du konfigurerar dina appar med App Center och Application Insights](../../azure-monitor/learn/mobile-center-quickstart.md).

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) är en tjänst för övervakning av Live Desktop-och Mobile-appar. Från HockeyApp kan du skicka anpassad och spåra telemetri för att övervaka användning och under lätta diagnostiseringen (förutom att hämta krasch data). Den här strömmen av telemetri kan frågas med hjälp av den kraftfulla [analys](../../azure-monitor/app/analytics.md) funktionen i [Azure Application insikter](../../azure-monitor/app/app-insights-overview.md). Dessutom kan du [Exportera anpassad och spåra telemetri](export-telemetry.md). Om du vill aktivera dessa funktioner konfigurerar du en brygga som vidarebefordrar HockeyApp anpassade data till Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>HockeyApp Bridge-appen
HockeyApp Bridge-appen är Core-funktionen som gör att du kan komma åt din HockeyApp anpassade och spåra telemetri i Application Insights via funktionerna för analys och kontinuerlig export. Anpassade händelser och spårnings händelser som samlats in av HockeyApp efter att HockeyApp Bridge-appen har skapats kan nås från dessa funktioner. Nu ska vi se hur du konfigurerar en av dessa molnappar.

I HockeyApp öppnar du konto inställningar, [API-token](https://rink.hockeyapp.net/manage/auth_tokens). Skapa antingen en ny token eller Återanvänd en befintlig. De minsta rättigheterna som krävs är "skrivskyddade". Ta en kopia av API-token.

![Hämta en HockeyApp-API-token](./media/hockeyapp-bridge-app/01.png)

Öppna Microsoft Azure-portalen och [skapa en Application Insights resurs](../../azure-monitor/app/create-new-resource.md ). Ange program typ till "HockeyApp Bridge Application":

![Ny Application Insights resurs](./media/hockeyapp-bridge-app/02.png)

Du behöver inte ange ett namn – detta ställs automatiskt in från HockeyApp namn.

HockeyApp Bridge-fälten visas. 

![Ange bro fält](./media/hockeyapp-bridge-app/03.png)

Ange HockeyApp-token som du noterade tidigare. Den här åtgärden fyller i list rutan "HockeyApp program" med alla dina HockeyApp-program. Välj den som du vill använda och Slutför resten av fälten. 

Öppna den nya resursen. 

Observera att det tar en stund innan data börjar flöda.

![Application Insights resurs väntar på data](./media/hockeyapp-bridge-app/04.png)

Klart! Anpassade och spårnings data som samlas in i din HockeyApp-instrumenterade app från den här punkten är nu även tillgängliga i analys-och kontinuerliga export funktionerna i Application Insights.

Vi går kort igenom var och en av de här funktionerna nu tillgängliga.

## <a name="analytics"></a>Analyser
Analytics är ett kraftfullt verktyg för ad hoc-frågor om dina data, så att du kan diagnostisera och analysera din telemetri och snabbt identifiera Rotors Aker och mönster.

![Analyser](./media/hockeyapp-bridge-app/05.png)

* [Lär dig mer om Analytics](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Kontinuerlig export
Med kontinuerlig export kan du exportera data till en Azure Blob Storage-behållare. Detta är mycket användbart om du behöver behålla dina data längre än den kvarhållningsperiod som för närvarande erbjuds av Application Insights. Du kan behålla data i blob-lagring, bearbeta dem i ett SQL Database eller din önskade data lager lösning.

[Läs mer om kontinuerlig export](export-telemetry.md)

## <a name="next-steps"></a>Nästa steg
* [Använda analyser för dina data](../../azure-monitor/log-query/get-started-portal.md)

