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
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050422"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Så här skapar du en princip för delad åtkomst som ger **tjänsten läsbehörighet** och får en anslutningssträng för den här principen: **registry read**

1. Välj **Resursgrupper**i [Azure-portalen](https://portal.azure.com). Välj den resursgrupp där hubben finns och välj sedan hubben i listan över resurser.

1. Välj Principer för **delad åtkomst**i den vänstra rutan i navet .

1. Välj **Lägg till**i den övre menyn ovanför listan över principer.

1. Under **Lägg till en princip**för delad åtkomst anger du ett beskrivande namn för principen, till exempel *serviceAndRegistryRead*. Under **Behörigheter**väljer du **Läsa register** och **Tjänstanslutning**och väljer sedan **Skapa**.

    ![Visa hur du lägger till en ny princip för delad åtkomst](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Välj din nya princip i listan över principer.

1. Under **Delade åtkomstnycklar**väljer du **kopieringsikonen för anslutningssträngen – primärnyckeln** och sparar värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Mer information om principer och behörigheter för IoT Hub-delad åtkomst finns i [Åtkomstkontroll och behörigheter](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
