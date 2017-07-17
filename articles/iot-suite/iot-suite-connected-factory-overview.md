---
title: "Översikt över Ansluten fabrik i Microsoft Azure IoT Suite | Microsoft Docs"
description: "En beskrivning av den förkonfigurerade lösningen Ansluten fabrik i Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 97da0f4dc5d5528bfb803eefe9037651939a8efd
ms.contentlocale: sv-se
ms.lasthandoff: 05/26/2017


---
# Kom igång med den förkonfigurerade lösningen Ansluten fabrik
<a id="get-started-with-the-connected-factory-preconfigured-solution" class="xliff"></a>

## Introduktion
<a id="introduction" class="xliff"></a>

Azure IoT Suites [förkonfigurerade lösningar][lnk-preconfigured-solutions] kombinerar flera Azure IoT-tjänster för att leverera lösningar från slutpunkt till slutpunkt som implementerar vanliga IoT-företagsscenarier. Den förkonfigurerade lösningen *Ansluten fabrik* ansluter till och övervakar dina industriella enheter. Med lösningen kan du analysera dataströmmar från dina enheter och öka produktiviteten och lönsamheten i verksamheten.

Den här självstudiekursen visar hur du etablerar den förkonfigurerade lösningen Ansluten fabrik. Vi går också igenom de grundläggande funktionerna i den förkonfigurerade lösningen. Du kan komma åt många av dessa funktioner från *instrumentpanelen* för lösningen som distribueras som en del av den förkonfigurerade lösningen:

![instrumentpanel för den förkonfigurerade lösningen ansluten fabrik][img-cf-home]

Du behöver en aktiv Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.

> [!NOTE]
> Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure][lnk_free_trial].
> 
> 

## Etablera lösningen
<a id="provision-the-solution" class="xliff"></a>

1. Logga in på azureiotsuite.com med din Azure-kontoinformation och skapa en lösning genom att klicka på **+**.
2. Klicka på **Välj** på panelen **Ansluten fabrik**.
3. Ange ett **lösningsnamn** för den förkonfigurerade anslutna fabriken för fjärrövervakning.
4. Välj den **prenumeration** och **region** som du vill använda för att etablera lösningen.
5. Klicka på **Skapa lösning** för att påbörja etableringen. Den här processen tar normalt flera minuter.

### Medan du väntar på att etableringsprocessen ska slutföras
<a id="while-you-wait-for-the-provisioning-process-to-complete" class="xliff"></a>

1. Klicka på ikonen för din lösning med statusen **Etablerar**.
2. Observera **etableringsstatusen** när Azure-tjänsterna distribueras i din Azure-prenumeration.
3. När etableringen har slutförts ändras statusen till **Klar**.
4. Klicka på ikonen så ser du informationen om din lösning i den högra rutan.

> [!NOTE]
> Om det uppstår några problem när du distribuerar den förkonfigurerade lösningen kan du läsa [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions] och [Vanliga frågor och svar][lnk-faq]. Om problemen kvarstår så skapa en tjänstbiljett på [portalen][lnk-portal].

