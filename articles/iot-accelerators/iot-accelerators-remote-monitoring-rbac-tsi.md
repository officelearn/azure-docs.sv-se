---
title: Övervakning fjärrdata åtkomstkontroll – Azure | Microsoft Docs
description: Den här artikeln innehåller information om hur du kan konfigurera åtkomstkontroller för Time Series Insights-Utforskaren för telemetri i lösningsacceleratorn för fjärrövervakning
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827241"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Konfigurera åtkomstkontroller för Time Series Insights telemetri explorer

Den här artikeln innehåller information om hur du konfigurerar åtkomstkontroller för Time Series Insights explorer i lösningsacceleratorn för fjärrövervakning. Om du vill ge användare för solution accelerator åtkomst till Time Series Insights explorer, måste du bevilja åtkomst till data varje användare.

Principerna för dataåtkomst beviljar behörigheter för att utfärda datafrågor, manipulera referensdata i miljön och sparade delade frågor och perspektiv som är associerade till miljön.

## <a name="grant-data-access"></a>Bevilja åtkomst till data

Följ dessa steg om du vill bevilja åtkomst till data för en användares huvudnamn:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta upp din Time Series Insights-miljö. Typ **tidsserier** i den **search** box. Välj **Time Series Environment** i sökresultatet. 

3. Välj Time Series Insights-miljön från listan.

4. Välj **Dataåtkomstprinciper**och välj sedan **+ Lägg till**.
    ![Hantera Time Series Insights-källan – miljö](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Välj **Välj användare**.  Sök efter användarnamn eller e-postadress att hitta användaren som du vill lägga till. Klicka på **Välj** att bekräfta valet. 

    ![Hantera Time Series Insights-källan – lägg till](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Välj **Välj roll**. Välj rätt roll för användaren:
   - Välj **deltagare** om du vill tillåta användare att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön. 
   - Annars väljer **läsare** att tillåta användare söker data i miljön och sparar personliga (inte delade) frågor i miljön.

     Välj **Ok** att bekräfta valet rollen.

     ![Hantera Time Series Insights-källan – välj användare](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Välj **Ok** i den **Välj användarroll** sidan.

    ![Hantera Time Series Insights-källan – välj roll](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. Den **Dataåtkomstprinciper** sidan visas användarna och roller för varje användare.

    ![Hantera Time Series Insights-källan – resultat](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur åtkomstkontroller beviljas för Time Series Insights explorer i lösningsacceleratorn för fjärrövervakning.

Mer information om lösningsacceleratorn för fjärrövervakning finns [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar lösningen för fjärrövervakning finns [anpassa och distribuera om en mikrotjänst](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->