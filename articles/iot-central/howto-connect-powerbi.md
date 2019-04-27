---
title: Visualisera dina Azure IoT Central-data i en Power BI-instrumentpanel | Microsoft Docs
description: Använda Power BI-lösning för Azure IoT Central för att visualisera och analysera IoT Central-data.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/15/2019
ms.topic: conceptual
ms.openlocfilehash: 322be1e13662d92a3cb0a805a9ccaacd05928f7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886816"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualisera och analysera dina Azure IoT Central-data i en Power BI-instrumentpanel

*Det här avsnittet gäller för administratörer.*

![Power BI-lösning pipeline](media/howto-connect-powerbi/iot-continuous-data-export.png)

Använd Power BI-lösning för Azure IoT Central för att skapa en kraftfull Power BI-instrumentpanel för att övervaka prestanda för dina IoT-enheter. I Power BI-instrumentpanelen kan du:
- Spåra hur mycket data som dina enheter skickar över tid
- Jämför datavolym mellan telemetri, tillstånd och händelser
- Identifiera enheter som rapporterar massor av mätning av faktisk användning
- Se historiska trender för enheten mätning av faktisk användning
- Identifiera problematiska enheter som skickar stora mängder kritiska händelser

Den här lösningen konfigurerar pipelinen som hämtar data i Azure Blob storage-kontot från [löpande Export av Data](howto-export-data.md). Dessa data flödar genom att Azure Functions, Azure Data Factory och Azure SQL Database för att bearbeta och transformera data. Utdata kan visualiseras och analyseras i en Power BI-rapport som du kan hämta som en PBIX-fil. Alla dessa resurser skapas i din Azure-prenumeration, så du kan anpassa varje komponent för att passa dina behov.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Hämta den [Power BI-lösning för Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) från Microsoft AppSource.

## <a name="prerequisites"></a>Nödvändiga komponenter
Installerar lösningen kräver följande:
- Åtkomst till en Azure-prenumeration
- Exporterade data med hjälp av [löpande Export av Data](howto-export-data.md) från din IoT Central-app. Vi rekommenderar att du aktiverar mått, enheter och enheten mall-strömmarna för att få ut mest av Power BI-instrumentpanelen.
- Power BI Desktop (senaste versionen)
- Power BI Pro (om du vill dela instrumentpanelen med andra)

## <a name="reports"></a>Rapporter

Två rapporter genereras automatiskt. 

Den första rapporten visar en historisk översikt över mått som rapporteras av enheter och delar upp de olika typerna av mätning av faktisk användning och enheter som har skickat det högsta antalet för mätning av faktisk användning.

![Power BI-rapportsida 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Den andra rapporten aktivitetsgruppsrapport djupare i händelser och visar en historisk vy över fel och varningar som rapporteras. Den visar även vilka enheter helt upp rapporterar det högsta antalet händelser samt specifikt felhändelser och varningshändelser.

![Power BI-rapportsida 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>Arkitektur
Alla resurser som har skapats kan nås i Azure-portalen. Allt ska vara i samma resursgrupp.

![Azure Portal-vy av resursgrupp](media/howto-connect-powerbi/azure-deployment.PNG)

Krav för varje resurs och hur du hämtar används beskrivs nedan.

### <a name="azure-functions"></a>Azure Functions
Azure Function-app hämtar utlöses varje gång en ny fil skrivs till Blob storage. Funktionerna extrahera fält inom varje mått, enheter och mallar enhetsfil och fyller på flera mellanliggande SQL-tabeller som ska användas av Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory ansluter till SQL-databasen som en länkad tjänst. Körs den lagrade proceduren aktiviteter som bearbetar informationen och lagra det i analysis-tabeller.

### <a name="azure-sql-database"></a>Azure SQL Database
Dessa tabeller skapas automatiskt för att fylla i standardrapporterna. Utforska dessa scheman i Power BI och du kan skapa egna visualiseringar på dessa data.

| Tabellnamn |
|------------|
|[analytics]. [Mätningar]|
|[analytics]. [Meddelanden]|
|[fas]. [Mätningar]|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[analytics].[Devices]|
|[analytics].[DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Beräknade kostnader

Här är en uppskattning av Azure-kostnader (Azure-funktion, Data Factory, Azure SQL) som ingår. Alla priser är i USD. Tänk på att priserna kan variera efter region, så du bör alltid söka efter de senaste priserna för de enskilda tjänsterna för att få de faktiska priserna.
Följande standarder ställs in för dig i mallen (du kan ändra någon av dessa när saker gör iordning):

- Azure Functions: App Service-plan S1, 74.40 $/ månad
- Azure SQL-S1, ~$30/month

Vi uppmanar dig att bekanta dig med olika prisalternativ och justera saker som passar dina behov.

## <a name="resources"></a>Resurser

Gå till AppSource för att hämta den [Power BI-lösning för Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att visualisera dina data i Power BI, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hantera enheter](howto-manage-devices.md)