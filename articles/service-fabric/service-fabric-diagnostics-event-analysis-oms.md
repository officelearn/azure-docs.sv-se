---
title: "Azure Service Fabric-Händelseanalys med OMS | Microsoft Docs"
description: "Läs mer om visualisera och analysera händelser med hjälp av OMS för övervakning och diagnostik av Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: f0cefab15a115719ea9c378546a7e6004bd06187
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Händelseanalys och visualisering med OMS

Operations Management Suite (OMS) är en uppsättning tjänster som underlättar övervakning och diagnostik för program och tjänster i molnet. För att få en mer detaljerad översikt över OMS och den erbjuder, läsa [vad är OMS?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Logganalys och OMS-arbetsyta

Logganalys samlar in data från hanterade resurser, inklusive en Azure-lagring eller en agent och underhåller i en central databas. Data kan sedan användas för analys, varningar och visualisering eller ytterligare exporteras. Logganalys stöder händelser, prestandadata eller andra anpassade data.

När OMS konfigureras, kommer du ha tillgång till en specifik *OMS-arbetsytan*, från där data kan efterfrågas eller visualiseras i instrumentpaneler.

När data tas emot av logganalys OMS har flera *hanteringslösningar* som är färdigförpackade lösningar för att övervaka inkommande data, anpassas efter flera scenarier. Dessa inkluderar en *Service Fabric Analytics* lösning och en *behållare* lösning som är de två viktigaste som att diagnostik- och övervaka när du använder Service Fabric-kluster. Det finns flera andra program som är värda att utforska och OMS kan även användas för att skapa anpassade lösningar som du kan läsa mer om [här](../operations-management-suite/operations-management-suite-solutions.md). Varje lösning som du vill använda för ett kluster kan konfigureras i samma OMS-arbetsyta, tillsammans med logganalys. Arbetsytor Tillåt för anpassade instrumentpaneler och visualisering av data och ändringar av data som du vill samla in processen och analysera.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>Ställa in en OMS-arbetsyta med Service Fabric Analytics lösning
Vi rekommenderar att du inkluderar Service Fabric-lösningen i OMS-arbetsyta - innehåller en instrumentpanel som visar olika inkommande loggkanaler från nivån plattform och program och ger möjlighet att fråga Service Fabric specifika loggar. Här är en relativt enkla Service Fabric-lösningen ser ut, med ett enda program som distribuerats på klustret:

![OMS SA lösning](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Det finns två sätt att etablera och konfigurera en OMS-arbetsyta, antingen via en Resource Manager-mall eller direkt från Azure Marketplace. Använd det tidigare när du distribuerar ett kluster och den senare om du redan har ett kluster som distribueras med diagnostik aktiverat.

### <a name="deploying-oms-using-a-resource-management-template"></a>Distribuera OMS med hjälp av en Resource Manager-mall

När du distribuerar ett kluster med en Resource Manager-mall mallen kan också skapa en ny OMS-arbetsyta, lägga till Service Fabric-lösningen i den och konfigurera den för att läsa data från lämplig storage-tabeller.

>[!NOTE]
>För att detta ska fungera måste diagnostik måste vara aktiverat för Azure storage-tabeller finns för OMS / Log Analytics läsa information i från.

[Här](https://azure.microsoft.com/resources/templates/service-fabric-oms/) är en exempelmall som du kan använda och ändra enligt krav som utför ovanstående åtgärder. I det fall att du vill använda flera alternativ, det finns några fler mallar som ger olika alternativ beroende på i processen kanske du för att skapa en OMS-arbetsyta - de finns på [Service Fabric och OMS mallar](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

### <a name="deploying-oms-using-through-azure-marketplace"></a>Distribution OMS med via Azure Marketplace

Om du vill lägga till en OMS-arbetsyta när du har distribuerat ett kluster, gå till Azure Marketplace och letar efter *”Service Fabric Analytics”*.

![OMS SA analyser i Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

* Klicka på **skapa**
* Klicka på i fönstret Service Fabric Analytics skapa **Välj en arbetsyta** för den *OMS-arbetsytan* fältet och sedan **skapa en ny arbetsyta**. Fyll i obligatoriska poster – det enda kravet är att prenumerationen för Service Fabric-kluster och OMS-arbetsytan ska vara samma. När posterna har validerats startar OMS-arbetsytan att distribuera. Detta tar bara några minuter.
* När du är klar klickar du på **skapa** igen längst ned i fönstret Skapa Service Fabric Analytics. Kontrollera att den nya arbetsytan visas under *OMS-arbetsytan*. Detta lägger till lösningen till arbetsytan som du nyss skapade.


Även om detta lägger till lösningen till arbetsytan måste arbetsytan fortfarande vara ansluten till diagnostikdata från klustret. Navigera till den resursgrupp som du har skapat lösningen i Service Fabric Analytics. Du bör se en *ServiceFabric (\<nameOfOMSWorkspace\>)*.

* Klicka på lösningen att navigera till dess översiktssidan från där du kan ändra inställningar för lösning, arbetsytan inställningar, och navigera till OMS-portalen.
* I den vänstra navigeringsmenyn klickar du på **lagringskonton loggar**under *arbetsytan datakällor*.

    ![Service Fabric Analytics lösning i portalen](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-portal.png)

* På den *Storage-konto loggar* klickar du på **Lägg till** högst upp att lägga till ditt kluster loggar till arbetsytan.
* Klicka i **lagringskonto** att lägga till lämpligt konto som skapats i klustret. Om du använde standardnamnet för storage-konto med namnet *sfdg\<resourceGroupName\>*. Du kan också bekräfta detta genom att kontrollera Azure Resource Manager-mallen som används för att distribuera ditt kluster genom att kontrollera värdet för den `applicationDiagnosticsStorageAccountName`. Du kan också behöva rulla nedåt och klicka på **läsa in mer** om kontonamnet inte visas. Klicka på rätt lagringskontonamnet när den ser ut för att välja den.
* Därefter måste du ange den *datatyp*, som borde vara **Service Fabric händelser**.
* Den *källa* automatiskt ska anges till *WADServiceFabric\*EventTable*.
* Klicka på **OK** att ansluta din arbetsyta till ditt kluster loggar.

    ![Lägg till storage-konto loggar i OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

* Kontot visas nu som en del av din *Storage-konto loggar* i din arbetsyta datakällor.

Du har nu lagt till Service Fabric Analytics-lösningen med den här i en OMS logganalys-arbetsyta som nu är korrekt ansluten till ditt kluster plattform och programmet tabell. Du kan lägga till ytterligare datakällor till arbetsytan på samma sätt.

## <a name="using-the-oms-agent"></a>Med hjälp av OMS-Agent

Är det rekommenderat att använda EventFlow och BOMULLSTUSS som aggregering lösningar eftersom de tillåter en mer modulär metod för diagnostik och övervakning. Om du vill ändra dina utdata från EventFlow krävs till exempel ändras inte den faktiska instrumentation en enkel ändring i konfigurationsfilen. Om du dock du bestämmer dig att investera i med hjälp av OMS och är beredd att fortsätta att använda den för händelseanalys (behöver inte vara den enda plattform som du använder, men i stället att det kommer att minst en av plattformarna), rekommenderar vi att du först ställa in den [OMS-agent](../log-analytics/log-analytics-windows-agents.md). Du bör också använda OMS-agenten när du distribuerar behållare i klustret som beskrivs nedan.

Processen för att göra detta är relativt enkelt eftersom du behöver bara lägga till agenten som en virtuell dator skala ange tillägg till Resource Manager-mall att säkerställa att den installeras på alla noder. En Resource Manager-mall som distribuerar OMS-arbetsytan med Service Fabric-lösning (se ovan) och lägger till agenten noderna kan hittas för kluster som kör [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) eller [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Fördelar med detta är följande:

* Rikare data om prestanda räknare och mått på klientsidan
* Enkelt att konfigurera mått som samlas in från klustret och utan att behöva uppdatera din klusterkonfigurationen. Ändringar av inställningarna för agentens kan göras från OMS-portalen och agenten startas om automatiskt så att den matchar konfigurationen. Om du vill konfigurera OMS-agent för specifika prestandaräknare, gå till arbetsytan **Start > Inställningar > Data > Windows prestandaräknare** och välj de data som du vill visa insamlade
* Data visas snabbare än med lagras före tas upp av OMS / logganalys
* Övervakning av behållare är mycket enklare eftersom den kan hämta docker-loggar (stdout, stderr) och statistik (prestandamått på behållaren och nod-nivåer)

Den huvudsakliga ersättningen är att eftersom agenten ska distribueras på ditt kluster tillsammans med alla program, det kan finnas vissa påverkan på prestanda för ditt program i klustret.

## <a name="monitoring-containers"></a>Övervakning av behållare

När du distribuerar behållare till ett Service Fabric-kluster, rekommenderas det att klustret har konfigurerats med OMS-agent och att lösningen behållare har lagts till OMS-arbetsyta för att övervaka och diagnostik. Här är lösningen behållare ser ut i en arbetsyta:

![Grundläggande OMS-instrumentpanelen](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenten aktiverar insamlingen av flera behållare-specifika loggar som kan frågas i OMS eller användas för visualiserade nyckeltal. Log-typer som samlas in är:

* ContainerInventory: Visar information om behållarens plats, namn och bilder
* ContainerImageInventory: information om distribuerade bilder, inklusive ID eller storlekar
* ContainerLog: specifika felloggar, docker-loggar (stdout osv.) och andra transaktioner
* ContainerServiceLog: docker daemon kommandon som har körts
* Prestanda: prestandaräknare inklusive behållaren cpu, minne, nätverkstrafik, disk-i/o och anpassade mått från värddatorerna

Den här artikeln beskriver de steg som krävs för att ställa in behållaren övervakning för klustret. Mer information om OMSS behållare lösning checka ut sina [dokumentationen](../log-analytics/log-analytics-containers.md).

Kontrollera att du har OMS-agenten distribueras på din klusternoder genom att följa stegen ovan om du vill konfigurera lösningen behållare på arbetsytan. När klustret är klar kan du distribuera en behållare till den. Tänk dessutom på att den första gången som en behållare avbildning distribueras till ett kluster, det tar flera minuter att ladda ned avbildningen beroende på storleken.

I Azure Marketplace, söker du efter *lösning för övervakning av behållare* och skapa den **lösning för övervakning av behållare** resultat som ska hämtas, under övervakning + Management kategori.

![Lägga till behållare lösning](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

I steget Skapa begär en OMS-arbetsyta. Markera det som har skapats med distributionen ovan. Det här steget lägger till en behållare lösning OMS-arbetsyta och identifieras automatiskt av OMS-agenten distribueras av mallen. Agenten börjar samla in data på behållare processer i kluster och mindre än 15 minuter eller så bör du se lösningen lysa med data enligt bilden på instrumentpanelen ovan.


## <a name="next-steps"></a>Nästa steg

Utforska följande OMS-verktyg och alternativ för att anpassa en arbetsyta för dina behov:

* För lokala kluster erbjuder OMS Gateway (HTTP framåt Proxy) som kan användas för att skicka data till OMS. Läs mer om att [ansluta datorer utan Internetanslutning till OMS med OMS-Gateway](../log-analytics/log-analytics-oms-gateway.md)
* Konfigurera OMS att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys