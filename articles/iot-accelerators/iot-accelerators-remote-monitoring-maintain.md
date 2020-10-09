---
title: Använd aviseringar i lösningen för fjärrövervakning – Azure | Microsoft Docs
description: Den här självstudiekursen visar hur du använder aviseringar till att identifiera och åtgärda problem med enheter anslutna till acceleratorn Fjärrövervakningslösning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 853fa2b80e04dd8d9225d023db8030fed044ed7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "73890926"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>Självstudie: Felsök och åtgärda enhetsproblem

I den här självstudien använder du självstudiekursen använder du acceleratorn Fjärrövervakningslösning till att identifiera och åtgärda problem med dina anslutna IoT-enheter. Du använder aviseringar i lösningsacceleratorns instrumentpanel till att identifiera problem och kör sedan fjärrjobb för att åtgärda dessa problem.

Contoso testar en ny enhet av typen **Prototype** (Prototyp) i fältet. Som Contoso-operatör märker du under testningen att **prototypen** oväntat utlöser en temperaturavisering på instrumentpanelen. Du måste nu undersöka beteendet hos den felaktiga **prototypen** och lösa problemet.

I den här kursen får du:

>[!div class="checklist"]
> * Undersöka en avisering från en enhet
> * Lösa problemet med enheten

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>Undersöka en avisering

På sidan **Instrumentpanel** märker du att det finns oväntade temperaturaviseringar från regeln som hör till enheterna av typen **Prototype** (Prototyp):

[![Aviseringar som visas på instrument panelen](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Du kan undersöka problemet vidare genom att välja alternativet **Explore Alert** (Utforska avisering) bredvid aviseringen:

[![Utforska aviseringen från instrument panelen](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

Informationsvyn för aviseringen visar:

* När aviseringen utlöstes
* Statusinformation om enheter som är kopplade till aviseringen
* Telemetri från enheterna kopplade till aviseringen

[![Aviseringsinformation](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Du kan bekräfta aviseringen genom att välja **Alert occurrences** (Aviseringsförekomster) och välja **Bekräfta**. Med den här åtgärden meddelas andra operatörer att du har sett aviseringen och arbetar med den:

[![Bekräfta aviseringarna](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

När du bekräftar aviseringen ändras statusen för förekomsten till **Bekräftad**.

I listan över enheter med aviseringar visas den enhet av typen **Prototype** (Prototyp) som har gjort att en temperaturavisering har utlösts:

[![Visa en lista över de enheter som orsakar aviseringen](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Lösa problemet

Om du vill lösa problemet med enheten av typen **Prototype** (Prototyp) måste du anropa metoden **DecreaseTemperature** (Minska temperatur) för enheten.

Du kan utföra åtgärden på enheten genom att välja den i listan över enheter med aviseringar och sedan välja **Jobb**. Enhetsmodellen **Prototype** (Prototyp) stöder sex metoder:

[![Visa de metoder som enheten stöder](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Välj **DecreaseTemperature** (Minska temperatur) och ange jobbnamnet **DecreaseTemperature** (Minska temperatur). Klicka sedan på **Använd**:

[![Skapa jobbet för att minska temperaturen](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Klicka på **Visa jobb status**om du vill spåra jobbets status. I vyn **Jobb** kan du spåra alla jobb och metodanrop i lösningen:

[![Övervaka jobbet för att minska temperaturen](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

Du kan kontrollera att temperaturen för enheten har minskat genom att visa telemetridata på sidan **Instrumentpanel**:

[![Visa minskningen av temperaturen](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen visades hur du identifierar problem med enheter och hur du agerar för att lösa dessa problem. Fortsätt till instruktionsartiklarna om du vill läsa om hur du ansluter en riktig enhet till lösningsacceleratorn.

Nu har du lärt dig hur du hanterar enhetsproblem. Förslag på nästa steg är att lära sig hur du [ansluter enheten till acceleratorn Fjärrövervakningslösning](iot-accelerators-connecting-devices.md).
