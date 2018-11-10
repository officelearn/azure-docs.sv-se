---
title: Självstudie om att övervaka dina IoT-enheter via en Azure-lösning | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du övervakar IoT-enheter med lösningsacceleratorn Fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/26/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 563c7a74d307a2c842680a031cf74b4a05ba800f
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138566"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Självstudier: Övervaka dina IoT-enheter

I den här självstudiekursen använder du lösningsacceleratorn Fjärrövervakning för att övervaka dina anslutna IoT-enheter. Du använder lösningens instrumentpanel för att visa telemetri, enhetsinformation, aviseringar och KPI:er.

I självstudien används två simulerade lastbilsenheter som skickar telemetri om plats, hastighet och lasttemperatur. Lastbilarna hanteras av en organisation som heter Contoso och är anslutna till lösningsacceleratorn Fjärrövervakning. Som Contoso-operatör behöver du övervaka plats och beteende på fältet för en av dina lastbilar (truck-02).

I den här kursen för du göra följande:

>[!div class="checklist"]
> * Filtrera enheterna på instrumentpanelen
> * Visa telemetri i realtid
> * Visa enhetsinformation
> * Visa aviseringar från enheterna
> * Visa system-KPI:er

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Välja enheter som ska visas

Du kan välja vilka anslutna enheters som ska visas på sidan **Instrumentpanel** med hjälp av filter. Om du bara vill visa enheter av typen **Truck** (Lastbil) väljer du de inbyggda filtret **Trucks** (Lastbilar) i filterlistrutan:

[![Filtrera lastbilar på instrumentpanelen](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

När du använder ett filter visas endast de enheter som matchar filtervillkoren på kartan och i telemetripanelen. Du kan se att det finns två lastbilar som är anslutna till lösningsacceleratorn, inklusive truck-02:

[![Bara lastbilar visas på kartan](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Du kan skapa, redigera och ta bort filter genom att klicka på **Hantera enhetsgrupper**.

## <a name="view-real-time-telemetry"></a>Visa telemetri i realtid

Lösningsacceleratorn kartlägger telemetridata i realtid på sidan **Instrumentpanel**. Högst upp i telemetridiagrammet visas tillgängliga telemetrityper för enheterna, inklusive truck-02, som valts av det aktuella filtret. Som standard visar diagrammet latitud för lastbilarna, och truck-02 verkar vara stillastående:

[![Telemetrityper för lastbil](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Visa temperaturtelemetri för lastbilarna genom att klicka på **Temperature** (Temperatur). Du kan se hur temperaturen för truck-02 har varierat under den senaste timmen:

[![Telemetrikartläggning av lastbilstemperatur](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>Visa kartan

På kartan visas information om de simulerade lastbilarna som har valts av det aktuella filtret. Du kan zooma och panorera kartan för att visa platserna i mer eller mindre detalj. Färgen på en enhetsikon på kartan visar om det finns aktiva **aviseringar** (mörkblå) eller **varningar** (röd) för enheten. En sammanfattning av **aviseringar** och **varningar** visas till vänster på kartan.

Du kan visa information för truck-02 genom att panorera och zooma kartan för att hitta den och sedan välja lastbilen på kartan. Klicka sedan på enhetsetiketten för att öppna panelen **Enhetsinformation**. Enhetsinformationen omfattar:

* Senaste telemetrivärden
* Metoder som enheten stöder
* Enhetsegenskaper

[![Visa enhetsinformation på instrumentpanelen](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Visa aviseringar

Panelen **Aviseringar** visar detaljerad information om de senaste aviseringarna från dina enheter. Aviseringarna från truck-02 tyder på att lasttemperaturen är högre än normalt:

[![Visa enhetsaviseringar på instrumentpanelen](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Du kan använda ett filter för att justera tidsintervallet för de senaste aviseringarna. Som standard visar panelen aviseringar från den senaste timmen.

## <a name="view-the-system-kpis"></a>Visa system-KPI:er

På sidan **Instrumentpanel** visas system-KPI:er beräknade av lösningsacceleratorn i panelen **Analys**:

[![KPI:er på instrumentpanelen](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

På instrumentpanelen visas tre KPI:er för aviseringarna som har valts av de aktuella filtren för aktuell enhet och tidsintervall:

* Antalet aktiva aviseringar för reglerna som utlöst flest aviseringar.
* Andelen aviseringar per enhetstyp.
* Procentandelen aviseringar som är kritiska.

För truck-02 är alla aviseringar varningar om en lasttemperatur som är högre än normalt.

Samma filter som har angett tidsintervallet för aviseringar och kontrollerar vilka enheter som visas bestämmer hur KPI:erna aggregeras. Som standard visar panelen KPI:er aggregerat under den senaste timmen.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen visades hur du använder sidan **Instrumentpanel** i lösningsacceleratorn Fjärrövervakning för att filtrera och övervaka de simulerade lastbilarna. Fortsätt till nästa självstudiekurs om du vill lära dig hur du använder lösningsacceleratorn till att identifiera problem med anslutna enheter.

> [!div class="nextstepaction"]
> [Identifiera problem med enheter som är anslutna till din övervakningslösning](iot-accelerators-remote-monitoring-automate.md)