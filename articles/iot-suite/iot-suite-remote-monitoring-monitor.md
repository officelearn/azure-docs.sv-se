---
title: Avancerad övervakning i fjärranslutna övervakningslösning - Azure | Microsoft Docs
description: Den här kursen visar hur du övervakar enheter med instrumentpanelen för fjärråtkomst övervakning lösning.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: e6bf1962115c4ee9212ae73eb98f664efa3b6a2d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Utför avancerad övervakning med hjälp av den fjärranslutna övervakningslösning

Den här kursen visar funktionerna i instrumentpanelen för fjärråtkomst övervakning. I självstudiekursen används ett scenario för att införa dessa funktioner i Contoso IoT-programmet.

I den här kursen använder du två simulerade Contoso lastbil enheter att lära dig hur du övervakar dina enheter från solution accelerator instrumentpanelen. Du behöver övervaka plats och beteendet för din lastbilar i fältet som en Contoso-operator.

I den här guiden får du lära dig att:

>[!div class="checklist"]
> * Filtrera enheter i instrumentpanelen
> * Visa realtid telemetri
> * Visa information om enhet
> * Visa aviseringar från dina enheter
> * Visa system KPI: er

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här självstudiekursen, måste en distribuerad instans av den fjärranslutna övervakningslösning i din Azure-prenumeration.

Om du inte har distribuerat remote övervakningslösning ännu, bör du genomföra den [Distribuera fjärråtkomst övervakning solution accelerator](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md) kursen.

## <a name="choose-the-devices-to-display"></a>Välj vilka enheter som ska visas

Att välja vilka enheter som visas på den **instrumentpanelen** använder filter. Visa endast den **lastbil** enheter, väljer inbyggt **lastbilar** filter i filtret listrutan:

![Filtrera efter lastbilar på instrumentpanelen](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

När du använder ett filter visas endast de enheter som matchar filtret villkor i mappningen på den **instrumentpanelen** sidan:

![Lastbilar visas på kartan](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

Filtret avgör också vilka enheter som du ser i den **telemetri** diagram:

![Lastbil telemetri visas på instrumentpanelen](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

För att skapa, redigera och ta bort filter, Välj **hantera filter**.

## <a name="view-real-time-telemetry"></a>Visa realtid telemetri

Solution accelerator visar detaljerad realtid telemetridata i diagrammet på den **instrumentpanelen** sidan. Telemetri diagrammet visar telemetri information för enheter som valts av det aktuella filtret:

![Lastbil telemetri ritytans](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Välj typen telemetri överst i diagrammet för att välja telemetri värden att visa:

![Lastbil telemetri ritytans](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

<!-- 05/01 - this features appears to have been removed
To pause the live telemetry display, choose **Flowing**. To re-enable the live display, choose **Pause**:

![Pause and restart telemetry display](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)-->

## <a name="use-the-map"></a>Använd kartan

Kartan visar information om simulerad lastbilar som valts av det aktuella filtret. Du kan zooma och Panorera kartan för att visa platser i mer eller mindre information. Ikoner för enheter på kartan ange någon **aviseringar** eller **varningar** som är aktiva för enheten. En sammanfattning av antalet **aviseringar** och **varningar** visas till vänster om kartan.

<!-- 05/01 - cannot select a deice on the map
To view the device details, pan and zoom the map to locate the devices, then click the device on the map. The details include:

* Recent telemetry values
* Methods the device supports
* Device properties

![View device details on the dashboard](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)-->

## <a name="view-alerts-from-your-devices"></a>Visa aviseringar från dina enheter

Kartan visar enheterna i det aktuella filtret med **aviseringar** och **varningar**. Den **aviseringar** panelen visas detaljerad information om de senaste aviseringarna från dina enheter:

![Visa system aviseringar på instrumentpanelen](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

Du kan använda den **instrumentpanelen** filter för att justera tidsintervallet för senaste aviseringarna. Panelen visas aviseringar från den senaste timmen som standard:

![Filtrera aviseringarna efter tid](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Visa system KPI: er

Den **instrumentpanelen** visar sidan system KPI: er:

![Instrumentpanelen KPI: er](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

Du kan använda den **instrumentpanelen** filter för att justera tidsintervallet för KPI-aggregering. Som standard visar panelen KPI: er samman under den senaste timmen.

## <a name="next-steps"></a>Nästa steg

Den här självstudiekursen visades hur du använder den **instrumentpanelen** att filtrera och övervaka simulerade lastbilar etablerats i din lösning för fjärråtkomst övervakning:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrera enheter i instrumentpanelen
> * Visa realtid telemetri
> * Visa information om enhet
> * Visa aviseringar från dina enheter
> * Visa system KPI: er

Nu när du har lärt dig hur du övervakar dina enheter, föreslagna nästa steg är att lära dig hur du:

* [Identifiera problem med tröskelvärdesbaserad regler](../iot-accelerators/iot-accelerators-remote-monitoring-automate.md).
* [Hantera och konfigurera dina enheter](./iot-suite-remote-monitoring-manage.md).
* [Felsök och åtgärda enhetsproblem](./iot-suite-remote-monitoring-maintain.md).
* [Testa din lösning med simulerade enheter](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->