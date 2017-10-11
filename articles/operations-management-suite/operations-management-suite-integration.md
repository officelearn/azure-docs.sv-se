---
title: Integrera med Operations Management Suite (OMS) | Microsoft Docs
description: "Förutom att använda standard OMS-funktionerna kan integrera du den med andra av hanteringsprogram och tjänster att tillhandahålla en hybridmiljö för hantering, att tillhandahålla anpassade hanteringsscenarier som är unik för din miljö eller ange en anpassad hantering upplevelse för kunderna.  Den här artikeln innehåller en översikt över olika alternativ för att integrera med OMS och länkar till artiklar som ger detaljerad teknisk information."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7a24df6f2c3b2c091d1b66b8b9c0a61035ffde11
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="integrating-with-operations-management-suite-oms"></a>Integrera med Operations Management Suite (OMS)
Operations Management Suite är Microsofts molnbaserade IT lösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur.  Förutom att använda standard OMS-funktionerna kan integrera du den med andra av hanteringsprogram och tjänster att tillhandahålla en hybridmiljö för hantering, att tillhandahålla anpassade hanteringsscenarier som är unik för din miljö eller ange en anpassad hantering upplevelse för kunderna.  Den här artikeln innehåller en översikt över olika alternativ för att integrera med OMS-tjänster och länkar till artiklar som ger detaljerad teknisk information. 

## <a name="log-analytics"></a>Log Analytics
Av hanteringsdata som samlas in av logganalys lagras i en databas som finns i Azure.  Alla data som lagras i databasen är tillgänglig i loggen sökningar som ger snabb analys över mycket stora mängder data.  Din integreringskraven kan vara att fylla i databasen med nya data, gör den tillgänglig för analys eller att extrahera data i databasen att tillhandahålla nya visualiseringen eller integrera med ett annat hanteringsverktyg.

Varje typ av data i databasen lagras som en post.  När du fyller i databasen, bör du ge användare med den typ av post som använder din lösning och en beskrivning av dess egenskaper.  När du hämtar data, måste den här informationen om de data som du arbetar med.

