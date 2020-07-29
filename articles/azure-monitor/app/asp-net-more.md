---
title: Få ut mer av Azure Application Insights | Microsoft Docs
description: När du har kommit igång med Application Insights här är en sammanfattning av de funktioner som du kan utforska.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: c0be377636159dbb2bdb628af6a0bc9077942397
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321351"
---
# <a name="more-telemetry-from-application-insights"></a>Mer telemetri från Application Insights
När du har [lagt till Application Insights i ASP.net-koden](./asp-net.md)finns det några saker som du kan göra för att få ännu mer telemetri. 

| Åtgärd | Det här får du|
|---|---|
|(IIS-servrar) [Installera statusövervakare](https://go.microsoft.com/fwlink/?LinkId=506648) på varje Server dator.<br/>(Azure-Webbappar) Öppna bladet Application Insights på kontroll panelen i Azure för webb programmet.| [**Prestandaräknare**](./performance-counters.md)<br/>[**Undantag**](asp-net-exceptions.md) – detaljerade stack spår<br/>[**Beroenden**](./asp-net-dependencies.md)|
|[Lägga till JavaScript-kodfragmentet på dina webb sidor](./javascript.md)|[Sid prestanda](./usage-overview.md), webb läsar undantag, Ajax-prestanda. Anpassad telemetri på klient sidan.|
|[Skapa webb test för tillgänglighet](./monitor-web-app-availability.md)|Få aviseringar om webbplatsen blir otillgänglig|
|[Se till att buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) genereras av MSBuild|[Bygg anteckningar i mått diagram](./annotations.md)
|[Skriv anpassade händelser och mått](./api-custom-events-metrics.md)|Räkna affärs händelser och mått, spåra detaljerad användning med mera.|
|[Profilera din Live-webbplats](https://aka.ms/AIProfilerPreview)|Detaljerade funktions tider från din Live Web App|

