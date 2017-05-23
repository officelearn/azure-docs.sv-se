---
title: "Översikt över Microsoft Azure IoT Suite | Microsoft Docs"
description: "Översikt över hur Azure IoT Suite levererar förkonfigurerade IoT-lösningar för att samla in, analysera och lagra data, skapa visualiseringar och integrera med andra system."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c57c0ddbe8e33d77c6f07f58dfe3236733f6c62f
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="overview-of-azure-iot-suite"></a>Översikt över Azure IoT Suite

Azures tjänster för Sakernas Internet (IoT, Internet of Things) tillhandahåller en rad intressanta funktioner. Med dessa tjänster i företagsklass kan du:

* Samla in data från enheter
* Analysera dataströmmar i rörelse
* Lagra och skicka frågor mot stora datamängder
* Visualisera både realtidsdata och historiska data
* Integrera med back office-system
* Hantera dina enheter

För att leverera dessa funktioner paketerar Azure IoT Suite samman flera Azure-tjänster med anpassade tillägg i form av *förkonfigurerade lösningar*. Dessa förkonfigurerade lösningar är grundläggande implementeringar av vanliga IoT-lösningsmönster som kan minska den tid det tar att leverera IoT-lösningar. Med [programutvecklingspaketen för IoT][lnk-sdks] kan du anpassa och utöka dessa lösningar efter dina egna behov. Du kan också använda dessa lösningar som exempel eller mallar när du utvecklar nya IoT-lösningar.

Följande videoklipp innehåller en introduktion till Azure IoT Suite:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Azure IoT-tjänster i Azure IoT Suite
De förkonfigurerade lösningarna använder vanligtvis följande tjänster:

* Kärnan i Azure IoT Suite är [Azure IoT Hub][lnk-iot-hub]-tjänsten. Den här tjänsten tillhandahåller funktioner för meddelandehantering från ”enhet till moln” och från ”moln till enhet” och fungerar som en gateway till molnet och andra viktiga IoT Suite-tjänster. Tjänsten gör att du kan ta emot meddelanden från dina enheter i hög skala och skicka kommandon till dina enheter. Med tjänsten kan du även [hantera dina enheter][lnk-device-management]. Du kan till exempel konfigurera, starta om eller utföra en fabriksåterställning på en eller flera enheter som är anslutna till hubben.
* [Azure Stream Analytics][lnk-asa] tillhandahåller analys av data i rörelse. IoT Suite använder den här tjänsten för att bearbeta inkommande telemetri, utföra aggregeringar och identifiera händelser. De förkonfigurerade lösningarna använder också Stream Analytics för att bearbeta informationsmeddelanden som innehåller data, till exempel metadata eller kommandosvar från enheter. Lösningarna använder Stream Analytics för att hantera meddelanden från dina enheter och leverera dem till andra tjänster.
* [Azure Storage][lnk-azure-storage] och [Azure Cosmos DB][lnk-document-db] tillhandahåller funktioner för datalagring. De förkonfigurerade lösningarna använder Blob Storage för att lagra telemetri och för att göra den tillgänglig för analys. Lösningarna använda Cosmos DB för att lagra metadata om enheter och för att aktivera enhetshanteringsfunktionerna i lösningarna.
* [Azure Web Apps][lnk-web-apps] och [Microsoft Power BI][lnk-power-bi] tillhandahåller visualiseringsfunktioner. Flexibiliteten i Power BI gör att du snabbt kan skapa egna interaktiva instrumentpaneler som använder IoT Suite-data.

En översikt över arkitekturen i en typisk IoT-lösning finns i [Microsoft Azure och Sakernas Internet (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>Förkonfigurerade lösningar

IoT Suite erbjuder förkonfigurerade lösningar som hjälper dig att snabbt komma igång med och utforska vanliga IoT-scenarier som *Fjärrövervakning* och *Förebyggande underhåll* och *Ansluten fabrik*. Du kan distribuera lösningarna till din Azure-prenumeration och sedan köra ett komplett IoT-scenario från slutpunkt till slutpunkt.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över vad IoT Suite kan göra och dess huvudkomponenter kan du lära dig mer om de förinställda lösningarna i IoT Suite. Mer information finns i [Var är förkonfigurerade lösningar i Azure IoT?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md

