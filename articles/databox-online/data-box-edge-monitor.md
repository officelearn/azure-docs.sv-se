---
title: Övervaka din Azure Data Box Edge-enhet | Microsoft Docs
description: Beskriver hur du använder Azure-portalen och lokala webbgränssnittet för att övervaka dina Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: d9f225bd25e07a7d3f88a42864c5b5def0520519
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615820"
---
# <a name="monitor-your-azure-data-box-edge"></a>Övervaka dina Azure Data Box-Edge

Den här artikeln beskriver hur du övervakar dina Azure Data Box Edge. Du kan använda Azure portal eller det lokala webbgränssnittet för att övervaka din enhet. Använda Azure-portalen för att visa enhetshändelser, konfigurera och hantera aviseringar och visa mått. Använd det lokala webbgränssnittet på den fysiska enheten om du vill visa maskinvarustatus för de olika enhetskomponenterna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Visa enhetshändelser och motsvarande aviseringar
> * Visa maskinvarustatus av komponenter
> * Visa kapacitet och transaktionen mått för din enhet
> * Konfigurera och hantera aviseringar

## <a name="view-device-events"></a>Visa händelser för enhet

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Visa maskinvarustatus

Vidta följande steg i det lokala webbgränssnittet visa maskinvarustatus av din enhetskomponenter.

1. Ansluta till det lokala webbgränssnittet på din enhet.
2. Gå till **Underhåll > maskinvarustatus**. Du kan visa hälsotillståndet för de olika enhetskomponenterna.

    ![Visa maskinvarustatus](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Visa mått

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Hantera aviseringar

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Nästa steg 

Lär dig hur du [hanterar bandbredd](data-box-edge-manage-bandwidth-schedules.md).