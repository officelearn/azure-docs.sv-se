---
title: Visualisera dina Azure IoT Central-data på en Power BI instrument panel | Microsoft Docs
description: Använd Power BI lösning för Azure IoT Central för att visualisera och analysera dina IoT Central data.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.openlocfilehash: dcfabbfe5bf807e8820fa14b6b8234d276578dcc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880077"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard-preview-features"></a>Visualisera och analysera dina Azure IoT Central-data i en Power BI instrument panel (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Det här avsnittet gäller för administratörer.*

![Pipeline för Power BI lösning](media/howto-connect-powerbi-pnp/iot-continuous-data-export.png)

Använd Power BI lösning för Azure IoT Central för att skapa en kraftfull Power BI-instrumentpanel för att övervaka prestanda för dina IoT-enheter. I Power BI instrument panelen kan du:
- Spåra hur mycket data dina enheter skickar med tiden
- Jämför data volymer mellan telemetri, tillstånd och händelser
- Identifiera enheter som rapporterar partier av mått
- Studera historiska trender för enhets mått
- Identifiera problematiska enheter som skickar massor av kritiska händelser

Den här lösningen konfigurerar pipelinen som tar data i ditt Azure Blob Storage-konto från [kontinuerlig data export](howto-export-data-blob-storage-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Dessa data flödar genom till Azure Functions, Azure Data Factory och Azure SQL Database för att bearbeta och transformera data. Utdata kan visualiseras och analyseras i en Power BI rapport som du kan ladda ned som en PBIX-fil. Alla dessa resurser skapas i din Azure-prenumeration så att du kan anpassa varje komponent så att den passar dina behov.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Hämta [Power BI-lösningen för Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) från Microsoft AppSource.

## <a name="prerequisites"></a>Förutsättningar
För att ställa in lösningen krävs följande:
- Åtkomst till en Azure-prenumeration
- Exporterade data med [kontinuerlig data export](howto-export-data-blob-storage-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) från din IoT Central-app. Vi rekommenderar att du aktiverar mått, enheter och mallar för enhets mallar för att få ut mesta möjliga av Power BI-instrumentpanelen.
- Power BI Desktop (senaste versionen)
- Power BI Pro (om du vill dela instrument panelen med andra)

## <a name="reports"></a>Rapporter

Två rapporter genereras automatiskt. 

Den första rapporten visar en historisk vy över mått som rapporteras av enheter och avbryter de olika typerna av mätningar och enheter som har skickat det högsta antalet mått.

![Power BI rapport sida 1](media/howto-connect-powerbi-pnp/template-page1-hasdata.PNG)

Den andra rapporten dykningar djupare i händelser och visar en historisk vy över fel och varningar som rapporter ATS. Det visar också vilka enheter som rapporterar det högsta antalet händelser, samt specifika fel händelser och varnings händelser.

![Power BI rapport sida 2](media/howto-connect-powerbi-pnp/template-page2-hasdata.PNG)

## <a name="architecture"></a>Arkitektur
Alla resurser som har skapats kan nås i Azure Portal. Allt ska vara under en resurs grupp.

![Azure Portal-vy över resurs grupp](media/howto-connect-powerbi-pnp/azure-deployment.PNG)

De olika resurserna och hur de används beskrivs nedan.

### <a name="azure-functions"></a>Azure Functions
Azure Function-appen utlöses varje gång en ny fil skrivs till Blob Storage. Funktionerna extraherar fälten inom varje mått-, enhets-och enhets mal lins-fil och fyller flera mellanliggande SQL-tabeller som ska användas av Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory ansluter till SQL-databasen som en länkad tjänst. Den kör lagrade procedur aktiviteter som bearbetar data och lagrar dem i analys tabellerna.

### <a name="azure-sql-database"></a>Azure SQL Database
Tabellerna skapas automatiskt för att fylla i standard rapporterna. Utforska dessa scheman i Power BI och du kan bygga dina egna visualiseringar för dessa data.

| Tabellnamn |
|------------|
|[Analytics]. Mått|
|[Analytics]. Kömeddelanden|
|[Stage]. Mått|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[analytics].[Devices]|
|[analytics].[DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Beräknade kostnader

Här är en uppskattning av Azure-kostnaderna (Azure Function, Data Factory, Azure SQL) som ingår. Alla priser är i USD. Tänk på att priserna varierar beroende på region, så du bör alltid söka efter de senaste priserna för de enskilda tjänsterna för att få de faktiska priserna.
Följande standardvärden har angetts för dig i mallen (du kan ändra vad som händer när du har ställt in dem):

- Azure Functions: App Service plan S1, $74.40/månad
- Azure SQL S1, ~ $30/månad

Vi rekommenderar att du bekantar dig med de olika pris alternativen och hur du anpassar saker efter dina behov.

## <a name="resources"></a>Resurser

Besök AppSource för att få [Power BIs lösning för Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du visualiserar dina data i Power BI, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Hantera enheter](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)