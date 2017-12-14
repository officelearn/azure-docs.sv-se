---
title: "Felsökning av enheter i fjärranslutna övervakningslösning - Azure | Microsoft Docs"
description: "Den här kursen visar hur du felsöker och åtgärda enhetsproblem med i fjärranslutna övervakningslösning."
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
ms.openlocfilehash: d26275b6b03115b775990c9efb5d4706fcb829d1
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Felsök och åtgärda enhetsproblem

Den här kursen visar hur du använder den **Underhåll** sida i lösningen för att felsöka och åtgärda problem med enheter. I självstudiekursen används ett scenario för att införa dessa funktioner i Contoso IoT-programmet.

Contoso testa en ny **prototyp** enhet i fältet. Som en Contoso-operator som du ser under testningen som den **prototyp** enheten oväntat utlösa ett larm för temperatur på instrumentpanelen. Du måste nu undersöka beteendet för detta fel **prototyp** enhet.

I den här guiden får du lära dig hur man:

>[!div class="checklist"]
> * Använd den **Underhåll** att undersöka larm
> * Anropa en metod för enheten för att åtgärda problemet

## <a name="prerequisites"></a>Krav

Om du vill följa den här självstudiekursen, måste en distribuerad instans av den fjärranslutna övervakningslösning i din Azure-prenumeration.

Om du inte har distribuerat remote övervakningslösning ännu, bör du genomföra den [Distribuera fjärråtkomst övervakning förkonfigurerade lösningen](iot-suite-remote-monitoring-deploy.md) kursen.

## <a name="use-the-maintenance-dashboard"></a>Använd instrumentpanelen för underhåll

På den **instrumentpanelen** sidan du Observera att det finns oväntat temperatur larm från den regel som är associerade med den **prototyp** enheter:

![Larm som visar på instrumentpanelen](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

Om du vill undersöka problemet ytterligare, Välj den **utforska larm** alternativet bredvid larmet:

![Utforska larm från instrumentpanelen](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

Detaljerad vy av larmet visar:

* När larmet utlöstes
* Statusinformation om de enheter som är associerade med larm
* Telemetri från enheter som är associerade med larm

![Larm information](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

Bekräfta larm, Välj den **larm förekomster** och välj **återanropen för kvittens**. Den här åtgärden kan andra operatorer för att se att du har sett larm och arbetar med den.

![Bekräfta larm](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

I listan kan du se den **prototyp** ansvarar för startar temperatur larmet enhet:

![Lista över de enheter som orsakar larm](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Åtgärda problemet

Åtgärda problemet med den **prototyp** enhet, måste du anropa den **DecreaseTemperature** metod på enheten.

För att fungera på en enhet väljer du den i listan över enheter och välj sedan **schema**. Den **prototyp** enhetsmodell anger fyra metoder måste ha stöd för en enhet:

![Visa de metoder som har stöd för enheten](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

Välj **DecreaseTemperature** och ange namnet på det jobb som **DecreaseTemperature**. Välj **Verkställ**:

![Skapa jobbet om du vill minska temperaturen](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

Spåra status för jobbet på den **Underhåll** väljer **jobb**. Använd den **jobb** visa för att spåra alla jobb och metodanrop i lösningen:

![Övervaka jobbet om du vill minska temperaturen](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

Om du vill visa information om ett specifikt jobb eller metodanrop, väljer du den i listan i den **jobb** vy:

![Visa jobbinformation](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer vi visar dig hur till:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Använd den **Underhåll** att undersöka larm
> * Anropa en metod för enheten för att åtgärda problemet

Nu du har lärt dig hur du hanterar problem med enheter, föreslagna nästa steg är att lära dig hur du [testa din lösning med simulerade enheter](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->