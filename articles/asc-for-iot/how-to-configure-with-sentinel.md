---
title: Konfigurera Sentinel (förhandsgranskning)
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311306"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Ansluta dina data från Azure Security Center för IoT till Azure Sentinel (förhandsversion)

> [!IMPORTANT]
> Azure Security Center för IoT-dataappen i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här guiden kan du lära dig hur du ansluter dina Azure Security Center för IoT-data till Azure Sentinel.

> [!div class="checklist"]
> * Krav
> * Inställningar för anslutning
> * Logganalysvarningsvy

Anslut aviseringar från Azure Security Center för IoT och strömma dem direkt till Azure Sentinel.

## <a name="prerequisites"></a>Krav

- Du måste ha **behörigheten Arbetsytan läs-** och skrivbehörighet. **write**
- **Azure Security Center för IoT** måste **vara aktiverat** på dina relevanta IoT-hubben.
- Du måste ha både **läs-** och skrivbehörighet för **Azure IoT Hub** som du vill ansluta. **write**
- Du måste också ha **läs-** och skrivbehörighet för **write** **resursgruppen Azure IoT Hub**.

> [!NOTE]
> Du måste ha Azure Security Center Standard-nivålicensiering som körs på din prenumeration för att skicka allmänna Azure-resursaviseringar. Med den kostnadsfria nivålicensiering som krävs för Azure Security Center för IoT vidarebefordras endast Azure Security Center för IoT-relaterade aviseringar till Azure Sentinel.

## <a name="connect-to-azure-security-center-for-iot"></a>Ansluta till Azure Security Center för IoT

1. I Azure Sentinel väljer du **Data-kopplingar** och klickar sedan på **Azure Security Center för IoT-panel.**
1. Klicka på **Öppna kopplingssida**längst ned i den högra rutan .
1. Klicka på **Anslut**bredvid varje IoT Hub-prenumeration vars aviseringar och enhetsaviseringar som du vill strömma till Azure Sentinel.
    - Om Azure Security Center för IoT inte är aktiverat på den hubben visas ett aktivera varningsmeddelande. Klicka på länken **Aktivera** för att starta och aktivera tjänsten.
1. Du kan bestämma om du vill att aviseringarna från Azure Security Center för IoT automatiskt ska generera incidenter i Azure Sentinel. Under **Skapa incidenter**väljer du **Aktivera** för att aktivera regeln för att automatiskt skapa incidenter från genererade aviseringar.  Den här regeln kan ändras eller redigeras enligt **Analytics** > **Active-regler.**

> [!NOTE]
>Det kan ta 10 sekunder eller mer att uppdatera hubblistan efter att ha gjort anslutningsändringar.

## <a name="log-analytics-alert-display"></a>Logganalysvarningsvisning

Så här använder du det relevanta schemat i Logganalys för att visa Azure Security Center för IoT-aviseringar:

1. Öppna **loggar** > **SecurityInsights** > **SecurityAlert**eller sök efter **SecurityAlert**.
1. Filter om du bara vill visa Azure Security Center för IoT-genererade aviseringar med följande kql-filter:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Serviceanteckningar

När du har anslutit en IoT Hub är hubbdata tillgängliga i Azure Sentinel cirka 15 minuter senare.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure Security Center för IoT till Azure Sentinel. Mer information om identifiering av hot och åtkomst till säkerhetsdata finns i följande artiklar:

- Lär dig hur du använder Azure Sentinel för att [få insyn i dina data och potentiella hot](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Lär dig hur du [kommer åt dina IoT-säkerhetsdata](how-to-security-data-access.md)
