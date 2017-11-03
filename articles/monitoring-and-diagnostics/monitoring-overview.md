---
title: "Övervakning i Microsoft Azure | Microsoft Docs"
description: "Alternativ när du vill övervaka någonting i Microsoft Azure. Övervakare för Azure och Application Insights logganalys"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: robb
ms.openlocfilehash: e164cbd910ccc38610c7aef37d25ff1b4413038d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>Översikt över övervakning i Microsoft Azure
Den här artikeln innehåller en översikt över de verktyg och tjänster som ingår i helhetsmässigt övervakning av Microsoft Azure. Det gäller för:
- Använda Azure-tjänster som övervakar Azure-infrastrukturen och program
- Med hjälp av Azure-tjänster övervaka hybrid och Azure-infrastruktur och program
- Använda Azure-tjänster som övervakar Azure-infrastrukturen och program

Det beskriver de olika produkter och tjänster som är tillgängliga och hur de fungerar tillsammans. Det kan hjälpa dig att avgöra vilka verktyg som passar dig bäst i vilket fall.  

## <a name="why-use-azures-monitoring-services"></a>Varför använda Azures övervakningstjänster?

Prestandaproblem i din molnapp kan påverka din verksamhet. Med flera sammankopplade komponenter och ofta versioner kan degradations inträffa när som helst. Och om du utvecklar en app användarna identifiera vanligtvis problem som du inte kan hitta vid testning. Du bör känna till de här problemen omedelbart och har verktyg för att diagnostisera och lösa problem. Dessutom resultatet problem i programmet ofta av den underliggande infrastruktur som programmen körs, så att ha en heltäckande vy för programmet och infrastruktur är nyckeln till övervakning Azure-miljön. Microsoft Azure har en uppsättning verktyg för att identifiera och lösa dessa problem.

## <a name="how-do-i-monitor-my-azure-environment"></a>Hur övervakar jag min Azure-miljön?

Det finns en mängd olika verktyg för övervakning av Azure-miljön, från programkoden som körs på Azure till tjänster och infrastruktur för att kod körs. Dessa verktyg fungerar tillsammans så erbjuder omfattande molnövervakning och inkluderar:

-   **Azure-Monitor** -tjänsten för Azure som fungerar som en konsoliderad pipeline för alla övervakningsdata från Azure-tjänster. Det ger dig tillgång till prestandamått och händelser som beskriver användningen av Azure-infrastrukturen och Azure-tjänster som du använder. Azure övervakaren är en övervakning data pipeline för Azure-miljön och ger dessa data direkt till Log Analytics samt 3 part verktyg där du kan få insyn i dessa data och kombinera det med data från på lokala eller andra molnresurser.

-   **Application Insights** -tjänsten för Azure som erbjuder prestanda övervakning och användaren programanalysen. Det övervakar den kod som du har skrivit och program som du har distribuerat på Azure eller lokalt / andra moln. Du kan få åtkomst till en mängd olika data inklusive svarstider för beroenden, undantag spårningar, felsökning ögonblicksbilder och körning av profiler av instrumentering ditt program med Application Insights SDK. Den innehåller kraftfulla verktyg för analys av det här programmet telemetri vid utveckling och drift av ditt program. Den djupt kan integreras med Visual Studio så att du kan hitta problem raderna i koden så att du kan åtgärda det och ger användningsanalys för att analysera kundens användning av ditt program för produktchefer.

-   **Logga Analytics** -kallades OMS logganalys, är en Azure-tjänst som en logg- och mått från Azure-tjänster (via Azure-övervakning), virtuella Azure-datorer och lokala eller andra molntjänster infrastruktur och ger flexibla loggen sökningar och ut för det rutan analytics ovanpå dessa data. Den innehåller omfattande verktyg för att analysera data från källor, tillåter komplexa frågor över alla loggar och proaktivt kan meddela på angivna villkor.  Du kan även samla in anpassade data till den centrala databasen så kan fråga och visualisera den. Du kan också dra nytta av loggen analytiska inbyggda lösningar och få insikter om säkerheten omedelbart och funktionerna i infrastrukturen.

## <a name="accessing-monitoring-in-the-azure-portal"></a>Åtkomst till övervakning i Azure-portalen
Alla övervakningstjänster för Azure finns nu i en enda UI-fönstret. Mer information om hur du åt det här området finns [Kom igång med Azure-Monitor](monitoring-get-started.md). 

