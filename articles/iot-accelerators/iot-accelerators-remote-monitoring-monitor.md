---
title: Övervaka dina IoT-enheter via en Azure-lösning | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du övervakar IoT-enheter med lösningsacceleratorn Fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5f42ed0fa5362959e5619f2d550ca1ae3711ed65
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097469"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Självstudier: Övervaka dina IoT-enheter

I den här självstudiekursen använder du lösningsacceleratorn Fjärrövervakning för att övervaka dina anslutna IoT-enheter. Du använder lösningens instrumentpanel för att visa telemetri, enhetsinformation, aviseringar och KPI:er.

I självstudiekursen introduceras övervakningsfunktionerna med hjälp av två simulerade lastbilar. Lastbilarna hanteras av en organisation som heter Contoso och är anslutna till lösningsacceleratorn Fjärrövervakning. Som Contoso-operatör behöver du övervaka lastbilarnas plats och beteende på fältet.

I den här kursen för du göra följande:

>[!div class="checklist"]
> * Filtrera enheterna på instrumentpanelen
> * Visa telemetri i realtid
> * Visa enhetsinformation
> * Visa aviseringar från enheterna
> * Visa system-KPI:er

## <a name="prerequisites"></a>Krav

Om du vill följa den här självstudien behöver du en distribuerad instans av lösningsacceleratorn Fjärrövervakning i Azure-prenumerationen.

Om du inte har distribuerat lösningsacceleratorn Fjärrövervakning ännu bör du genomföra snabbstarten [Distribuera en molnbaserad fjärrövervakningslösning](quickstart-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Välja enheter som ska visas

Du kan välja vilka anslutna enheters som ska visas på sidan **Instrumentpanel** med hjälp av filter. Om du bara vill visa enheter av typen **Truck** (Lastbil) väljer du de inbyggda filtret **Trucks** (Lastbilar) i filterlistrutan:

[![Filtrera lastbilar på instrumentpanelen](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

När du använder ett filter visas på sidan **Instrumentpanel** bara de enheter på kartan som matchar filtervillkoren:

[![Bara lastbilar visas på kartan](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Filtret bestämmer även vilka enheter du ser i diagrammet **telemetridiagrammet**:

[![Telemetridata för lastbilar visas på instrumentpanelen](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-expanded.png#lightbox)

Du kan skapa, redigera och ta bort filter genom att välja **Hantera enhetsgrupper**.

## <a name="view-real-time-telemetry"></a>Visa telemetri i realtid

Lösningsacceleratorn kartlägger telemetridata i realtid på sidan **Instrumentpanel**. Högst upp i telemetridiagrammet visas tillgängliga telemetrityper för enheterna som valts av det aktuella filtret:

[![Telemetrityper för lastbil](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Visa telemetridata för temperatur genom att klicka på **Temperature** (Temperatur):

[![Telemetrikartläggning av lastbilstemperatur](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="use-the-map"></a>Använda kartan

På kartan visas information om de simulerade lastbilarna som har valts av det aktuella filtret. Du kan zooma och panorera kartan för att visa platserna i mer eller mindre detalj. Färgen på en enhetsikon på kartan visar om det finns aktiva **aviseringar** eller **varningar** för enheten. En sammanfattning av **aviseringar** och **varningar** visas till vänster på kartan.

Du kan visa enhetsinformationen genom att panorera och zooma kartan för att hitta enheten och sedan välja enheten på kartan. Klicka sedan på enhetsetiketten för att öppna panelen **Enhetsinformation**. Enhetsinformationen omfattar:

* Senaste telemetrivärden
* Metoder som enheten stöder
* Enhetsegenskaper

[![Visa enhetsinformation på instrumentpanelen](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Visa aviseringar

Panelen **Aviseringar** visar detaljerad information om de senaste aviseringarna från dina enheter:

[![Visa enhetsaviseringar på instrumentpanelen](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Du kan använda ett filter för att justera tidsintervallet för de senaste aviseringarna. Som standard visar panelen aviseringar från den senaste timmen:

[![Filtrera aviseringarna efter tid](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-expanded.png#lightbox)

## <a name="view-the-system-kpis"></a>Visa system-KPI:er

På sidan **Instrumentpanel** visas system-KPI:er beräknade av lösningsacceleratorn i panelen **Analys**:

[![KPI:er på instrumentpanelen](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

På instrumentpanelen visas tre KPI:er för aviseringarna som har valts av de aktuella filtren för aktuell enhet och tidsintervall:

* Antalet aktiva aviseringar för reglerna som utlöst flest aviseringar.
* Andelen aviseringar per enhetstyp.
* Procentandelen aviseringar som är kritiska.

Samma filter som har angett tidsintervallet för aviseringar och kontrollerar vilka enheter som visas bestämmer hur KPI:erna aggregeras. Som standard visar panelen KPI:er aggregerat under den senaste timmen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att gå vidare till nästa självstudie lämnar du den distribuerade lösningsacceleratorn Fjärrövervakning. Du kan minska kostnaden för att köra lösningsacceleratorn när du inte använder den genom att stoppa de simulerade enheterna på inställningspanelen:

[![Pausa telemetri](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-expanded.png#lightbox)

Du kan starta om de simulerade enheterna när du är redo att starta nästa självstudie.

Om du inte längre behöver lösningsacceleratorn tar du bort den från sidan [Etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Ta bort lösningen](media/iot-accelerators-remote-monitoring-monitor/deletesolution.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen visades hur du använder sidan **Instrumentpanel** i lösningsacceleratorn Fjärrövervakning för att filtrera och övervaka de simulerade lastbilarna. Fortsätt till nästa självstudiekurs om du vill lära dig hur du använder lösningsacceleratorn till att identifiera problem med anslutna enheter.

> [!div class="nextstepaction"]
> [Identifiera problem med enheter som är anslutna till din övervakningslösning](iot-accelerators-remote-monitoring-automate.md)