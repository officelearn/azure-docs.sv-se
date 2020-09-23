---
title: Konfigurera Azure Sentinel för Defender för IoT (för hands version)
description: Förklarar hur du konfigurerar Azure Sentinel för att ta emot data från din Defender for IoT-lösning.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 71147352c5b75195ed0dff2b05acc5315f3183cc
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941901"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Anslut dina data från Defender för IoT till Azure Sentinel (för hands version)

Azure Security Center för IoT data Connector i Azure Sentinel är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

I den här guiden får du lära dig hur du ansluter dina Defender för IoT-data till Azure Sentinel.

> [!div class="checklist"]
> * Förutsättningar
> * Inställningar för anslutning
> * Log Analytics aviserings vy

Anslut aviseringar från Defender för IoT och strömma dem direkt till Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha **Läs** -och **Skriv** behörighet för arbets ytan.
- **Defender för IoT** måste vara **aktiverat** på dina relevanta IoT Hub (er).
- Du måste ha både **Läs** -och **Skriv** behörighet för **Azure-IoT Hub** du vill ansluta.
- Du måste också ha **Läs** -och **Skriv** behörighet för **Azure IoT Hub resurs gruppen**.

> [!NOTE]
> Du måste ha Azure Security Center standard nivå licensiering som körs på din prenumeration för att skicka allmänna Azure Resource-aviseringar. Med den kostnads fria nivå licensiering som krävs för Defender för IoT vidarebefordras bara Defender för IoT-relaterade aviseringar till Azure Sentinel.

## <a name="connect-to-defender-for-iot"></a>Ansluta till Defender för IoT

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Defender för IoT** .
1. Klicka på **Öppna kopplings sida**längst ned i den högra rutan.
1. Klicka på **Anslut**, bredvid varje IoT Hub prenumeration vars aviseringar och enhets aviseringar du vill strömma till Azure Sentinel.
    - Om Defender för IoT inte är aktiverat på den hubben visas ett aktiverings varnings meddelande. Klicka på länken **Aktivera** för att starta och aktivera tjänsten.
1. Du kan välja om du vill att aviseringar från Defender för IoT automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter**väljer du **Aktivera** för att aktivera regeln för att automatiskt skapa incidenter från de genererade aviseringarna.  Den här regeln kan ändras eller redige ras under **analys**  >  **aktiva** regler.

> [!NOTE]
>Det kan ta 10 sekunder eller mer att uppdatera Hub-listan när du har ändrat anslutnings ändringarna.

## <a name="log-analytics-alert-display"></a>Log Analytics aviserings visning

Så här använder du det relevanta schemat i Log Analytics för att Visa Defender för IoT-aviseringar:

1. Öppna **loggar**  >  **SecurityInsights**  >  **SecurityAlert**eller Sök efter **SecurityAlert**.
1. Filtrera om du bara vill visa Defender för IoT-genererade aviseringar med följande KQL-filter:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Service anmärkningar

När du har anslutit en IoT Hub är Hub-data tillgängliga i Azure Sentinel cirka 15 minuter senare.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter till Defender för IoT till Azure Sentinel. Mer information om hot identifiering och säkerhets data åtkomst finns i följande artiklar:

- Lär dig hur du använder Azure Sentinel för att [få insyn i dina data och potentiella hot](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Lär dig hur du [kommer åt dina IoT-säkerhetsdata](how-to-security-data-access.md)
