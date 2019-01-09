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
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 5daf0944212dc4b8040a39e6efbf5bb25f7f39f0
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117239"
---
# <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs
Azure Application Insights visar data om ditt program i en Microsoft Azure *resource*. Skapa en ny resurs är därför en del av [ställa in Application Insights för att övervaka ett nytt program][start]. I många fall kan skapar en resurs göras automatiskt av IDE. Men i vissa fall kan du skapa en resurs manuellt – till exempel om du vill ha separata resurser för utveckling och produktion build-versioner av ditt program.

När du har skapat resursen kan du hämta dess instrumenteringsnyckeln och använda den för att konfigurera SDK i programmet. Resursnyckeln länkar telemetri till resursen.

## <a name="sign-up-to-microsoft-azure"></a>Logga på Microsoft Azure
Om du inte har en [Microsoft-konto, skaffa ett nu](https://live.com). (Om du använder tjänster som Outlook.com, OneDrive, Windows Phone och XBox Live är du redan har ett Microsoft-konto.)

Du behöver också en prenumeration på [Microsoft Azure](https://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig, med ditt Windows Live ID. Du debiteras bara för det du använder. Standard basic-avtalet kan en viss mängd experimentella Använd kostnadsfritt.

När du har åtkomst till en prenumeration kan du logga in till Application Insights på [ https://portal.azure.com ](https://portal.azure.com), och använda en Live-ID för inloggning.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs
I den [portal.azure.com](https://portal.azure.com), lägga till en Application Insights-resurs:

![Klicka på Nytt, Application Insights](./media/create-new-resource/01-new.png)

* **Programtyp** påverkar vad som visas på översiktsbladet och egenskaper som är tillgängliga i [metric explorer][metrics]. Om du inte ser din typ av app, väljer du allmänna.
* **Prenumeration** är ditt betalningskonto i Azure.
* **Resursgrupp** är att underlätta för att hantera egenskaper som åtkomstkontroll. Om du redan har skapat andra Azure-resurser kan du placera den här nya resursen i samma grupp.
* **Plats** är där vi sparar dina data.
* **Fäst på instrumentpanelen** placerar en panel för snabb åtkomst för din resurs på startsidan för Azure. Vi rekommenderar.

När din app har skapats, öppnas ett nytt blad. Det här bladet visas prestanda- och användningsdata om din app. 

Gå tillbaka till den nästa gång du loggar in på Azure genom att leta efter appens Snabbstart panelen på start-kort (startsidan). Eller klicka på Bläddra för att hitta den.

## <a name="copy-the-instrumentation-key"></a>Kopiera instrumenteringsnyckeln
Instrumenteringsnyckeln som identifierar den resurs som du skapade. Du behöver den för att ge till SDK: N.

![Klicka på Essentials, Instrumenteringsnyckeln, CTRL + C](./media/create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Installera SDK i din app
Installera Application Insights SDK i din app. Det här steget beror mycket på vilken typ av ditt program. 

Använd instrumentationsnyckeln för att konfigurera [SDK: N som du installerar i ditt program][start].

SDK innehåller standardmoduler som skickar telemetri utan att du behöver skriva någon kod. Att spåra användaråtgärder och diagnostisera problem i detalj, [använda API: et] [ api] att skicka din egen telemetri.

## <a name="monitor"></a>Se dessa data
Stäng bladet Snabbstart för att gå tillbaka till ditt programblad i Azure-portalen.

Klicka på panelen Sök för att se [Diagnostiksökning][diagnostic], var de första händelserna visas. 

Om du väntade dig mer data, klickar du på **uppdatera** efter några sekunder.

## <a name="creating-a-resource-automatically"></a>Skapa en resurs automatiskt
Du kan skriva en [PowerShell-skript](../../azure-monitor/app/powershell.md) att skapa en resurs automatiskt.

## <a name="next-steps"></a>Nästa steg
* [Skapa en instrumentpanel](../../azure-monitor/app/app-insights-dashboards.md)
* [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)
* [Utforska mått](../../azure-monitor/app/metrics-explorer.md)
* [Skriv analysfrågor](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md

