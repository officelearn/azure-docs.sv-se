---
title: SCOM-integration med Application Insights | Microsoft Docs
description: Om du är en SCOM-användare kan övervaka prestanda och diagnostisera problem med Application Insights. Omfattande instrumentpaneler, smart aviseringar, kraftfulla diagnosverktyg och analys av frågor.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mbullwin
ms.openlocfilehash: d7c3134c36707fcfa079968d19d93b73d7a5f5cc
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724085"
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>Prestandaövervakning av program med Application Insights för SCOM
Om du använder System Center Operations Manager (SCOM) för att hantera dina servrar kan du övervaka prestanda och diagnostisera prestandaproblem med hjälp av [Azure Application Insights](app-insights-asp-net.md). Application Insights övervakar ditt webbprogram inkommande begäranden, utgående REST och SQL-anrop, undantag och loggspårningar. Den tillhandahåller instrumentpaneler med diagram med mätvärden och smart aviseringar, samt kraftfulla diagnostiksökning och analytiska frågor över den här telemetrin. 

Du kan växla på Application Insights-övervakning med hjälp av en SCOM-hanteringspaketet.

> [!IMPORTANT]
> Den här System Center Operations Manager-hanteringspaket är nu **inaktuell**. Den har inte stöd för senaste Application Insights SDK: erna och längre rekommenderas inte.

## <a name="before-you-start"></a>Innan du börjar
Vi förutsätter:

* Du är bekant med SCOM och att du använder SCOM 2012 R2 eller 2016 för att hantera din IIS-webbservrar.
* Du har redan installerat på dina servrar ett webbprogram som du vill övervaka med Application Insights.
* App framework-version är .NET 4.5 eller senare.
* Du har åtkomst till en prenumeration i [Microsoft Azure](https://azure.com) och kan logga in på den [Azure-portalen](https://portal.azure.com). Din organisation kan ha en prenumeration och kan lägga till ditt Microsoft-konto till den.

(Utvecklingsteamet kan bygga den [Application Insights SDK](app-insights-asp-net.md) till webbappen. Den här byggning instrumentation ger dem större flexibilitet i att skriva anpassad telemetri. Men det spelar ingen roll: du kan följa stegen som beskrivs här med eller utan SDK: N inbyggd.)

## <a name="one-time-install-application-insights-management-pack"></a>(En gång) Installera Application Insights management pack
På den dator där du kör Operations Manager:

1. Avinstallera en tidigare version av management pack:
   1. Öppna Administration, hanteringspaket i Operations Manager. 
   2. Ta bort den gamla versionen.
2. Hämta och installera hanteringspaketen från katalogen.
3. Starta om Operations Manager.

## <a name="create-a-management-pack"></a>Skapa ett hanteringspaket
1. Öppna i Operations Manager **redigering**, **.NET... med Application Insights**, **guiden Lägg till övervakning**, och välj igen **.NET... med program Insights**.
   
    ![](./media/app-insights-scom/020.png)
2. Namn på konfigurationen när du har din app. (Du har kan instrumentera en app i taget.)
   
    ![](./media/app-insights-scom/030.png)
3. Skapa ett nytt management pack på samma sida i guiden, eller välj ett paket som du tidigare skapade för Application Insights.
   
     (Application Insights [hanteringspaket](https://technet.microsoft.com/library/cc974491.aspx) är en mall som du skapar en instans. Du kan återanvända samma instans senare.)

    ![På fliken allmänna egenskaper skriver du namnet på appen. Klicka på nytt och ange ett namn för ett hanteringspaket. Klicka på OK och klicka på Nästa.](./media/app-insights-scom/040.png)

1. Välj en app som du vill övervaka. Funktionen search söker bland appar som är installerade på servrarna.
   
    ![På fliken vad ska övervakas, klicka på Lägg till, skriver du en del av namnet på, klicka på Sök, Välj appen och Lägg till OK.](./media/app-insights-scom/050.png)
   
    Fritextfält för omfånget för övervakning kan användas för att ange en delmängd av dina servrar, om du inte vill att övervaka appen i alla servrar.
2. På nästa sida i guiden, måste du ange dina autentiseringsuppgifter för att logga in på Microsoft Azure.
   
    Välj Application Insights-resurs där du vill att dessa data som ska analyseras och visas på den här sidan. 
   
   * Om programmet har konfigurerats för Application Insights under utvecklingen, Välj dess befintlig resurs.
   * Annars skapa en ny resurs med namnet för appen. Om det finns andra appar som ingår i samma system kan du placera dem i samma resursgrupp, se åtkomsten till telemetrin som är enklare att hantera.
     
     Du kan ändra inställningarna senare.
     
     ![På fliken för Application Insights-inställningar, klicka på ”Logga in” och ange autentiseringsuppgifterna för ditt Microsoft-konto för Azure. Välj en prenumeration, resursgrupp och resursen.](./media/app-insights-scom/060.png)
3. Slutför guiden.
   
    ![Klicka på Skapa](./media/app-insights-scom/070.png)

Upprepa proceduren för varje app som du vill övervaka.

Om du vill ändra inställningarna senare, ska du öppna egenskaperna för övervakaren från fönstret redigering.

![I redigering, Välj .NET Application Performance Monitoring med Application Insights, Välj din Övervakare och klicka på Egenskaper.](./media/app-insights-scom/080.png)

## <a name="verify-monitoring"></a>Kontrollera övervakar
Övervakaren är att du har installerat sökningar för din app på varje server. Om verktyget hittar appen, konfigurerar den Application Insights Status Monitor och övervaka appen. Om det behövs installerar först Status Monitor på servern.

Du kan kontrollera vilka av instanser av appen har identifierats:

![Öppna Application Insights i övervakning](./media/app-insights-scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Visa telemetri i Application Insights
I den [Azure-portalen](https://portal.azure.com), bläddra till resursen för din app. Du [Visa diagram som visar telemetri](app-insights-dashboards.md) från din app. (Om den inte visas på huvudsidan ännu, klickar du på Live Metrics Stream).

## <a name="next-steps"></a>Nästa steg
* [Ställa in en instrumentpanel](app-insights-dashboards.md) samla de viktigaste diagrammen övervaka detta och andra appar.
* [Läs mer om värden](app-insights-metrics-explorer.md)
* [Ställa in aviseringar](app-insights-alerts.md)
* [Diagnostisera prestandaproblem](app-insights-detect-triage-diagnose.md)
* [Kraftfulla Analytics-frågor](app-insights-analytics.md)
* [Webbtester för tillgänglighet](app-insights-monitor-web-app-availability.md)

