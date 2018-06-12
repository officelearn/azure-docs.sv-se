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
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 59bb8564613e9a0cebda00c2c847283ff218b882
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294725"
---
# <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs
Azure Application Insights visar data om ditt program i en Microsoft Azure *resurs*. Skapa en ny resurs är därför en del av [ställa in Application Insights för att övervaka ett nytt program][start]. I många fall kan att skapa en resurs göras automatiskt med IDE. Men i vissa fall kan du skapa en resurs manuellt – till exempel om du vill ha separata resurser för utveckling och produktion versioner av programmet.

När du har skapat resursen du hämta dess instrumentation nyckel och använda den för att konfigurera SDK i programmet. Resursnyckeln länkar telemetrin till resursen.

## <a name="sign-up-to-microsoft-azure"></a>Registrera dig för Microsoft Azure
Om du inte har en [Microsoft konto måste du skaffa ett nu](http://live.com). (Om du använder tjänster som Outlook.com, OneDrive, Windows Phone och XBox Live du redan har ett Microsoft-konto.)

Du måste också en prenumeration på [Microsoft Azure](http://azure.com). Om din grupp eller organisation har en Azure-prenumeration, kan ägare lägger du till, med ditt Windows Live ID. Du kan endast debiteras du använder. Grundläggande standardplanen kan under en viss experiment används utan kostnad.

När du har åtkomst till en prenumeration kan logga in till Application Insights på [ http://portal.azure.com ](https://portal.azure.com), och använda ditt Live ID för inloggning.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs
I den [portal.azure.com](https://portal.azure.com), Lägg till Application Insights-resurs:

![Klicka på Nytt, Application Insights](./media/app-insights-create-new-resource/01-new.png)

* **Programtyp** påverkar vad som visas på bladet översikt och egenskaper som är tillgängliga i [mått explorer][metrics]. Välj Allmänt om du inte ser typen av app.
* **Prenumerationen** är din betalningskonto i Azure.
* **Resursgruppen** är för att hantera egenskaper i syfte att underlätta som åtkomstkontroll. Om du redan har skapat andra Azure-resurser kan du placera den här nya resursen i samma grupp.
* **Plats** är där vi behåller dina data.
* **Fäst på instrumentpanelen** placerar en snabb åtkomst-panelen för din resurs på Azure-startsidan. Rekommenderas.

När appen har skapats, öppnas ett nytt blad. Det här bladet är där du ser prestanda- och användningsdata om din app. 

Gå tillbaka till den nästa gång du loggar in till Azure genom att leta efter din app Snabbkurs panelen på start-kort (startsidan). Eller klicka på Bläddra för att hitta den.

## <a name="copy-the-instrumentation-key"></a>Kopiera nyckeln instrumentation
Nyckeln instrumentation identifierar resursen som du skapade. Du behöver det att ge till SDK.

![Klicka på Essentials, nyckeln Instrumentation CTRL + C](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Installera SDK i din app
Installera Application Insights SDK i din app. Det här steget beror kraftigt på vilken typ av ditt program. 

Använd instrumentation för att konfigurera [SDK som du installerar programmet][start].

SDK innehåller standard moduler som skickar telemetri utan att du behöver skriva någon kod. Spåra användaråtgärder eller diagnostisera problem i detalj, [-API: n] [ api] att skicka telemetri om dina egna.

## <a name="monitor"></a>Se telemetridata
Stäng bladet Snabbstart för att återgå till ditt program-blad i Azure-portalen.

Klicka på Sök-rutan för att se [diagnostiska Sök][diagnostic], där första händelserna som visas. 

Om du väntar mer data, klickar du på **uppdatera** efter några sekunder.

## <a name="creating-a-resource-automatically"></a>Skapa en resurs automatiskt
Du kan skriva en [PowerShell-skript](app-insights-powershell.md) att automatiskt skapa en resurs.

## <a name="next-steps"></a>Nästa steg
* [Skapa en instrumentpanel](app-insights-dashboards.md)
* [Diagnostiksökning](app-insights-diagnostic-search.md)
* [Utforska mått](app-insights-metrics-explorer.md)
* [Skriv analysfrågor](app-insights-analytics.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

