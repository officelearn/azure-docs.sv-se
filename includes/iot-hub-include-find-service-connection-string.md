---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b6ca43616a2e7e7611b122bce5c95084e1fd5012
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402409"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Följ dessa steg om du vill hämta IoT Hub anslutnings sträng för **tjänst** principen:

1. Öppna din IoT Hub i [Azure Portal](https://portal.azure.com).  Det enklaste sättet att komma till din IoT-hubb är att välja **resurs grupper**, välja resurs gruppen där IoT Hub finns och sedan välja din IoT Hub i listan över resurser.

2. I den vänstra rutan i IoT Hub väljer du **principer för delad åtkomst**.

3. Välj **tjänst** princip i listan över principer.

4. Under **delade åtkomst nycklar**väljer du kopierings ikonen för **anslutnings strängen – primär nyckel** och spara värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Mer information om IoT Hub principer för delad åtkomst och behörigheter finns i [åtkomst kontroll och behörigheter](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
