---
title: Skapa en ny Azure Application Insights-resurs | Microsoft Docs
description: Manuellt konfigurera Application Insights-övervakning för ett nytt live program.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073312"
---
# <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

Azure Application Insights visar data om ditt program i en Microsoft Azure *resource*. Skapa en ny resurs är därför en del av [ställa in Application Insights för att övervaka ett nytt program][start]. När du har skapat din nya resurs kan du hämta dess instrumenteringsnyckeln och använda den för att konfigurera Application Insights SDK. Instrumenteringsnyckeln länkar din telemetri till resursen.

## <a name="sign-in-to-microsoft-azure"></a>Logga in på Microsoft Azure

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

Logga in på den [Azure-portalen](https://portal.azure.com), och skapa en Application Insights-resurs:

![Klicka på tecknet ”+” i det övre vänstra hörnet. Välj utvecklingsverktyg följt av Application Insights](./media/create-new-resource/new-app-insights.png)

   | Inställningar        |  Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar. |
   | **Resursgrupp**     | myResourceGroup      | Namn för ny eller befintlig resursgrupp som värd för App Insights data. |
   | **Plats** | Östra USA | Välj en plats nära dig eller nära där appen finns. |

Ange lämpliga värden i de obligatoriska fälten och välj sedan **granska + skapa**.

![Ange värden i obligatoriska fält och välj sedan ”granska + skapa”.](./media/create-new-resource/review-create.png)

När din app har skapats, öppnas ett nytt fönster. Det här fönstret är där du se prestanda- och användningsdata om dina övervakade programmet. 

## <a name="copy-the-instrumentation-key"></a>Kopiera instrumenteringsnyckeln

Instrumenteringsnyckeln som identifierar den resurs som du vill associera med telemetridata. Du måste kopiera att lägga till instrumentationsnyckeln i programkoden.

![Klicka på och kopierar instrumenteringsnyckeln.](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Installera SDK i din app

Installera Application Insights SDK i din app. Det här steget beror mycket på vilken typ av ditt program.

Använd instrumentationsnyckeln för att konfigurera [SDK: N som du installerar i ditt program][start].

SDK innehåller standardmoduler som skickar telemetri utan att du behöver skriva någon ytterligare kod. Att spåra användaråtgärder och diagnostisera problem i detalj, [använda API: et] [ api] att skicka din egen telemetri.

## <a name="creating-a-resource-automatically"></a>Skapa en resurs automatiskt
Du kan skriva en [PowerShell-skript](../../azure-monitor/app/powershell.md) att skapa en resurs automatiskt.

## <a name="next-steps"></a>Nästa steg
* [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)
* [Utforska mått](../../azure-monitor/app/metrics-explorer.md)
* [Skriv analysfrågor](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md