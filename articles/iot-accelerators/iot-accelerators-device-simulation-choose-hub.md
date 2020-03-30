---
title: Använda befintlig IoT-hubb med enhetssimuleringslösning - Azure | Microsoft-dokument
description: I den här artikeln beskrivs hur du konfigurerar lösningsacceleratorn för enhetssimulering för att använda en befintlig IoT Hub.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889183"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Använda en befintlig IoT Hub med lösningsaccelerator för enhetssimulering

När du distribuerar Enhetssimulering kan du också välja att distribuera en IoT-hubb som ska användas i simuleringen. Det här alternativet distribuerar en [IoT-hubb på S2-nivå med en enhet med en enda skalning](../iot-hub/iot-hub-scaling.md). Om du distribuerar den här valfria IoT-hubben kan du fortfarande välja att rikta in dig på en annan IoT Hub för en simuleringskörning.

Om du väljer att inte distribuera den valfria IoT Hub måste du använda din egen hubb för alla simuleringar du kör.

Om du inte har en IoT-hubb kan du alltid skapa en ny från [Azure-portalen](https://portal.azure.com).

Om du vill använda en befintlig IoT-hubb behöver du anslutningssträngen för principen för delad åtkomst för **iothubowner.** Du kan hämta den här anslutningssträngen från [Azure-portalen:](https://portal.azure.com)

1. Klicka på Principer för **delad åtkomst**på hubbens konfigurationssida i portalen .

1. Klicka på **iothubowner**.

1. Kopiera antingen den primära eller sekundära anslutningssträngen.

[![Hämta anslutningssträng](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Använd anslutningssträngen som du kopierade när du konfigurerade simuleringen:

![Konfigurera simulering](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du använder ett befintligt IoT-nav i en simulering. Därefter kanske du vill lära dig hur du [skapar en avancerad enhetsmodell](iot-accelerators-device-simulation-advanced-device.md) för en simulering.
