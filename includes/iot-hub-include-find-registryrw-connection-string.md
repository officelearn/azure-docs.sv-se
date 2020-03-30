---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883763"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Så här hämtar du anslutningssträngen för IoT Hub för **registereditornReadWrite:**

1. Välj **Resursgrupper**i [Azure-portalen](https://portal.azure.com). Välj den resursgrupp där hubben finns och välj sedan hubben i listan över resurser.

2. Välj Principer för **delad åtkomst**i den vänstra rutan i navet .

3. Välj **principen registeredreadwrite** i listan över principer.

4. Under **Delade åtkomstnycklar**väljer du **kopieringsikonen för anslutningssträngen – primärnyckeln** och sparar värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Mer information om principer och behörigheter för IoT Hub-delad åtkomst finns i [Åtkomstkontroll och behörigheter](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