Finns det något som du förväntar dig att se men som inte visas för din lösning? Lämna förslag på funktioner i [User Voice](https://feedback.azure.com/forums/321918-azure-iot).

## Scenarioöversikt
<a id="scenario-overview" class="xliff"></a>

När du distribuerar den förkonfigurerade lösningen Ansluten fabrik innehåller den redan resurser som hjälper dig att gå igenom ett vanligt industriscenario. I det här scenariot rapporterar flera fabriker (som är anslutna till lösningen) de datavärden som krävs för att beräkna utrustningseffektivitet (Overall Equipment Efficiency, OEE) och KPI: er (Key Performance Indicators). I avsnitten nedan får du information om följande:

* Övervaka fabrik, produktionslinjer, station OEE och KPI-värden
* Analysera telemetridata som genereras av dessa enheter med hjälp av Azure Time Series Insights
* Åtgärda problem vid aviseringar

En viktig egenskap i detta scenario är att du kan utföra alla dessa åtgärder via fjärranslutning från instrumentpanelen med lösningar. Du behöver inte fysisk åtkomst till enheterna.

## Visa instrumentpanelen för lösningen
<a id="view-the-solution-dashboard" class="xliff"></a>

På instrumentpanelen för lösningen kan du hantera den distribuerade lösningen. Det här är en hierarkisk representation av en global fabrikskonfiguration. Du kan till exempel se OEE och KPI:er, publicera nya noder för telemetri och åtgärdsaviseringar.

1. När etableringen har slutförts och panelen för din förkonfigurerade lösning visar statusen **Klar** klickar du på **Starta**. Därmed öppnas portalen för lösningen Ansluten fabrik på en ny flik.

    ![Starta den förkonfigurerade lösningen][img-launch-solution]

1. Som standard visar lösningsportalen *instrumentpanelen*. Du kan navigera till andra delar av portalen via menyn till vänster på sidan.

    ![Instrumentpanel för den förkonfigurerade lösningen Ansluten fabrik][cf-img-menu]

Följande information visas på instrumentpanelen:

* En panel med en **fabrikslista** som visar status, plats och aktuell produktionskonfiguration i lösningen. Första gången du kör lösningen finns det fyra simulerade enheter. Produktionslinjesimuleringen består av tre verkliga OPC UA-servrar per produktionslinje. Dessa utför simulerade uppgifter och delar data. Mer information om OPC UA finns i [Vanliga frågor och svar][lnk-faq].
* En **karta** visar platsen för alla enheter som är kopplade till lösningen. Lösningen kan använda Bing Maps-API:t för att rita information på kartan. Om Bing Maps Enterprise API är aktiverat för din prenumeration används den här funktionen automatiskt. I annat fall kan du se [vanliga frågor och svar][lnk-faq] för information om hur du gör kartan dynamisk.
* En panel för **aviseringar** som visar aviseringar som genereras när ett telemetri- eller OEE/KPI-värde överskrider ett visst tröskelvärde.
* En panel för **OEE** (Overall Equipment Efficiency) som visar OEE-värdena för hela företaget eller den fabrik, produktionslinje eller station du tittar på. Det här värdet sammanställs från stationsvyn till företagsnivån. OEE-bilden och dess beståndsdelar kan analyseras ytterligare.
* Panelen för **KPI:er** som visar antalet enheter som producerats och energi som förbrukas av hela företaget eller den fabrik, produktionslinje eller station som du visar. Dessa värden sammanställs från stationsvyn till företagsnivån.

## Visa fabriker
<a id="view-factories" class="xliff"></a>

Panelen för *fabriker* visar den geografiska platsen för alla fabriker i lösningen, deras status och den aktuella produktionskonfigurationen. Från listan över platser kan du navigera till andra nivåer i lösningshierarkin. Raderna i listan är hyperlänkar till information om produktionslinjerna på den platsen. Du kan söka vidare i informationen om produktionslinjen ned till vyn på stationsnivå. Du kan också använda ett filter för listan.

![Fabriker för den förkonfigurerade lösningen Ansluten fabrik][cf-img-factories] 

1. På **fabrikspanelen** visas fabrikslistan för lösningen.

2. I fabrikslistan visas inledningsvis sex fabriker som skapades vid etableringsprocessen. Du kan lägga till ytterligare simulerade och fysiska enheter till lösningen.

3. Om du vill visa information om en fabrik klickar du på raden i fabrikslistan.

4. Om du vill visa information om en produktionslinje klickar du på raden i listan.

5. Om du vill visa publicerade OPC UA-noder för en station i produktionslinjen klickar du på raden i listan.

6. Om du vill visa information om en viss nod i stationen klickar du på raden i listan. Den här åtgärden öppnar en kontextpanel med Time Series Insights-visualiseringar. Klicka på dessa diagram om du vill göra vidare analyser i Time Series Insights-utforskarmiljön.

## Visa karta
<a id="view-map" class="xliff"></a>

Om din prenumeration ger åtkomst till Bing Maps-API:t visar *fabrikskartan* geografisk plats och status för alla fabriker i lösningen. Klicka på de platser som visas på kartan för att visa mer detaljerad information för platsen.

![Karta för den förkonfigurerade lösningen Ansluten fabrik][cf-img-map]

## Visa aviseringar
<a id="view-alerts" class="xliff"></a>

Panelen för **aviseringshistorik** visar aviseringar som genereras på grund av ett rapporterat värde eller ett beräknat OEE/KPI-värde överskrider det angivna tröskelvärdet. Den här panelen visar aviseringar på varje nivå i hierarkin, från vyn på stationsnivå till vyn på global nivå. Aviseringarna innehåller en beskrivningen av aviseringen, datum, tid, plats och antal förekomster. Du kan få information om vad som orsakat aviseringen med hjälp av Time Series Insights-data. Time Series Insights-data visualiseras i aviseringar (om tillämpligt). Om du är administratör kan vidta du standardåtgärder för aviseringar, till exempel:

* Stänga aviseringen.
* Bekräfta aviseringen.

Du kan också kan du utföra mer komplexa åtgärder. För Pressure OPC UA-noden i sammansättningen kan du till exempel:

* Läsa in en webbsida i ett nytt webbläsarfönster för att visa mer information.
* Anropa en OPC UA-metod på enheten för att åtgärda orsaken till aviseringen.
* Välja att standardåtgärderna inte ska vara tillgängliga.

    ![Aviseringar för den förkonfigurerade lösningen Ansluten fabrik][cf-img-alerts]

> [!NOTE]
> De här aviseringarna genereras av regler som har angetts i en konfigurationsfil i den förinställda lösningen. Dessa regler kan generera aviseringar när OEE- eller KPI-värdena eller OPC UA-nodvärden överskrider de angivna tröskelvärdena.

1. **Aviseringspanelen** visar de aviseringar som genererats i den här lösningen.

2. Klicka på en avisering på aviseringspanelen om du vill se mer information om aviseringen.

3. Du kan analysera aviseringsinformation mer i detalj genom att klicka på diagrammet på aviseringspanelen för att öppna Time Series Insights-utforskarmiljön.

4. På aviseringspanelen finns flera åtgärder du kan vidta för aviseringen. Välj ett lämpligt alternativ och klicka på knappen för att utföra åtgärden.

## Visa OEE (Overall Equipment Efficiency)
<a id="view-overall-equipment-efficiency" class="xliff"></a>

OEE är nyckeltal för att mäta produktionseffektivitet. OEE är ett branschstandardmått som beräknas genom att multiplicera tillgänglighet, anläggningsutnyttjande och kvalitetsutbyte: OEE = tillgänglighet x anläggningsutnyttjande x kvalitetsutbyte.

![OEE för den förkonfigurerade lösningen Ansluten fabrik][cf-img-oee]

1. Om du vill visa OEE för någon nivå i hierarkin navigerar du till den vy som du vill visa. OEE för vyn visas på panelen tillsammans med de delar som utgör OEE-procentvärdet.

2. Om du vill analysera OEE mer i detalj för en nivå i hierarkin klickar du på procenttalet för OEE, tillgänglighet, anläggningsutnyttjande eller kvalitetsutbyte. En kontextpanel visas med Time Series Insights-visualiseringar som visar data från den senaste timmen, de senaste 24 timmarna och de senaste sju dagarna.

    ![TSI-visualisering för den förkonfigurerade lösningen Ansluten fabrik][cf-img-tsi-visualization]

3. Om du vill analysera aviseringsdata mer i detalj klickar du på diagrammet på aviseringspanelen. Den här åtgärden öppnar Time Series Insights-utforskarmiljön.

    ![TSI-utforskaren för den förkonfigurerade lösningen Ansluten fabrik][cf-img-tsi-explorer]

## Visa KPI: er
<a id="view-key-performance-indicators" class="xliff"></a>

Lösningen innehåller två KPI:er: *units per hour* (enheter per timme) och *energy used in kWh* (energi som används i kWh).

![KPI för den förkonfigurerade lösningen Ansluten fabrik][cf-img-kpi]

1. Om du vill visa enheter per timme eller energi som används för någon nivå i hierarkin navigerar du till den vy som du vill visa. Enheter per timme och energi som används visas på panelen.

2. Om du vill analysera enheter per timme eller energi som används mer i detalj för en nivå i hierarkidata klickar du antingen på mätaren för enheter som producerats eller energi som använts på **KPI**-panelen. En kontextpanel visas med Time Series Insights-visualiseringar som visar data från den senaste timmen, de senaste 24 timmarna och de senaste sju dagarna.

## Scenariogranskning
<a id="scenario-review" class="xliff"></a>

I det här scenariot övervakade du OEE- och KPI-värden på instrumentpanelen. Sedan använde du Time Series Insights för att visa mer information och detaljerade telemetridata för OEE och KPI:er för att kunna identifiera avvikelser. Du använde också aviseringspanelen för att visa problem med dina fabriker och du använde tillgängliga alternativ för att åtgärda orsaken till aviseringen.

## Andra funktioner
<a id="other-features" class="xliff"></a>

I följande avsnitt beskrivs ytterligare funktioner för den förkonfigurerade lösningen Ansluten fabrik som inte beskrivs som en del av scenariot ovan.

## Använda filter
<a id="apply-filters" class="xliff"></a>

1. Klicka på **sparren** för att visa en lista över tillgängliga filter på panelen med fabriksplatser eller på panelen med aviseringar.

2. Filterpanelen visas. 

    ![Filter för den förkonfigurerade lösningen Ansluten fabrik][cf-img-alert-filter]

3. Välj önskat filter. Du kan också skriva fri text i filterfälten om det behövs.

4. Sedan tillämpas filtret. Filterläget visas i instrumentpanelen med hjälp av en tratt som visas i tabellerna för fabriker och aviseringar.

    ![Filter för den förkonfigurerade lösningen Ansluten fabrik][cf-img-alert-filter-funnel]

    > [!NOTE]
    > Ett aktivt filter påverkar inte de OEE och KPI-värden som visas. Det filtrerar bara listinnehållet.

5. Om du vill ta bort ett filter klickar du på tratten och på filtret på kontextpanelen för filtret. Texten **Alla** visas i tabellerna för fabriker och aviseringar.

## Navigera i en OPC UA-serverläsare
<a id="browse-an-opc-ua-server" class="xliff"></a>

När du distribuerar den förkonfigurerade lösningen etableras automatiskt simulerade OPC UA-servrar som du kan navigerar i via serverläsaren. Dessa servrar är *simulerade OPC UA-servrar*. Med simulerade servrar kan du enkelt experimentera med den förkonfigurerade lösningen utan att du behöver distribuera verkliga, fysiska servrar. Om du vill ansluta en verklig OPC UA-server till lösningen kan du läsa självstudiekursen [Connect your OPC UA device to the connected factory preconfigured solution][lnk-connect-cf] (Ansluta OPC UA-enheten till den förkonfigurerade lösningen Ansluten fabrik).

1. Klicka på **fabriksikonen** i navigeringsfältet i instrumentpanelen.

    ![Serverläsare för den förkonfigurerade lösningen Ansluten fabrik][cf-img-server-browser]

2. Välj en av servrarna i den förkonfigurerade listan. Listan visar de servrar som är distribuerade åt dig i den förkonfigurerade lösningen.

    ![Serverurval för den förkonfigurerade lösningen Ansluten fabrik][cf-img-server-choice]

3. Klicka på **Connect** (Anslut) för att visa en dialogruta för säkerhet. För simuleringen är det säkert att klicka på **Proceed** (Fortsätt).

4. Klicka på någon av noderna i serverträdet så att den expanderas. Noder som publicerar telemetri har ett skalstreck bredvid sig.

    ![Serverträd för den förkonfigurerade lösningen Ansluten fabrik][cf-img-server-tree]

5. Högerklicka på ett objekt om du vill läsa, skriva, publicera eller anropa noden. Vilka åtgärder som är tillgängliga beror på dina behörigheter och nodens attribut. Läsalternativet visar en kontextpanel med värdet för den specifika noden. Skrivalternativet visar en kontextpanel där du kan ange ett nytt värde. Anropsalternativet visar en nod där du kan ange parametrar för anropet.

## Publicera en nod
<a id="publish-a-node" class="xliff"></a>

När du navigerar i en *simulerad OPC UA-server* kan du välja att publicera nya noder. Du kan analysera telemetri från dessa noder i lösningen. Med de *simulerade OPC UA-servrarna* kan du enkelt experimentera med den förkonfigurerade lösningen utan att du behöver distribuera verkliga fysiska enheter.

1. Bläddra till en nod i OPC UA-serverläsarträdet som du vill publicera.

2. Högerklicka på noden.

3. Välj **Publish** (Publicera).

    ![Publicera nod i Ansluten fabrik][cf-img-publish-node]

4. En kontextpanel visas som anger att publiceringen är klar. Noden visas i vyn på stationsnivå med en bock bredvid den.

    ![Slutförd publicering i den förkonfigurerade lösningen Ansluten fabrik][cf-img-publish-success]

## Kommando och kontroll
<a id="command-and-control" class="xliff"></a>

Med den anslutna fabriken kan du styra dina industriella enheter direkt från molnet. Du kan använda funktionen till att vidta åtgärder vid aviseringar som genereras av enheten. Du kan till exempel skicka ett kommando till enheten från molnet. Tillgängliga kommandon finns i noden **StationCommands** i OPC UA-serverläsarträdet. I det här scenariot öppnar du en övertrycksventil på en monteringsstation i en produktionslinje i München. För att kunna styra funktioner måste du ha rollen **Administratör** för distributionen av den förkonfigurerade lösningen.

1. Bläddra till noden **StationCommands** i OPC UA-serverläsarträdet.

2. Välj det kommando som du vill använda.

3. Högerklicka på noden.

4. Välj **Call** (Anropa).

    ![Anropskommando för den förkonfigurerade lösningen Ansluten fabrik][cf-img-call-command]

5. En kontextpanel visas som anger vilken metod du anropar och eventuell parameterinformation.

6. Välj **Call** (Anropa).

    ![Kontextpanel för anrop för den förkonfigurerade lösningen Ansluten fabrik][cf-img-call-context]

7. Kontextpanelen uppdateras för att informera dig att metodanropet har slutförts. Du kan verifiera att anropet har slutförts genom att läsa av värdet för trycknoden som uppdaterades av anropet.

    ![Slutfört anrop för den förkonfigurerade lösningen Ansluten fabrik][cf-img-call-success]


## I bakgrunden
<a id="behind-the-scenes" class="xliff"></a>

När du distribuerar en förkonfigurerad lösning skapar distributionsprocessen flera resurser i Azure-prenumerationen som du valt. Du kan visa dessa resurser på Azure-[portalen][lnk-portal]. Under distributionsprocessen skapas en **resursgrupp** med ett namn baserat på det namn som du valde för den förkonfigurerade lösningen:

![Förkonfigurerad lösning på Azure-portalen][img-cf-portal]

Du kan visa inställningarna för varje resurs genom att välja resursen i listan över resurser i resursgruppen.

Du kan också visa källkoden för den förkonfigurerade lösningen. Källkoden för den förkonfigurerade lösningen Ansluten fabrik finns i [azure-iot-connected-factory][lnk-cfgithub]-databasen på GitHub:

När du är klar kan du ta bort den förkonfigurerade lösningen från Azure-prenumerationen på webbplatsen [azureiotsuite.com][lnk-azureiotsuite]. På den här webbplatsen kan du enkelt ta bort alla resurser som etablerades när du skapade den förkonfigurerade lösningen.

> [!NOTE]
> För att vara säker på att du tar bort allt som hör till den förkonfigurerade lösningen tar du bort den från [azureiotsuite.com][lnk-azureiotsuite]. Ta inte bort resursgruppen i portalen.

## Nästa steg
<a id="next-steps" class="xliff"></a>

Nu när du har distribuerat en fungerande förkonfigurerad lösning kan du fortsätta och lära dig mer om IoT Suite genom att läsa följande artiklar:

* [Genomgång av den förkonfigurerade lösningen Ansluten fabrik][lnk-rm-walkthrough]
* [Ansluta enheten till den förkonfigurerade lösningen Ansluten fabrik][lnk-connect-cf]
* [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions]

[img-cf-home]:media/iot-suite-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-suite-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-suite-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-suite-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-suite-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-suite-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-suite-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-suite-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-suite-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-suite-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-suite-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-suite-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-suite-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-suite-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-suite-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-suite-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-suite-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-suite-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-suite-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-suite-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-suite-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
