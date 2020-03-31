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
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "69558506"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Så här hämtar du IoT **service** Hub-anslutningssträngen för tjänstprincipen:

1. Välj **Resursgrupper**i [Azure-portalen](https://portal.azure.com). Välj den resursgrupp där hubben finns och välj sedan hubben i listan över resurser.

1. Välj Principer för **delad åtkomst**i den vänstra rutan i IoT-hubben .

1. Välj **serviceprincipen** i listan över principer.

1. Under **Delade åtkomstnycklar**väljer du **kopieringsikonen för anslutningssträngen – primärnyckeln** och sparar värdet.

    ![Visa hur anslutningssträngen hämtas](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Mer information om principer och behörigheter för IoT Hub-delad åtkomst finns i [Åtkomstkontroll och behörigheter](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
