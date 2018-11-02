---
title: Använda en befintlig IoT-hubb med Enhetssimulering lösningen – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar Enhetssimulering lösningsaccelerator om du vill använda en befintlig IoT-hubb.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 38cde750ce07741a433baa1b8607a584f94ad9b1
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50753928"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Använda en befintlig IoT-hubb med lösningsaccelerator Enhetssimulering

När du distribuerar Enhetssimulering kan välja du också att distribuera en IoT-hubb som ska användas i simuleringen. Det här alternativet distribuerar en [S2-nivån IoT hub med en enda skalningsenhet](../iot-hub/iot-hub-scaling.md). Om du distribuerar den här valfria IoT-hubb, kan du fortfarande välja att rikta en annan IoT-hubb som en simulering körning.

Om du väljer att inte distribuera valfria IoT-hubben, måste du använda din egen hubb för alla simuleringar som du kör.

Om du inte har en IoT-hubb, kan du alltid skapa en ny etikett från den [Azure-portalen](https://portal.azure.com).

Om du vill använda en befintlig IoT-hubb, behöver du anslutningssträngen för den **iothubowner** delad åtkomstprincip. Du kan hämta den här anslutningssträngen från den [Azure-portalen](https://portal.azure.com):

1. På konfigurationssidan för hubbens blad i portalen, klickar du på **principer för delad åtkomst**.

1. Klicka på **iothubowner**.

1. Kopiera den primär eller sekundär anslutningssträngen.

[![Hämta anslutningssträngen](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Använd den anslutningssträng som du kopierade när du konfigurerar simuleringen:

![Konfigurera simulering](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du använder en befintlig IoT-hubb i en simulering. Därefter kan du lära dig hur du [skapa en avancerad enhetsmodell](iot-accelerators-device-simulation-advanced-device.md) för en simulering.
