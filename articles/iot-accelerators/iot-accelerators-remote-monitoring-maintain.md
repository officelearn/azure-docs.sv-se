---
title: Felsökning av enheter i fjärranslutna övervakningslösning - Azure | Microsoft Docs
description: Den här kursen visar hur du felsöker och åtgärda enhetsproblem med i fjärranslutna övervakningslösning.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 1e6c74b5c952d5a387dbdac4d6e81d691758fb36
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Felsök och åtgärda enhetsproblem

Den här kursen visar hur du använder den **Underhåll** sida i lösningen för att felsöka och åtgärda problem med enheter. I självstudiekursen används ett scenario för att införa dessa funktioner i Contoso IoT-programmet.

Contoso testa en ny **prototyp** enhet i fältet. Som en Contoso-operator som du ser under testningen som den **prototyp** enheten oväntat utlösa en avisering om temperatur på instrumentpanelen. Du måste nu undersöka beteendet för detta fel **prototyp** enhet.

I den här guiden får du lära dig att:

>[!div class="checklist"]
> * Använd den **Underhåll** sidan om du vill undersöka aviseringen
> * Anropa en metod för enheten för att åtgärda problemet

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här självstudiekursen, måste en distribuerad instans av den fjärranslutna övervakningslösning i din Azure-prenumeration.

Om du inte har distribuerat remote övervakningslösning ännu, bör du genomföra den [Distribuera fjärråtkomst övervakning solution accelerator](iot-accelerators-remote-monitoring-deploy.md) kursen.

## <a name="use-the-maintenance-dashboard"></a>Använd instrumentpanelen för underhåll

På den **instrumentpanelen** sidan du Observera att det finns oväntat temperatur aviseringar från regler som associeras med den **prototyp** enheter:

![Aviseringar visas på instrumentpanelen](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm.png)

Om du vill undersöka problemet ytterligare, Välj den **utforska avisering** alternativet bredvid aviseringen:

![Utforska avisering från instrumentpanelen](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm.png)

Detaljerad vy av aviseringen visas:

* När aviseringen utlöstes
* Statusinformation om de enheter som är kopplade till aviseringen
* Telemetri från enheter som är kopplade till aviseringen

![Aviseringsinformation](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail.png)

Bekräfta en avisering, Välj den **Varna förekomster** och välj **återanropen för kvittens**. Den här åtgärden kan andra operatorer för att se att du har sett aviseringen och arbetar med den.

![Bekräfta aviseringar](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge.png)

När du har godkänt aviseringen status för ändras till **godkänd**.

I listan kan du se den **prototyp** enhet som är ansvarig för startar temperatur aviseringen:

![Lista över de enheter som orsakar aviseringen](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Åtgärda problemet

Åtgärda problemet med den **prototyp** enhet, måste du anropa den **DecreaseTemperature** metod på enheten.

För att fungera på en enhet väljer du den i listan över enheter och välj sedan **jobb**. Den **prototyp** enhetsmodell anger sex metoder måste ha stöd för en enhet:

![Visa de metoder som har stöd för enheten](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods.png)

Välj **DecreaseTemperature** och ange namnet på det jobb som **DecreaseTemperature**. Välj **Verkställ**:

![Skapa jobbet om du vill minska temperaturen](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob.png)

Spåra status för jobbet på den **Underhåll** väljer **jobb**. Använd den **jobb** visa för att spåra alla jobb och metodanrop i lösningen:

![Övervaka jobbet om du vill minska temperaturen](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob.png)

Om du vill visa information om ett specifikt jobb eller metodanrop, väljer du den i listan i den **jobb** vy:

![Visa jobbinformation](./media/iot-accelerators-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Nästa steg

I kursen får du sett hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Använd den **Underhåll** sidan om du vill undersöka aviseringen
> * Anropa en metod för enheten för att åtgärda problemet

Nu du har lärt dig hur du hanterar problem med enheter, föreslagna nästa steg är att lära dig hur du [testa din lösning med simulerade enheter](iot-accelerators-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->