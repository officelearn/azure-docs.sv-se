---
title: Övervaka din Azure Data Box Edge-enhet | Microsoft-dokument
description: Beskriver hur du använder Azure-portalen och det lokala webbgränssnittet för att övervaka din Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756752"
---
# <a name="monitor-your-azure-data-box-edge"></a>Övervaka din Azure Data Box Edge

I den här artikeln beskrivs hur du övervakar din Azure Data Box Edge. Om du vill övervaka din enhet kan du använda Azure-portalen eller det lokala webbgränssnittet. Använd Azure-portalen för att visa enhetshändelser, konfigurera och hantera aviseringar och visa mått. Använd det lokala webbgränssnittet på din fysiska enhet för att visa maskinvarustatus för de olika enhetskomponenterna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Visa enhetshändelser och motsvarande aviseringar
> * Visa maskinvarustatus för enhetskomponenter
> * Visa kapacitet och transaktionsmått för din enhet
> * Konfigurera och hantera aviseringar

## <a name="view-device-events"></a>Visa enhetshändelser

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Visa maskinvarustatus

Gör följande i det lokala webbgränssnittet för att visa maskinvarustatus för enhetskomponenterna.

1. Anslut till enhetens lokala webbgränssnitt.
2. Gå till **underhålls- > maskinvarustatus**. Du kan visa hälsotillståndet för de olika enhetskomponenterna.

    ![Visa maskinvarustatus](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Visa mått

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Hantera aviseringar

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Nästa steg 

Lär dig att [hantera bandbredd](data-box-edge-manage-bandwidth-schedules.md).