Du kan också komma åt övervakning funktioner för specifika resurser genom att markera resurserna och gå nedåt i sina övervakningsalternativ. 

## <a name="examples-of-when-to-use-which-tool"></a>Exempel på när du ska använda vilka verktyg 

Följande avsnitt visar några grundläggande scenarier och vilka verktyg som ska användas tillsammans. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>Scenario 1 – reparera fel i ett Azure-program under utveckling   

**Det bästa alternativet är att använda Application Insights, Azure-Monitor och Visual Studio tillsammans**

Azure tillhandahåller nu alla fördelar med Visual Studio-felsökaren i molnet. Konfigurera Azure-Monitor för att skicka telemetri till Application Insights. Aktivera Visual Studio för att lägga till Application Insights SDK i programmet. En gång i Application Insights, du kan använda den programavbildningen visuellt identifiera vilka delar i tillämpningsprogrammet körs är ogiltigt eller inte. För de delar som inte är felfri, finns fel och undantag redan för undersökning. Du kan använda olika analytics i Application Insights gå djupare. Du kan använda Visual Studio-felsökaren för att spåra ytterligare problem i koden och PIN-kod om du inte är säker om felet. 

Mer information finns i [övervakning Web Apps](../application-insights/app-insights-azure-web-apps.md) och referera till innehållsförteckningen till vänster anvisningar på olika typer av appar och språk.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>Scenario 2 – felsöka ett Azure .NET-webbprogram för fel som endast visar i produktion 

> [!NOTE]
> Dessa funktioner finns i förhandsgranskningen. 

**Det bästa alternativet är att använda Application Insights och om möjligt Visual Studio för fullständig felsökning uppstå.**

Använda Application Insights ögonblicksbild felsökaren för att felsöka appen. När ett visst tröskelvärde för fel inträffar med olika komponenter i fångar systemet automatiskt telemetri i windows tid som kallas ”ögonblicksbilder”. Avbildas är säkra för produktion moln eftersom den är tillräckligt små för att inte påverka prestanda men betydande att tillåta spårning.  Systemet kan ta flera ögonblicksbilder. Du kan titta på en punkt i tiden i Azure-portalen eller använda Visual Studio med den fullständiga upplevelsen. Med Visual Studio kan utvecklare igenom den ögonblicksbilden som om de felsökning i realtid. Lokala variabler, parametrar, minne och ramar är alla tillgängliga. Utvecklare måste beviljas åtkomst till den här produktionsdata via en RBAC-roll.  

Mer information finns i [ögonblicksbild felsökning](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>Scenario 3 – felsöka ett Azure-program som använder behållare eller mikrotjänster 

**Samma som scenario 1. Använda Application Insights, Azure-Monitor och Visual Studio tillsammans** Application Insights stöder också samla in telemetri från processer som körs i behållare och mikrotjänster (Kubernetes, Docker, Azure Service Fabric). Mer information [finns i den här videon om hur du felsöker behållare och mikrotjänster](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Scenario 4 – korrigera prestandaproblem i ditt Azure-program

Den [Application Insights profiler](../application-insights/app-insights-profiler.md) är utformad för att felsöka dessa typer av problem. Du kan identifiera och felsöka problem med prestanda för program som körs i Apptjänster (Web Apps Logic Apps Mobilappar, API Apps) och andra beräkningsresurser som virtuella datorer, virtuella skaluppsättningar (VMSS), Cloud Services och Service Fabric. 

> [!NOTE]
> Möjligheten att profilen för virtuella datorer, virtuella skaluppsättningar (VMSS), Cloud Services och tjänster Fabric är i förhandsgranskningen.   

Dessutom kan meddelas du proaktivt via e-post om vissa typer av fel som långsam sidinläsningstider av verktyget Smart identifiering.  Du behöver inte göra någon konfiguration på det här verktyget. Mer information finns i [Smart identifiering - Prestandaavvikelser](../application-insights/app-insights-proactive-performance-diagnostics.md).



## <a name="next-steps"></a>Nästa steg
Lär dig mer om

* [Azure Övervakare i ett videoklipp från Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Komma igång med Azure Övervakare](monitoring-get-started.md)
* [Azure Diagnostics](../azure-diagnostics.md) om du försöker att diagnostisera problem i din molntjänst, virtuell dator, virtuella skala anges eller Service Fabric-programmet.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) om du försöker diagnostiska problem i din App Service Web app.
* [Logga Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) och [Operations Management Suite](https://www.microsoft.com/oms/) produktion övervakningslösning
