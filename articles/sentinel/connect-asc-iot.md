---
title: Ansluta Azure Security Center för IoT till Azure Sentinel | Microsoft-dokument
description: Lär dig hur du ansluter Azure Security Center för IoT-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588645"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Ansluta dina data från Azure Security Center för IoT till Azure Sentinel 


> [!IMPORTANT]
> Azure Security Center för IoT-dataappen är för närvarande i offentlig förhandsversion. Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Använd Azure Security Center för IoT-anslutningsappen för att strömma alla dina Azure Security Center för IoT-händelser till Azure Sentinel. 

## <a name="prerequisites"></a>Krav

- **Läs-** och **skrivbehörigheter** på arbetsytan som Azure Sentinel distribueras till
- **Azure Security Center för IoT** måste **vara aktiverat** på dina relevanta IoT-hubber
- **Läs-** och **skrivbehörigheter** för **Azure IoT Hub** som du vill ansluta
- **Läs-** och **skrivbehörigheter** för **resursgruppen Azure IoT Hub**

> [!NOTE]
> Även om du måste **Standard** aktivera Azure Security Center Standard-nivålicensen på din prenumeration för att strömma IoT-resursaviseringar till Azure Sentinel, behöver du bara aktivera Azure Security Center Free-nivålicensen på din prenumeration för att visa Azure Security Center för IoT-aviseringar i Azure Sentinel. **Free** 

## <a name="connect-to-azure-security-center-for-iot"></a>Ansluta till Azure Security Center för IoT

1. I Azure Sentinel väljer du **Data-kopplingar** och klickar sedan på **Azure Security Center för IoT-panel.**
1. Klicka på **Öppna kopplingssida**i det nedre högra fönstret . 
1. Klicka på **Anslut**bredvid varje IoT Hub-prenumeration vars aviseringar och enhetsaviseringar som du vill strömma till Azure Sentinel. 
    - Om Azure Security Center för IoT inte är aktiverat på den hubben visas ett **aktivera** varningsmeddelande. Klicka på länken **Aktivera** för att starta tjänsten. 
1. Du kan bestämma om du vill att aviseringarna från Azure Security Center för IoT automatiskt ska generera incidenter i Azure Sentinel. Under **Skapa incidenter**väljer du **Aktivera** för att aktivera standardalytic-regeln för att skapa incidenter automatiskt från aviseringar som genereras i den anslutna säkerhetstjänsten. Den här regeln kan ändras eller redigeras enligt **Analytics** > **Active-regler.**

> [!NOTE]
> Det kan ta lite tid innan hubblistan uppdateras efter att anslutningsändringar har gjort det. 

## <a name="log-analytics-alert-display"></a>Logganalysvarningsvisning

Så här använder du det relevanta schemat i Logganalys för att visa Azure Security Center för IoT-aviseringar:

1. Öppna **loggar** > **SecurityInsights** > **SecurityAlert**eller sök efter **SecurityAlert**. 
2. Filter om du bara vill visa Azure Security Center för IoT-genererade aviseringar med följande kql-filter:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Serviceanteckningar

När du har anslutit en IoT Hub är hubbdata tillgängliga i Azure Sentinel cirka 15 minuter senare.


## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure Security Center för IoT-data till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.
