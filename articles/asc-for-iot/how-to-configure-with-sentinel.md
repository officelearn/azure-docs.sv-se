---
title: Konfigurera Sentinel (för hands version)
description: Förklarar hur du konfigurerar Azure Sentinel för att ta emot data från din Azure Security Center för IoT-lösning.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: fe8e4c1b08f96e5f6b2fc7649f7a4361616b7c87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311306"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Anslut dina data från Azure Security Center för IoT till Azure Sentinel (för hands version)

> [!IMPORTANT]
> Azure Security Center för IoT data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här hand boken får du lära dig hur du ansluter Azure Security Center för IoT-data till Azure Sentinel.

> [!div class="checklist"]
> * Krav
> * Inställningar för anslutning
> * Log Analytics aviserings vy

Anslut aviseringar från Azure Security Center för IoT och strömma dem direkt till Azure Sentinel.

## <a name="prerequisites"></a>Krav

- Du måste ha **Läs** -och **Skriv** behörighet för arbets ytan.
- **Azure Security Center för IoT** måste vara **aktiverat** på dina relevanta IoT Hub (er).
- Du måste ha både **Läs** -och **Skriv** behörighet för **Azure-IoT Hub** du vill ansluta.
- Du måste också ha **Läs** -och **Skriv** behörighet för **Azure IoT Hub resurs gruppen**.

> [!NOTE]
> Du måste ha Azure Security Center standard nivå licensiering som körs på din prenumeration för att skicka allmänna Azure Resource-aviseringar. Med den kostnads fria nivå licens som krävs för Azure Security Center för IoT vidarebefordras bara Azure Security Center för IoT-relaterade aviseringar till Azure Sentinel.

## <a name="connect-to-azure-security-center-for-iot"></a>Anslut till Azure Security Center för IoT

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Azure Security Center för IoT** .
1. Klicka på **Öppna kopplings sida**längst ned i den högra rutan.
1. Klicka på **Anslut**, bredvid varje IoT Hub prenumeration vars aviseringar och enhets aviseringar du vill strömma till Azure Sentinel.
    - Om Azure Security Center för IoT inte är aktiverat på den hubben visas ett aktiverings varnings meddelande. Klicka på länken **Aktivera** för att starta och aktivera tjänsten.
1. Du kan välja om du vill att aviseringarna från Azure Security Center för IoT automatiskt ska generera incidenter i Azure Sentinel. Under **skapa incidenter**väljer du **Aktivera** för att aktivera regeln för att automatiskt skapa incidenter från de genererade aviseringarna.  Den här regeln kan ändras eller redige ras under **analys** > **aktiva** regler.

> [!NOTE]
>Det kan ta 10 sekunder eller mer att uppdatera Hub-listan när du har ändrat anslutnings ändringarna.

## <a name="log-analytics-alert-display"></a>Log Analytics aviserings visning

Om du vill använda det relevanta schemat i Log Analytics för att Visa Azure Security Center för IoT-aviseringar:

1. Öppna **loggar** > **SecurityInsights** > **SecurityAlert**eller Sök efter **SecurityAlert**.
1. Filtrera om du bara vill se Azure Security Center för IoT-genererade aviseringar med följande KQL-filter:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Service anmärkningar

När du har anslutit en IoT Hub är Hub-data tillgängliga i Azure Sentinel cirka 15 minuter senare.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure Security Center för IoT till Azure Sentinel. Mer information om hot identifiering och säkerhets data åtkomst finns i följande artiklar:

- Lär dig hur du använder Azure Sentinel för att [få insyn i dina data och potentiella hot](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Lär dig hur du [kommer åt dina IoT-säkerhetsdata](how-to-security-data-access.md)
