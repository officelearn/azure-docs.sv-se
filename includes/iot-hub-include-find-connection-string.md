---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187440"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

När hubben har skapats hämtar du anslutningssträngen för den. Den används till att ansluta enheter och program till hubben. 

1. Klicka på hubben för att se IoT Hub-fönstret med Inställningar och så vidare. Klicka på **Principer för delad åtkomst**.
   
2. Välj principen **iothubowner** i **Principer för delad åtkomst**. 

3. Under **Nycklar för delad åtkomst** kopierar du **Anslutningssträng -- primärnyckel** som ska användas senare.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    Mer information finns i avsnittet om [åtkomstkontroll](../articles/iot-hub/iot-hub-devguide-security.md) i ”utvecklarhandboken för IoT Hub”.
