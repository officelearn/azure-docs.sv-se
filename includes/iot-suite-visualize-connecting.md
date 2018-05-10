---
title: ta med fil
description: ta med fil
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 8eada6b88c320396b30ecdeb96c95e4ec61679ce
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
## <a name="view-device-telemetry"></a>Visa enhetstelemetri

Du kan visa telemetri som skickas från enheten på den **enheter** sida i lösningen.

1. Välj den enhet som du har etablerats i listan över enheter på den **enheter** sidan. En panel visar information om enheten till exempel ett diagram där enheten telemetri:

    ![Se detaljer för enhet](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Välj **tryck** ändra visningen av telemetri:

    ![Visa trycket telemetri](media/iot-suite-visualize-connecting/devicespressure.png)

1. Om du vill visa diagnostisk information om enheten rulla ned till **diagnostik**:

    ![Visa enheten diagnostik](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Fungerar på din enhet

Om du vill anropa metoder på dina enheter, använder den **enheter** sida i fjärranslutna övervakningslösning. Till exempel i fjärranslutna övervakningslösning **kylaggregat** enheter implementera en **FirmwareUpdate** metod.

1. Välj **enheter** att navigera till den **enheter** sida i lösningen.

1. Välj den enhet som du har etablerats i listan över enheter på den **enheter** sidan:

    ![Välj den fysiska enheten](media/iot-suite-visualize-connecting/devicesselect.png)

1. Om du vill visa en lista över de metoder som du kan anropa på enheten, Välj **schema**. Du kan välja flera enheter i listan om du vill schemalägga en metod för att köras på flera enheter. Den **schema** panelen visas typerna av metoden som är gemensamma för alla enheter som du har valt.

1. Välj **FirmwareUpdate**, ange Jobbnamnet på **UpdatePhysicalChiller**. Ange **Version på inbyggd programvara** till **2.0.0**, ange **Firmware URI** till **http://contoso.com/updates/firmware.bin**, och välj sedan **tillämpa**:

    ![Schemalägga firmware-uppdatering](media/iot-suite-visualize-connecting/deviceschedule.png)

1. En sekvens av meddelanden som visas i konsolen köra koden enhet medan den simulerade enheten hanterar metoden.

1. När uppdateringen är klar visas den nya versionen av inbyggd programvara på den **enheter** sidan:

    ![Uppdateringen slutfördes](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> För att spåra status för jobb i lösningen, Välj **visa**.

## <a name="next-steps"></a>Nästa steg

Artikeln [anpassa fjärråtkomst övervakning solution accelerator](../articles/iot-suite/iot-suite-remote-monitoring-customize.md) beskrivs några sätt att anpassa solution accelerator.