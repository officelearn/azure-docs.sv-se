---
title: Självstudie om att använda aviseringar och åtgärda enhetsproblem i fjärrövervakningslösningen – Azure | Microsoft Docs
description: Den här självstudiekursen visar hur du använder aviseringar till att identifiera och åtgärda problem med enheter anslutna till lösningsacceleratorn Fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 3138f0ebb6316e69c873a37d479ddc0279a361ef
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285090"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>Självstudie: Felsök och åtgärda enhetsproblem

I den här självstudien använder du självstudiekursen använder du lösningsacceleratorn Fjärrövervakning till att identifiera och åtgärda problem med dina anslutna IoT-enheter. Du använder aviseringar i lösningsacceleratorns instrumentpanel till att identifiera problem och kör sedan fjärrjobb för att åtgärda dessa problem.

Contoso testar en ny enhet av typen **Prototype** (Prototyp) i fältet. Som Contoso-operatör märker du under testningen att **prototypen** oväntat utlöser en temperaturavisering på instrumentpanelen. Du måste nu undersöka beteendet hos den felaktiga **prototypen** och lösa problemet.

I den här kursen för du göra följande:

>[!div class="checklist"]
> * Undersöka en avisering från en enhet
> * Lösa problemet med enheten

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>Undersöka en avisering

På sidan **Instrumentpanel** märker du att det finns oväntade temperaturaviseringar från regeln som hör till enheterna av typen **Prototype** (Prototyp):

[![Aviseringar som visas på instrumentpanelen](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Du kan undersöka problemet vidare genom att välja alternativet **Explore Alert** (Utforska avisering) bredvid aviseringen:

[![Utforska avisering på instrumentpanelen](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

Informationsvyn för aviseringen visar:

* När aviseringen utlöstes
* Statusinformation om enheter som är kopplade till aviseringen
* Telemetri från enheterna kopplade till aviseringen

[![Aviseringsinformation](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Du kan bekräfta aviseringen genom att välja **Alert occurrences** (Aviseringsförekomster) och välja **Bekräfta**. Med den här åtgärden meddelas andra operatörer att du har sett aviseringen och arbetar med den:

[![Bekräfta aviseringen](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

När du bekräftar aviseringen ändras statusen för förekomsten till **Bekräftad**.

I listan över enheter med aviseringar visas den enhet av typen **Prototype** (Prototyp) som har gjort att en temperaturavisering har utlösts:

[![Lista enheter som orsakar aviseringen](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Lösa problemet

Om du vill lösa problemet med enheten av typen **Prototype** (Prototyp) måste du anropa metoden **DecreaseTemperature** (Minska temperatur) för enheten.

Du kan utföra åtgärden på enheten genom att välja den i listan över enheter med aviseringar och sedan välja **Jobb**. Enhetsmodellen **Prototype** (Prototyp) stöder sex metoder:

[![Visa metoder som enheten stöder](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Välj **DecreaseTemperature** (Minska temperatur) och ange jobbnamnet **DecreaseTemperature** (Minska temperatur). Klicka sedan på **Använd**:

[![Skapa jobbet för att minska temperaturen](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Du kan spåra jobbets status genom att klicka på **Visa jobbstatus**. I vyn **Jobb** kan du spåra alla jobb och metodanrop i lösningen:

[![Övervaka jobbet för att minska temperaturen](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

Du kan kontrollera att temperaturen för enheten har minskat genom att visa telemetridata på sidan **Instrumentpanel**:

[![Visa temperaturminskningen](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen visades hur du identifierar problem med enheter och hur du agerar för att lösa dessa problem. Fortsätt till instruktionsartiklarna om du vill läsa om hur du ansluter en fysisk enhet till lösningsacceleratorn.

Nu har du lärt dig hur du hanterar enhetsproblem. Förslag på nästa steg är att lära sig hur du [ansluter enheten till lösningsacceleratorn Fjärrövervakning](iot-accelerators-connecting-devices.md).
