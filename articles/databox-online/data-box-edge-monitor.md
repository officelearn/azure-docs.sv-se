---
title: Övervaka Azure Data Box Edge-enheten | Microsoft Docs
description: Beskriver hur du använder Azure Portal och det lokala webb gränssnittet för att övervaka din Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "60756752"
---
# <a name="monitor-your-azure-data-box-edge"></a>Övervaka dina Azure Data Box Edge

I den här artikeln beskrivs hur du övervakar Azure Data Box Edge. Du kan övervaka din enhet genom att använda Azure Portal eller det lokala webb gränssnittet. Använd Azure Portal för att Visa enhets händelser, konfigurera och hantera aviseringar och visa mått. Använd det lokala webb gränssnittet på din fysiska enhet för att Visa maskin varu status för de olika enhets komponenterna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Visa enhets händelser och motsvarande aviseringar
> * Visa maskin varu status för enhets komponenter
> * Visa kapacitets-och transaktions mått för din enhet
> * Konfigurera och hantera aviseringar

## <a name="view-device-events"></a>Visa enhets händelser

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Visa maskin varu status

Utför följande steg i det lokala webb gränssnittet för att Visa maskin varu statusen för dina enhets komponenter.

1. Anslut till det lokala webb gränssnittet på enheten.
2. Gå till **underhålls > maskin varu status**. Du kan visa hälso tillståndet för de olika enhets komponenterna.

    ![Visa maskin varu status](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Visa mått

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Hantera aviseringar

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Nästa steg 

Lär dig att [hantera bandbredd](data-box-edge-manage-bandwidth-schedules.md).