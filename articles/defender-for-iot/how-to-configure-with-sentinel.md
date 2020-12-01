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
ms.openlocfilehash: c5c0f74ed8a5688b20eea4e74f747d3ff6dd0e63
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340007"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Anslut dina data från Defender för IoT till Azure Sentinel (för hands version)

Azure Defender för IoT data Connector i Azure Sentinel är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

I den här guiden får du lära dig hur du ansluter dina Defender för IoT-data till Azure Sentinel.

> [!div class="checklist"]
> * Förutsättningar
> * Inställningar för anslutning
> * Log Analytics aviserings vy

Anslut aviseringar från Defender för IoT och strömma dem direkt till Azure Sentinel.

Genom att mer tätt integrera Azure Defender för IoT med Azure Sentinel, de första molnbaserade SIEM och de första SIEM med inbyggd IoT och annan säkerhet, ger Microsoft en enklare metod för enhetlig säkerhet i IT och i industriella nätverk. I kombination med Azure Sentinel Machine Learning gör den här integrationen att organisationer snabbt kan identifiera angrepp på många faser som ofta passerar IT-och-gränser. Azure Defender för IoT: s integrering med Azure Sentinel-funktionerna för säkerhets dirigering, automation och Response (SOAR) möjliggör dessutom automatiserad respons och förebyggande användning av inbyggda spel böcker-optimerade. 

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha **Läs** -och **Skriv** behörighet för arbets ytan.
- **Defender för IoT** måste vara **aktiverat** på dina relevanta IoT Hub (er).
- Du måste ha både **Läs** -och **Skriv** behörighet för **Azure-IoT Hub** du vill ansluta.
- Du måste också ha **Läs** -och **Skriv** behörighet för **Azure IoT Hub resurs gruppen**.


## <a name="connect-to-defender-for-iot"></a>Ansluta till Defender för IoT

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Defender för IoT** .
1. Klicka på **Öppna kopplings sida** längst ned i den högra rutan.
1. Klicka på **Anslut**, bredvid varje IoT Hub prenumeration vars aviseringar och enhets aviseringar du vill strömma till Azure Sentinel.
    - Om Defender för IoT inte är aktiverat på den hubben visas ett aktiverings varnings meddelande. Klicka på länken **Aktivera** för att starta och aktivera tjänsten.
1. Du kan välja om du vill att aviseringar från Defender för IoT automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter** väljer du **Aktivera** för att aktivera regeln för att automatiskt skapa incidenter från de genererade aviseringarna.  Den här regeln kan ändras eller redige ras under **analys**  >  **aktiva** regler.

> [!NOTE]
>Det kan ta 10 sekunder eller mer att uppdatera Hub-listan när du har ändrat anslutnings ändringarna.

## <a name="using-log-analytics-for-alert-display"></a>Använda Log Analytics för aviserings visning

Så här använder du det relevanta schemat i Log Analytics för att Visa Defender för IoT-aviseringar:

1. Öppna **loggar**  >  **SecurityInsights**  >  **SecurityAlert** eller Sök efter **SecurityAlert**.
1. Filtrera om du bara vill visa Defender för IoT-genererade aviseringar med följande KQL-filter:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Service anmärkningar

När du har anslutit en IoT Hub är Hub-data tillgängliga i Azure Sentinel cirka 15 minuter senare.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter till Defender för IoT till Azure Sentinel. Mer information om hot identifiering och säkerhets data åtkomst finns i följande artiklar:

- Lär dig hur du använder Azure Sentinel för att [få insyn i dina data och potentiella hot](../sentinel/quickstart-get-visibility.md).

- Lär dig hur du [kommer åt dina IoT-säkerhetsdata](how-to-security-data-access.md)