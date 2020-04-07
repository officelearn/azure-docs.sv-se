---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757018"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Så här skapar du en princip för delad åtkomst som ger **tjänstanslutning** och **registerskrivningsbehörighet** och skaffar en anslutningssträng för den här principen:

1. Välj **Resursgrupper**i [Azure-portalen](https://portal.azure.com). Välj den resursgrupp där hubben finns och välj sedan hubben i listan över resurser.

1. Välj Principer för **delad åtkomst**i den vänstra rutan i navet .

1. Välj **Lägg till**i den övre menyn ovanför listan över principer.

1. Under **Lägg till en princip**för delad åtkomst anger du ett beskrivande namn för principen, till exempel *serviceAndRegistryReadWrite*. Under **Behörigheter**väljer du **Registerskrivning** och **Tjänstanslutning**och väljer sedan **Skapa**. **(Läsbehörigheten för registret** inkluderas automatiskt när du väljer **Registeredörskrivning**.)

    ![Visa hur du lägger till en ny princip för delad åtkomst](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Välj din nya princip i listan över principer.

1. Under **Delade åtkomstnycklar**väljer du **kopieringsikonen för anslutningssträngen – primärnyckeln** och sparar värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Mer information om principer och behörigheter för IoT Hub-delad åtkomst finns i [Åtkomstkontroll och behörigheter](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
