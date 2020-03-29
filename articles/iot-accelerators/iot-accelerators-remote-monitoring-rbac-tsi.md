---
title: Dataåtkomstkontroll för fjärrövervakning – Azure | Microsoft-dokument
description: Den här artikeln innehåller information om hur du kan konfigurera åtkomstkontroller för Time Series Insights telemetriutforskare i lösningsacceleratorn för fjärrövervakning
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65827241"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Konfigurera åtkomstkontroller för telemetriutforskaren Time Series Insights

Den här artikeln innehåller information om hur du konfigurerar åtkomstkontroller för Tidsseriestatistikutforskaren i lösningsacceleratorn för fjärrövervakning. Om du vill att användare av lösningsacceleratorn ska kunna komma åt Utforskaren för Time Series Insights måste du ge varje användardata åtkomst.

Principerna för dataåtkomst beviljar behörigheter för att utfärda datafrågor, manipulera referensdata i miljön och sparade delade frågor och perspektiv som är associerade till miljön.

## <a name="grant-data-access"></a>Bevilja åtkomst till data

Följ dessa steg för att bevilja dataåtkomst för ett användarobjekt:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Hitta din time series insights-miljö. Skriv **tidsserier** i **sökrutan.** Välj **Tidsseriemiljö** i sökresultaten. 

3. Välj Time Series Insights-miljön från listan.

4. Välj **Data Access-principer**och välj sedan **+ Lägg till**.
    ![Hantera Time Series Insights-källan – miljö](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Välj **Välj användare**.  Sök efter användarnamnet eller e-postadressen för att hitta den användare du vill lägga till. Klicka på **Markera** för att bekräfta markeringen. 

    ![Hantera Time Series Insights-källan – lägg till](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Välj **Välj roll**. Välj lämplig åtkomstroll för användaren:
   - Välj **Deltagare** om du vill tillåta användaren att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön. 
   - Annars väljer du **Läsare** om du vill tillåta användarfrågedata i miljön och spara personliga (inte delade) frågor i miljön.

     Välj **Ok** för att bekräfta rollvalet.

     ![Hantera Time Series Insights-källan – välj användare](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Välj **Ok** på sidan **Välj användarroll.**

    ![Hantera Time Series Insights-källan – välj roll](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. På sidan **Dataåtkomstprinciper** visas användarna och rollerna för varje användare.

    ![Hantera Time Series Insights-källan – resultat](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du veta hur åtkomstkontroller beviljas för Tidsseriestatistikutforskaren i lösningsacceleratorn för fjärrövervakning.

Mer begreppsmässig information om lösningsacceleratorn för fjärrövervakning finns i [Fjärrövervakningsarkitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar lösningen för fjärrövervakning finns i [Anpassa och distribuera om en mikrotjänst](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->