---
title: Ansluta Azure Defender för IoT till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Azure Defender (tidigare Azure Security Center) för IoT-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659621"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Anslut dina data från Azure Defender (tidigare Azure Security Center) för IoT till Azure Sentinel 


> [!IMPORTANT]
> Azure Defender för IoT data Connector är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Använd Azure Defender för IoT Connector för att strömma alla Azure Defender för IoT-händelser till Azure Sentinel. 

## <a name="prerequisites"></a>Krav

- **Läs** -och **Skriv** behörighet på arbets ytan där Azure Sentinel har distribuerats
- **Azure Defender för IoT** måste vara **aktiverat** på dina relevanta IoT Hub
- **Läs** -och **Skriv** behörighet för **Azure-IoT Hub** som du vill ansluta
- **Läs** -och **Skriv** behörighet för **Azure IoT Hub resurs gruppen**

## <a name="connect-to-azure-defender-for-iot"></a>Ansluta till Azure Defender för IoT

1. I Azure Sentinel väljer du **data kopplingar** och väljer sedan **Azure Defender för IoT** (kan fortfarande anropas Azure Security Center för IoT) från galleriet.
1. Klicka på **Öppna kopplings sida**i det nedre högra fönstret. 
1. Klicka på **Anslut**, bredvid varje IoT Hub prenumeration vars aviseringar och enhets aviseringar du vill strömma till Azure Sentinel. 
    - Om Azure Defender för IoT inte är aktiverat på hubben visas ett **aktiverings** varnings meddelande. Klicka på länken **Aktivera** för att starta tjänsten. 
1. Du kan välja om du vill att aviseringarna från Azure Defender för IoT automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter**väljer du **Aktivera** för att aktivera standard analys regeln för att skapa incidenter automatiskt från aviseringar som genereras i den anslutna säkerhets tjänsten. Den här regeln kan ändras eller redige ras under **analys**  >  **aktiva** regler.

> [!NOTE]
> Det kan ta lite tid för Hub-listan att uppdateras efter att anslutningarna har ändrats. 

## <a name="log-analytics-alert-display"></a>Log Analytics aviserings visning

Så här använder du det relevanta schemat i Log Analytics för att Visa Azure Defender för IoT-aviseringar:

1. Öppna **loggar**  >  **SecurityInsights**  >  **SecurityAlert**eller Sök efter **SecurityAlert**. 
2. Filtrera om du bara vill se Azure Defender för IoT-genererade aviseringar med följande KQL-filter:

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>Service anmärkningar

När du har anslutit en IoT Hub är Hub-data tillgängliga i Azure Sentinel cirka 15 minuter senare.


## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure Defender för IoT-data till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
