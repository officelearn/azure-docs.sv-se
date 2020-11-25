---
title: Data åtkomst kontroll för fjärrövervakning – Azure | Microsoft Docs
description: Den här artikeln innehåller information om hur du kan konfigurera åtkomst kontroller för Time Series Insights telemetri Explorer i Solution Accelerator för fjärr styrning
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005970"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Konfigurera åtkomst kontroller för Time Series Insights telemetri Explorer

Den här artikeln innehåller information om hur du konfigurerar åtkomst kontroller för Time Series Insights Explorer i Solution Accelerator för fjärr styrning. Om du vill tillåta användare av Solution Accelerator att komma åt Time Series Insights Explorer måste du ge varje användar åtkomst till data.

Principerna för dataåtkomst beviljar behörigheter för att utfärda datafrågor, manipulera referensdata i miljön och sparade delade frågor och perspektiv som är associerade till miljön.

## <a name="grant-data-access"></a>Bevilja åtkomst till data

Följ dessa steg om du vill bevilja data åtkomst för ett huvud konto för användare:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Leta upp din Time Series Insightss miljö. Skriv **tids serier** i **sökrutan.** Välj **tids serie miljö** i Sök resultaten. 

3. Välj Time Series Insights-miljön från listan.

4. Välj **data åtkomst principer** och välj sedan **+ Lägg till**.
    ![Hantera Time Series Insights-källan – miljö](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Välj **Välj användare**.  Sök efter användar namnet eller e-postadressen för att hitta den användare som du vill lägga till. Bekräfta valet genom att klicka på **Välj** . 

    ![Hantera Time Series Insights-källan – lägg till](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Välj **Välj roll**. Välj lämplig åtkomst roll för användaren:
   - Välj **deltagare** om du vill tillåta att användare ändrar referens data och delar sparade frågor och perspektiv med andra användare av miljön. 
   - Annars väljer du **läsare** för att tillåta användare att fråga efter data i miljön och spara personliga (inte delade) frågor i miljön.

     Bekräfta valet av roll genom att klicka på **OK** .

     ![Hantera Time Series Insights-källan – välj användare](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Välj **OK** på sidan **Välj användar roll** .

    ![Hantera Time Series Insights-källan – välj roll](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. Sidan för **data åtkomst principer** listar användare och roller för varje användare.

    ![Hantera Time Series Insights-källan – resultat](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur åtkomst kontroller beviljas för Time Series Insights Explorer i Solution Accelerator för fjärr styrning.

Mer information om lösnings acceleratorn för fjärrövervakning finns i [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar lösningen för fjärrövervakning finns i [Anpassa och distribuera om en mikrotjänst](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->