![Fylla i OMS-databasen](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>Fyll i logganalys-databasen
Det finns flera metoder för att fylla i OMS-databasen.  Vilken metod du använder beror på faktorer som där datakällan finns, i form av data och som du behöver stöd för klienter.  När data lagras i databasen, spelar det ingen roll som hur den samlats in.

I följande avsnitt beskrivs de olika alternativen för att fylla i OMS-databasen.

#### <a name="connected-sources-and-data-sources"></a>Anslutna källor och datakällor
Anslutna källor är där du kan hämta data för OMS-databasen.  Datakällor och lösningar körs på anslutna källor och definiera specifika data som samlas in.  Om ditt program skriver data till en av dessa datakällor, kan du samla den genom att konfigurera datakällan.  Till exempel om ditt program skapar Syslog-händelser, sedan de samlas in av datakällan Syslog på en Linux-agenten.

* [Datakällor i logganalys](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Lösningar
Lösningar utöka funktionerna i OMS.  En lösning som kan samla in data från den anslutna datakällan eller den kan utföra analyser på poster som redan samlats in i databasen.  Varje lösning som tillhandahålls av Microsoft har en enskild artikel som innehåller information om de data som samlas in.

* [Lösningar i logganalys](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>HTTP-datainsamlaren API
Log Analytics HTTP Data Collector API är en REST-API som gör att du kan lägga till JSON-data i logganalys-databasen.  Du kan utnyttja detta API när du har ett program som inte ger data via ett av de andra datakällor eller lösningar.  Det kan användas för att fylla i databasen från klienter som kan anropa API: et och inte är beroende av samlingen schemat för en datakälla eller en lösning.

* [Log Analytics HTTP datainsamlaren API](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>Hämta data från databasen logganalys
Det finns flera metoder för att hämta data från OMS-databasen.  Du kanske vill att användarna ska hämta data med hjälp av OMS-konsolen och förser dem med olika typer av grafik och analys.  Du kan också hämta data från en extern process, till exempel en annan lösning.

#### <a name="log-searches"></a>Log-sökningar
Alla data som lagras i OMS-databasen är tillgänglig via loggen sökningar.  Användare kan utföra sina egna ad hoc-analys i OMS-konsolen eller skapa en instrumentpanel med en visualisering för en viss logg-sökning.  Lösningar kan innehålla anpassade vyer med grafik baserad på fördefinierade sökningar.  Du kan använda Sök-API loggen att komma åt data i OMS-databasen från ett externt program eller hantering av verktyg.  

* [Loggen söker i logganalys](../log-analytics/log-analytics-log-searches.md)
* [Logganalys logga Sök REST API](../log-analytics/log-analytics-log-search-api.md)
* [Log Analytics-cmdlets](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>Anpassade vyer
View Designer kan du skapa anpassade vyer i OMS-konsolen som ger användare visualisering och analys av data i din lösning.  Varje vy innehåller en ikon som visas på huvudsidan i konsolen och valfritt antal visualiseringen delar som är baserade på loggen sökningar som du definierar.

* [Log Analytics Vydesigner](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
Logganalys exportera automatiskt data från OMS-databasen till Power BI så att du kan utnyttja dess visualiseringar och verktyg för analys.  Den här exporten utförs enligt ett schema så att data är uppdaterade. 

* [Exportera logganalys data till Power BI](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>Automation
OMS kan automatisera processer att reagera på insamlade data eller utföra andra hanteringsfunktioner.  Det kan samla in data från ditt program och infoga i OMS-databasen eller du kan automatisera korrigering av ett känt problem som svar på data i databasen. 

![Automation](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooks
Runbooks i Azure Automation Kör PowerShell-skript och arbetsflöden i Azure-molnet.  Du kan använda dem för att hantera resurser i Azure eller andra resurser som kan nås från molnet.  Runbooks kan också köras i ett lokalt datacenter med hjälp av Hybrid Runbook Worker.  Du kan starta en runbook från Azure-portalen eller externa processer med hjälp av ett antal metoder, till exempel PowerShell eller Automation-API.

* [Starta en runbook i Azure Automation](../automation/automation-starting-a-runbook.md)
* [Azure Automation-cmdlets](https://msdn.microsoft.com/library/dn690262.aspx)
* [Automation REST API](https://msdn.microsoft.com/library/mt662285.aspx)
* [Automation .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Aviseringar
Varningsregler kör automatiskt logga sökningar enligt ett schema.  Om resultaten matchar särskilda resulterande aviseringen startar en runbook i Azure Automation eller anropa en webhook som kan starta en extern process.  Båda dessa svar kan innehålla information om aviseringen, inklusive data som returneras i loggen sökningen.

* [Aviseringar i logganalys](../log-analytics/log-analytics-alerts.md)
* [Log Analytics avisering API](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>Säkerhetskopiering och återställning
Azure Backup och Site Recovery tillhandahåller tjänster för att skydda företagets data och säkerställa tillgängligheten för servrar och program.  Du kan använda dessa tjänster för att utföra dessa scenarier som erbjuder tjänster för säkerhetskopiering för ditt program eller initiera en redundansväxling för en virtuell dator.

* [Azure Backup-cmdletar](https://msdn.microsoft.com/library/mt619253.aspx)
* [Azure Site Recovery REST-API](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Azure Site Recovery-Cmdlets](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Anpassade lösningar
Du kan kapsla integration logik i en anpassad lösning för att köra i arbetsytan eller i en kund-arbetsytan.  Din lösning kan innehålla någon av metoderna integrering i den här artikeln förutom övriga resurser för att ange en fullständig hanteringsscenario.  Resurser i lösningen paketeras så att när lösningen tas bort alla resurser som skapats tas bort från OMS-arbetsytan och Azure-prenumeration.

Lösningen kan exempelvis omfatta en Automation-runbook för att samla in och bearbeta data och fylla i logganalys-databasen med hjälp av HTTP-Data Collector API.  Du kan också inkludera en anpassad vy som visar och analyserar insamlade data.  

* Skapa anpassade lösningar (kommer snart)    

## <a name="next-steps"></a>Nästa steg
* Referens för den [OMS SDK](operations-management-suite-sdk.md) teknisk information om automatisering OMS-tjänster.  

