---
title: "Avancerad övervakning i fjärranslutna övervakningslösning - Azure | Microsoft Docs"
description: "Den här kursen visar hur du övervakar enheter med instrumentpanelen för fjärråtkomst övervakning lösning."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 2e6d965d9177a61f974b319a1bd2155c9132533f
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Utför avancerad övervakning med hjälp av den fjärranslutna övervakningslösning

Den här kursen visar funktionerna i instrumentpanelen för fjärråtkomst övervakning. I självstudiekursen används ett scenario för att införa dessa funktioner i Contoso IoT-programmet.

I den här kursen använder du två simulerade Contoso lastbil enheter att lära dig hur du övervakar dina enheter från instrumentpanelen förkonfigurerade lösningen. Du behöver övervaka plats och beteendet för din lastbilar i fältet som en Contoso-operator.

I den här guiden får du lära dig hur man:

>[!div class="checklist"]
> * Filtrera enheter i instrumentpanelen
> * Visa realtid telemetri
> * Visa information om enhet
> * Visa larm från dina enheter
> * Visa system KPI: er

## <a name="prerequisites"></a>Krav

Om du vill följa den här självstudiekursen, måste en distribuerad instans av den fjärranslutna övervakningslösning i din Azure-prenumeration.

Om du inte har distribuerat remote övervakningslösning ännu, bör du genomföra den [Distribuera fjärråtkomst övervakning förkonfigurerade lösningen](iot-suite-remote-monitoring-deploy.md) kursen.

## <a name="choose-the-devices-to-display"></a>Välj vilka enheter som ska visas

Att välja vilka enheter som visas på den **instrumentpanelen** använder filter. Visa endast den **lastbil** enheter, väljer inbyggt **lastbilar** filter i filtret listrutan:

![Filtrera efter lastbilar på instrumentpanelen](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

När du använder ett filter visas endast de enheter som matchar filtret villkor i mappningen på den **instrumentpanelen** sidan:

![Lastbilar visas på kartan](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

Filtret avgör också vilka enheter som du ser i den **telemetri** diagram:

![Lastbil telemetri visas på instrumentpanelen](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

För att skapa, redigera och ta bort filter, Välj **hantera filter**.

## <a name="view-real-time-telemetry"></a>Visa realtid telemetri

Den förkonfigurerade lösningen visar detaljerad realtid telemetridata i diagrammet på den **instrumentpanelen** sidan. Telemetri diagrammet visar telemetri information för enheter som valts av det aktuella filtret:

![Lastbil telemetri ritytans](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Välj typen telemetri överst i diagrammet för att välja telemetri värden att visa:

![Lastbil telemetri ritytans](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

Om du visa live telemetri, Välj **Flowing**. Om du vill återaktivera live visningen, Välj **paus**:

![Pausa och starta om telemetri visning](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)

## <a name="use-the-map"></a>Använd kartan

Kartan visar information om simulerad lastbilar som valts av det aktuella filtret. Du kan zooma och Panorera kartan för att visa platser i mer eller mindre information. Ikoner för enheter på kartan ange någon **larm** eller **varningar** som är aktiva för enheten. En sammanfattning av antalet **larm** och **varningar** visas till vänster om kartan.

Om du vill visa information om enheten Panorera och Zooma i kartan för att hitta enheter och sedan klickar du på enheten på kartan. Informationen omfattar:

* Senaste telemetri värden
* Metoder som har stöd för enheten
* Enhetsegenskaper

![Visa information om enhet på instrumentpanelen](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)

## <a name="view-alarms-from-your-devices"></a>Visa larm från dina enheter

Kartan visar enheterna i det aktuella filtret med **larm** och **varningar**. Den **System larm** panelen visas detaljerad information om de senaste larm från dina enheter:

![Visa system larm på instrumentpanelen](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

Du kan använda den **System larm** filter för att justera tidsintervallet för senaste larm. Panelen visar larm från den senaste timmen som standard:

![Filtrera larm enligt tid](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Visa system KPI: er

Den **instrumentpanelen** visar sidan system KPI: er:

![Filtrera larm enligt tid](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

Du kan använda den **System KPI** filter för att justera tidsintervallet för KPI-aggregering. Som standard visar panelen KPI: er samman under den senaste timmen.

## <a name="next-steps"></a>Nästa steg

Den här självstudiekursen visades hur du använder den **instrumentpanelen** att filtrera och övervaka simulerade lastbilar etablerats i din lösning för fjärråtkomst övervakning:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrera enheter i instrumentpanelen
> * Visa realtid telemetri
> * Visa information om enhet
> * Visa larm från dina enheter
> * Visa system KPI: er

Nu när du har lärt dig hur du övervakar dina enheter, föreslagna nästa steg är att lära dig hur du:

* [Identifiera problem med tröskelvärdesbaserad regler](./iot-suite-remote-monitoring-automate.md).
* [Hantera och konfigurera dina enheter](./iot-suite-remote-monitoring-manage.md).
* [Felsök och åtgärda enhetsproblem](./iot-suite-remote-monitoring-maintain.md).
* [Testa din lösning med simulerade enheter](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->