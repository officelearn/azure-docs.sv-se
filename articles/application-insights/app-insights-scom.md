---
title: SCOM-integrering med Application Insights | Microsoft Docs
description: "Om du använder en SCOM, övervaka prestanda och diagnostisera problem med Application Insights. Omfattande instrumentpaneler, smart aviseringar, kraftfulla verktyg för Nätverksdiagnostik och analys frågor."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/12/2016
ms.author: mbullwin
ms.openlocfilehash: 35ea37b751909e14e616a965462b832e4e51bae0
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>Prestandaövervakning av program med Application Insights för SCOM
Om du använder System Center Operations Manager (SCOM) för att hantera dina servrar, kan du övervaka prestanda och diagnostisera prestandaproblem med hjälp av [Azure Application Insights](app-insights-asp-net.md). Application Insights övervakar ditt webbprogram inkommande begäranden, utgående REST och SQL-anrop, undantag och loggspårningar. Det ger instrumentpaneler med mått diagram och smarta aviseringar, samt kraftfulla diagnostiska Sök och analytiska frågor via den här telemetri. 

Du kan växla på Application Insights-övervakning med hjälp av ett hanteringspaket för SCOM.

## <a name="before-you-start"></a>Innan du börjar
Vi förutsätter:

* Du är bekant med SCOM och att du använder SCOM 2012 R2 eller 2016 för att hantera din IIS-webbservrar.
* Du har redan installerat på dina servrar för ett webbprogram som du vill övervaka med Application Insights.
* Appen framework-version är .NET 4.5 eller senare.
* Du har åtkomst till en prenumeration i [Microsoft Azure](https://azure.com) och kan logga in på den [Azure-portalen](https://portal.azure.com). Din organisation kan ha en prenumeration och kan lägga till ditt Microsoft-konto i den.

(Utvecklingsteamet kan skapa den [Application Insights SDK](app-insights-asp-net.md) till webbappen. Den här byggning instrumentation ger dem större flexibilitet vid skrivning anpassad telemetri. Men det spelar ingen roll: du kan följa stegen som beskrivs här med eller utan SDK inbyggda.)

## <a name="one-time-install-application-insights-management-pack"></a>(En gång) Installera Application Insights management pack
På den dator där du kör Operations Manager:

1. Avinstallera en tidigare version av management pack:
   1. Öppna Administration Management Packs i Operations Manager. 
   2. Ta bort den gamla versionen.
2. Hämta och installera det management pack-katalogen.
3. Starta om Operations Manager.

## <a name="create-a-management-pack"></a>Skapa ett hanteringspaket
1. Öppna i Operations Manager, **redigering**, **.NET... med Application Insights**, **guiden Lägg till övervakning**, och välj igen **.NET med program... Insikter**.
   
    ![](./media/app-insights-scom/020.png)
2. Namnet konfigurationen efter din app. (Du har instrumentera en app åt gången.)
   
    ![](./media/app-insights-scom/030.png)
3. På samma sida i guiden Skapa ett nytt hanteringspaket eller välj ett paket som du tidigare skapade för Application Insights.
   
     (Application Insights [hanteringspaket](https://technet.microsoft.com/library/cc974491.aspx) är en mall som du skapar en instans. Du kan återanvända samma instans senare.)

    ![Skriv namnet på appen i fliken allmänna egenskaper. Klicka på nytt och ange ett namn för ett management pack. Klicka på OK och klicka på Nästa.](./media/app-insights-scom/040.png)

1. Välj en app som du vill övervaka. Sökfunktionen söker bland appar som är installerade på servrarna.
   
    ![På vad ska övervakas, klicka på Lägg till, skriver du en del av namnet på appen, klicka på Sök, Välj appen och Lägg till, OK.](./media/app-insights-scom/050.png)
   
    Fritextfält för omfånget för övervakning kan användas för att ange en delmängd av dina servrar, om du inte vill att övervaka app på alla servrar.
2. Du måste ange dina autentiseringsuppgifter för att logga in på Microsoft Azure på nästa sida i guiden.
   
    På den här sidan väljer du önskad telemetridata som visas och analyseras Application Insights-resursen. 
   
   * Om programmet har konfigurerats för Application Insights under utvecklingen, väljer du den befintliga resursen.
   * Annars skapar du en ny resurs med namnet för appen. Om det finns andra program som ingår i samma system kan du placera dem i samma resursgrupp, så att åtkomsten till telemetrin lättare att hantera.
     
     Du kan ändra inställningarna senare.
     
     ![På fliken för Application Insights-inställningar, klicka på Logga in och ange autentiseringsuppgifterna för ditt Microsoft-konto för Azure. Välj en prenumeration, resursgrupp och resurs.](./media/app-insights-scom/060.png)
3. Slutför guiden.
   
    ![Klicka på Skapa](./media/app-insights-scom/070.png)

Upprepa proceduren för varje app som du vill övervaka.

Om du behöver ändra inställningarna senare, öppna egenskaperna för övervakaren från fönstret Redigering igen.

![I redigering, Välj .NET Application Performance Monitoring med Application Insights, Välj din Övervakare och klickar på Egenskaper.](./media/app-insights-scom/080.png)

## <a name="verify-monitoring"></a>Kontrollera övervakning
Övervakaren är att du har installerat sökningar för din app på varje server. Om den hittar appen, konfigurerar den Application Insights Status Monitor för att övervaka appen. Om det behövs installerar först Status Monitor på servern.

Du kan kontrollera vilka instanser av appen har identifierats:

![Öppna i övervakning, Programinsikter](./media/app-insights-scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Visa telemetri i Application Insights
I den [Azure-portalen](https://portal.azure.com), bläddra till resursen för din app. Du [Visa diagram som visar telemetri](app-insights-dashboards.md) från din app. (Om den inte visas på huvudsidan ännu, klickar du på direktsänd dataström med mått.)

## <a name="next-steps"></a>Nästa steg
* [Ställ in en instrumentpanel](app-insights-dashboards.md) sammanföra viktigaste diagrammen övervakning detta och andra appar.
* [Lär dig mer om mått](app-insights-metrics-explorer.md)
* [Konfigurera aviseringar](app-insights-alerts.md)
* [Diagnostisera prestandaproblem](app-insights-detect-triage-diagnose.md)
* [Kraftfulla Analytics-frågor](app-insights-analytics.md)
* [Tillgänglighetstester för webbprogram](app-insights-monitor-web-app-availability.md)

