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
ms.openlocfilehash: 55dc712d323c43849fa3de23c83e29fc348a0f1e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403947"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Följ dessa steg om du vill skapa en princip för delad åtkomst som beviljar Läs behörighet för **tjänsten Connect** och **Registry** och får en anslutnings sträng för den här principen:

1. Öppna din IoT Hub i [Azure Portal](https://portal.azure.com). Det enklaste sättet att komma till din IoT-hubb är att välja **resurs grupper**, välja resurs gruppen där IoT Hub finns och sedan välja din IoT Hub i listan över resurser.

2. I den vänstra rutan i IoT Hub väljer du **principer för delad åtkomst**.

3. Välj **Lägg till**på den översta menyn ovanför listan över principer.

4. I fönstret **Lägg till en princip för delad åtkomst** anger du ett beskrivande namn för principen. till exempel: *serviceAndRegistryRead*. Under **behörigheter**väljer du **register läsa** och **tjänst Anslut**och väljer sedan **skapa**.

    ![Visa hur du lägger till en ny princip för delad åtkomst](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

5. Gå tillbaka till fönstret **principer för delad åtkomst** och välj den nya principen i listan över principer.

6. Under **delade åtkomst nycklar**väljer du kopierings ikonen för **anslutnings strängen – primär nyckel** och spara värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Mer information om IoT Hub principer för delad åtkomst och behörigheter finns i [åtkomst kontroll och behörigheter](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
