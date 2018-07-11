---
title: Använda en befintlig IoT-hubb med Enhetssimulering lösningen – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar Enhetssimulering lösningsaccelerator om du vill använda en befintlig IoT-hubb.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967560"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Använda en befintlig IoT-hubb med lösningsaccelerator Enhetssimulering

När du etablerar lösningsaccelerator Enhetssimulering kan välja du att distribuera en IoT-hubb i resursgruppen för solution accelerator ska användas i simuleringen.

Om du inte väljer att distribuera valfria IoT-hubben, måste du använda din egen hubb för alla simuleringar som du kör. Om du väljer att distribuera valfria IoT Hub, kan du använda den här valfria hub eller din egen hubb.

Om du inte har en IoT-hubb, kan du alltid skapa en ny etikett från den [Azure-portalen](https://portal.azure.com).

Om du vill använda en befintlig IoT-hubb, behöver du en anslutningssträng för den **iothubowner** delad åtkomstprincip. Du kan hämta den här anslutningssträngen från den [Azure-portalen](https://portal.azure.com):

1. På konfigurationssidan för hubbens blad i portalen, klickar du på **principer för delad åtkomst**.
1. Klicka på **iothubowner**.
1. Kopiera den primär eller sekundär anslutningssträngen.

[![Hämta anslutningssträngen](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Använd den anslutningssträng som du kopierade när du konfigurerar simuleringen:

[![Konfigurera simulering](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du använder en befintlig IoT-hubb i en simulering. Därefter kan du lära dig hur du [konfigurera en anpassad enhetsmodell](iot-accelerators-device-simulation-custom-model.md) för en simulering.
