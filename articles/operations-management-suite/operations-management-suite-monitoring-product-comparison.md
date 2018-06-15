---
title: Microsoft monitoring Produktjämförelse | Microsoft Docs
description: Microsoft Operations Management Suite (OMS) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur.  I den här artikeln identifierar vi de olika tjänster som ingår i OMS. Artikeln innehåller också länkar till mer detaljerat innehåll om tjänsterna.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
ms.openlocfilehash: b4201f105a87b0a41059c061eb37fb35d4514e02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866319"
---
# <a name="microsoft-monitoring-product-comparison"></a>Microsoft övervakning Produktjämförelse
Den här artikeln innehåller en jämförelse mellan System Center Operations Manager (SCOM) och logganalys i Operations Management Suite (OMS) i sina arkitektur, logiken för hur de övervaka resurser och hur de utför analys av den information som samlas in .  Detta är att ge dig en grundläggande förståelse av deras skillnader och relativa styrka.  

## <a name="basic-architecture"></a>Grundläggande arkitektur
### <a name="system-center-operations-manager"></a>System Center Operations Manager
Alla installeras SCOM i ditt datacenter.  [Agenter är installerade](http://technet.microsoft.com/library/hh551142.aspx) på Windows- och Linux-datorer som hanteras av SCOM.  Agenter som ska ansluta till [hanteringsservrar](https://technet.microsoft.com/library/hh301922.aspx) som kommunicera med SCOM-driftdatabasen och datalagret.  Agenter är beroende av domänautentisering för att ansluta till hanteringsservrar.  De utanför en betrodd domän kan utföra autentisering med datorcertifikat eller ansluta till en [Gateway-servern](https://technet.microsoft.com/library/hh212823.aspx).

SCOM kräver två SQL-databaser, ett för användningsdata och ett annat datalager som stöd för rapportering och dataanalys.  En [rapportservern](https://technet.microsoft.com/library/hh298611.aspx) kör SQL Reporting Services för att rapportera om data från datalagret. 

SCOM kan övervaka molnresurser med hanteringspaket för produkter som [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708), och [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Dessa hanteringspaket använda en eller flera lokala agenter som proxyservrar för identifierande molnresurser och arbetsflöden som körs för att mäta prestanda och tillgänglighet.  Proxy-agenterna kan även användas för att [övervaka nätverksenheter](https://technet.microsoft.com/library/hh212935.aspx) och andra externa resurser.

Driftkonsolen är ett windowsprogram som ansluter till någon av hanteringsservrarna och administratören kan visa och analysera insamlade data och konfigurera SCOM-miljö.  En webbaserad konsol kan finnas på en IIS-server och ger dataanalys via en webbläsare.

![SCOM-arkitektur](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
De flesta OMS-komponenter finns i Azure-molnet så att du kan distribuera och hantera den med minimal kostnad och administrativt arbete.  Alla data som samlas in av logganalys lagras i OMS-databasen.

Logganalys kan samla in data från en av tre källor:

* Fysiska och virtuella datorer som kör Windows och [Microsoft Monitoring Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) - eller Linux och [Operations Management Suite-Agent för Linux](https://technet.microsoft.com/library/mt622052.aspx).  Dessa datorer kan vara lokala eller virtuella datorer i Azure eller en annan molnet.
* Ett Azure Storage-konto med [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) data som samlas in av virtuell dator, webbroll eller Azure-arbetsroll.
* [Anslutning till en SCOM-hanteringsgrupp](https://technet.microsoft.com/library/mt484104.aspx).  I den här konfigurationen kommunicera agenterna med SCOM-hanteringsservrar som levererar data till databasen SCOM där den sedan levereras till OMS-datalagret.
  Administratörer analysera insamlade data och konfigurera logganalys med OMS-portalen som finns i Azure och kan nås från valfri webbläsare.  Mobila appar åtkomst till dessa data är tillgängliga för plattformarna som standard.

![Log Analytics-arkitektur](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integrera SCOM och logganalys
När SCOM används som datakälla för Log Analytics kan du använda funktioner för båda produkterna i ett hybridscenario övervakning miljö.  Du kan konfigurera befintliga SCOM agenter via Driftkonsolen som ska hanteras av OMS, förutom fortsätter att köra hanteringspaket från SCOM.  
Data från en ansluten SCOM-hanteringsgrupp levereras till Log Analytics med hjälp av någon av fyra metoder:

* Händelserna och prestandadata samlas in av agenten och levereras till SCOM.  Hanteringsservrar i SCOM skickar data till logganalys.
* Vissa händelser, till exempel IIS-loggar och säkerhetshändelser fortsätta att levereras direkt till Log Analytics från agenten.
* Vissa lösningar ska leverera ytterligare programvara till agenten eller kräver att programvaran installeras för att samla in ytterligare data.  Dessa data skickas vanligtvis direkt till Log Analytics.
* Vissa lösningar samlar in data direkt från SCOM-hanteringsservrar som inte kommer från agenten.  Till exempel den [avisering hanteringslösning](https://technet.microsoft.com/library/mt484092.aspx) samlar in varningar från SCOM när de har skapats.

## <a name="monitoring-logic"></a>Övervakningslogik
SCOM och logganalys arbeta med liknande data som samlas in från agenter har viktiga skillnader i hur de definiera och implementera sin logik för insamling av data och hur de analysera den insamlade data

### <a name="operations-manager"></a>Operations Manager
Övervakningslogiken för SCOM är implementerad i [hanteringspaket](https://technet.microsoft.com/library/hh457558.aspx) som innehåller logik för identifiering av komponenter som ska övervakas, Avläsning av hälsotillstånd för dessa komponenter och för att samla in data för att analysera.  Övervakning av data kan vara så enkelt som att samla in en händelser och prestandaräknare eller den kan använda komplex logik som implementeras i ett skript.  Hanteringspaket som innehåller fullständig övervakning är tillgängliga för olika [program från Microsoft och tredje parter](http://go.microsoft.com/fwlink/?LinkId=82105) förutom maskinvaru- och enheter.  Du kan [skapa egna hanteringspaket](http://aka.ms/mpauthor) för anpassade program.

Hanteringspaket innehåller flera olika arbetsflöden som alla utför vissa övervakning specifik funktion som provtagning en prestandaräknare, kontrollera status för en tjänst eller köra ett skript.  Varje arbetsflöde körs oberoende av varandra och definierar sin egen, till exempel vilken databas som den kommer att skrivas resultat och om den skapar en avisering. 

Du kan åsidosätta detaljer om arbetsflödet, till exempel hur ofta de körs tröskelvärdet när de anser att ett fel och allvarlighetsgrad för aviseringen de genererar.  Du kan också ange ytterligare funktioner genom att lägga till egna arbetsflöden.

![Åsidosättningar](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Hanteringspaket är installerade på Operations Manager-databasen och automatiskt distribueras till agenter av hanteringsservrar.  Varje agent hämta hanteringspaket automatiskt och läsa in arbetsflöden som är relevanta för de program som de har installerats.  Data som samlas in av agenten skickas tillbaka till hanteringsservern för infogning i SCOM-driftdatabasen och datalagret.  Driftkonsolen kan du visa och analysera data via anpassade vyer, instrumentpaneler och rapporter som finns i management pack.

Distributionen av hanteringspaket illustreras i följande diagram.

![Management pack-flöde](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>Händelse- och insamling av prestanda
Logganalys samlar in händelser och prestandaräknare från agent-system som använder källor som Windows-händelseloggen, IIS-loggar och Syslog.  Du kan ange kriterier för vilka data samlas in via logganalys-portalen och sedan skapa loggen frågor för att analysera insamlade data.  En uppsättning villkor som standard definieras när du skapar din OMS-arbetsyta och du kan ange ytterligare information om specifika program. 

![Definiera händelseloggar i logganalys](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

SCOM har många detaljerad arbetsflöden som vanligtvis definierar specifika kriterier för data och den åtgärd som ska utföras i svaret, har Log Analytics mer allmänna villkor för datainsamling.  Loggen frågor och lösningar ger mer riktade kriterier för att analysera och fungerar på specifika data i molnet när den har samlats in.

#### <a name="solutions"></a>Lösningar
Lösningar som erbjuder ytterligare logik för insamling och analys.  Du kan välja lösningar för att lägga till i prenumerationen OMS från galleriet lösning.

![Lösningar galleri](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Lösningar köras i första hand i molnet ger analys av händelser och prestandaräknare som samlats in i OMS-databasen.  De kan också definiera ytterligare data som ska samlas in som kan analyseras med loggen frågor eller genom ytterligare användargränssnittet i lösningen i OMS-instrumentpanelen. 

Till exempel den [ändringsspårning lösning](https://technet.microsoft.com/library/mt484099.aspx) identifierar konfiguration ändras på agenten system och skriver händelser i OMS-databasen som kan analyseras med flera grafiska vyer som summerar identifierat ändringar.  Du kan öka detaljnivån från vyn sammanfattade i loggen frågor som visar detaljerad information som samlas in av lösningen.

Medan du kan välja vilka lösningar som du lägger till i din prenumeration måste har du för närvarande inte möjlighet att skapa egna lösningar.  Du kan välja händelser och prestandaräknare att samla in och skapa anpassade vyer baserat på egna log-frågor.

Övervakningslogik för Log Analytics sammanfattas i följande diagram.

![Log Analytics lösning flöde](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Hälsoövervakning
### <a name="operations-manager"></a>Operations Manager
SCOM kan utforma de olika komponenterna i ett program och ange realtid hälsotillstånd för varje.  Detta gör att du kan inte bara visa identifierat fel och prestanda över tid, men också att verifiera faktiska hälsotillståndet för ett program eller system och varje komponent vid en given tidpunkt.  Eftersom den förstår tidsperioder som ett program är tillgängligt stöder hälsa motorn i SCOM också Service Level avtal (SLA) som analysera och rapportera om tillgängligheten för ett program över tid.

Vyn nedan visar exempelvis realtid hälsotillståndet för SQL database motorerna övervakas av SCOM.  Hälsotillståndet för var och en av databaserna för en av database motorerna visas längst ned i hälften av vyn.

![Tillståndsvy](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

Hälsoutforskaren för ett database motorerna visas nedan med de Övervakare som används för att fastställa dess allmänna tillstånd.  De här övervakarna definieras i SQL management pack och köra mot alla SQL-databasprogram som identifierats av SCOM.

![Hälsoutforskaren](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Komponenter på flera datorer kan kombineras för att mäta hälsotillståndet för ett distribuerat program.  Detta kan vara särskilt användbart för affärsprogram som innehåller flera distribuerade komponenter.  Du kan skapa en modell som mäter hälsotillståndet för varje komponent att den samlade in tillgänglighet för programmet.

Active Directory är ett exempel på ett hanteringspaket som ger en modell för att analysera de distribuerade komponenterna.  Exempel diagrammet nedan visar hälsotillståndet för hela miljön och relationen mellan skogar, domäner och domänkontrollanter.  Var och en av dessa komponenter omfattar delkomponenter och flera bildskärmar liknar SQL-exemplet ovan.

![SCOM diagramvy](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS inte inkludera en gemensam motor till modellen program och mäter deras realtid hälsa.  Enskilda lösningar kan utvärdera den övergripande hälsan för specifika tjänster som är baserade på insamlade data och de kan installera egen kod på agenten att utföra analyser i realtid.  Eftersom lösningar körs i molnet med åtkomst till OMS-databasen, kan de ofta ger djupare analys än normalt utförs av hanteringspaket. 

Till exempel den [AD-bedömning och SQL-bedömning lösningar](https://technet.microsoft.com/library/mt484102.aspx) analysera insamlade data och ange en klassificering för olika områden i miljön.  Den innehåller rekommendationer för förbättringar som kan göras för att förbättra tillgänglighet och prestanda för miljön.

![AD lösning](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Dataanalys
SCOM och Log Analytics har olika funktioner för att analysera insamlade data.  SCOM har vyer och instrumentpaneler i Operations-konsolen för att analysera senaste data i en mängd olika format och rapporter för att presentera data från datalagret i tabellform.  Log Analytics tillhandahåller en komplett logg frågespråket och gränssnitt för att analysera data i OMS-databasen.  När SCOM används som datakälla för Log Analytics, innehåller databasen data som samlas in av SCOM så logganalys-verktyg kan användas för att analysera data från båda systemen.

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>Vyer
Vyer i Operations-konsolen kan du visa olika datatyper som samlats in av SCOM i olika format, vanligtvis tabular för händelser, varningar och systemtillstånd, och linjediagram för prestandadata.  Vyer utföra minimal analys eller konsolidering av data, men att du kan filtrera enligt särskilda kriterier. 

![Vyer](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Hanteringspaket ger vanligtvis flera vyer som stöder programmets eller systemets som den övervakar.  Detta kan inkludera tillståndsvyer för de olika objekt som hanteringspaketet identifierar aviseringsvyer för identifierade problem och prestandavyer för räknare.

Vyer är särskilt lämplig för att analysera det aktuella tillståndet i miljön, inklusive öppna aviseringar och hälsotillståndet för övervakade system och objekt.  Du kan öka detaljnivån till detaljerad händelser eller prestandadata som stöd för en viss avisering för att kunna diagnostisera den grundläggande orsaken. På liknande sätt kan du visa prestanda och hälsotillståndet för olika komponenterna i ett program för att utvärdera dess aktuella hälsa.

#### <a name="dashboards"></a>Instrumentpaneler
Instrumentpaneler i Driftkonsolen fungerar främst med samma data som vyer men mer anpassningsbara och kan innehålla bättre visualiseringar.  En uppsättning standard instrumentpaneler är tillgängliga att du kan enkelt anpassa för egna ändamål.  Du kan också använda en PowerShell-widget som kan visa data som returnerats från en PowerShell-fråga.

![Instrumentpanel](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Utvecklare har möjligheten att lägga till anpassade komponenter till instrumentpaneler de inkluderar i sina hanteringspaket.  Dessa kan vara specialiserade till ett visst program, till exempel instrumentpanelen i SQL management pack som visas nedan.  Den här instrumentpanelen kan också användas som en mall för anpassade versioner.

![SQL-instrumentpanelen](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Rapporter
Rapporter i SCOM analysera data från datalagret i tabellform.  De kan skrivas ut och schemalagd automatisk i olika filformat, inklusive PDF, CSV och Word.  Rapporter arbeta med data från datalagret så att de är särskilt lämplig för analys av långsiktiga trender.

Hanteringspaket ger vanligtvis anpassade rapporter för ett visst program.  Du kan också välja från ett bibliotek för allmänna rapporter som du kan anpassa för ditt eget program eller för att ad hoc-analys.

Följande är en exempelrapport prestanda som visar data som samlas in av Active Directory Management Pack.

![Rapport](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Logganalys har en [frågespråket](https://technet.microsoft.com/library/mt484120.aspx) som du kan använda för att utföra analyser över data från flera program utan att behöva skapa en anpassad vy eller rapporten.  Eftersom OMS implementeras i molnet, omfattas inte av några maskinvarubegränsningar prestanda för frågor och analys av data och kan snabbt analysera inklusive miljontals poster. 

Frågorna i logganalys är också grunden för andra funktioner.  Du kan spara en fråga, exportera resultaten till Excel eller automatiskt köra regelbundet och generera en avisering om resultaten uppfyller specifika villkor.  

![Loggen frågan flöde](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Nedan visas ett exempel på en Log Analytics-fråga.  I det här exemplet returneras alla händelser med ”starta” i namnet och grupperade efter händelse-ID.  Anger användaren helt enkelt frågan och logganalys genererar dynamiskt användargränssnitt för att utföra analysen.  Att välja ett objekt i listan returnerar detaljerad händelsedata.

![Log-fråga](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Förutom att tillhandahålla ad hoc-analys, frågor i logganalys kan sparas för framtida användning och även lagt till i din [OMS instrumentpanelen](http://technet.microsoft.com/library/mt484090.aspx) som visas i följande exempel.

![OMS-instrumentpanelen](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Nästa steg
* Distribuera [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
* Registrera dig för [logganalys](https://azure.microsoft.com/documentation/services/log-analytics).  

