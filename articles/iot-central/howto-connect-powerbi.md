---
title: Visualisera dina Azure IoT Central-data i en Power BI-instrumentpanel | Microsoft Docs
description: Använda Azure IoT Central Analytics Power BI-lösningsmall för att visualisera och analysera IoT Central-data.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 5cb55e73b379b909811bde728d2ab39e29635bf5
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190707"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualisera och analysera dina Azure IoT Central-data i en Power BI-instrumentpanel

![Power BI-lösning mallens pipeline](media/howto-connect-powerbi/iot-continuous-data-export.png)

Använd Azure IoT Central Analytics Power BI-lösningsmall för att skapa en kraftfull Power BI-instrumentpanel för att övervaka prestanda för dina IoT-enheter. I Power BI-instrumentpanelen kan du:
- Spåra hur mycket data som dina enheter skickar över tid
- Jämför datavolym mellan telemetri, tillstånd och händelser
- Identifiera enheter som rapporterar massor av mätning av faktisk användning
- Se historiska trender för enheten mätning av faktisk användning
- Identifiera problematiska enheter som skickar stora mängder kritiska händelser

Den här lösningsmallen ställer in den pipeline som hämtar data i Azure Blob storage-kontot från [löpande dataexport](howto-export-data.md). Dessa data flödar genom att Azure Functions, Azure Data Factory och Azure SQL Database som kan bearbeta och transformera data så att visualiseras och analyseras i en Power BI-rapport som du kan hämta som en PBIX-fil. Alla dessa resurser skapas i din Azure-prenumeration, så du kan anpassa varje komponent för att passa dina behov. Den här lösningsmallen är helt öppen källkod, så du kan läsa mer om arkitekturen och bygg ut lösningen genom att besöka den [Github-lagringsplatsen](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

**[Hämta Azure IoT Central Analytics lösningsmallen från Microsoft AppSource.](https://aka.ms/iotcentralpowerbisolutiontemplate)**

## <a name="prerequisites"></a>Förutsättningar
Konfigurera mallen kräver följande:
- Åtkomst till en Azure-prenumeration
- Exporterade data med hjälp av [löpande dataexport](howto-export-data.md) från din IoT Central-app. Vi rekommenderar att du aktiverar mått, enheter och enheten mall-strömmarna för att få ut mest av Power BI-instrumentpanelen.
- Power BI Desktop (senaste versionen)
- Power BI Pro (om du vill dela instrumentpanelen med andra)

## <a name="reports"></a>Rapporter

Två rapporter genereras automatiskt. 

Den första rapporten visar en historisk översikt över mått som rapporteras av enheter och delar upp de olika typerna av mätning av faktisk användning och enheter som har skickat det högsta antalet för mätning av faktisk användning.

![Power BI-rapportsida 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Den andra rapporten aktivitetsgruppsrapport djupare i händelser och visar en historisk vy över fel och varningar som rapporteras. Den visar även vilka enheter helt upp rapporterar det högsta antalet händelser samt specifikt felhändelser och varningshändelser.

![Power BI-rapportsida 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>Resurser

Gå till AppSource för att hämta den [lösningsmall för Azure IoT Central Analytics](https://aka.ms/iotcentralpowerbisolutiontemplate).

Gå till den [Github-lagringsplatsen](https://aka.ms/iotcentralgithubpowerbisolutiontemplate) vill veta mer om arkitekturen och bygg ut lösningen.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att visualisera dina data i Power BI, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hantera enheter](howto-manage-devices.md)
