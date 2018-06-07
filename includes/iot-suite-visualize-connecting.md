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
ms.openlocfilehash: 5702c6e9c9d75c6cccb82f1c57684ef7b9898c34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34666032"
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

Om du vill anropa metoder på dina enheter, använder den **enheter** sida i Fjärrövervaknings-lösning. Till exempel i lösningen Fjärrövervaknings **kylaggregat** enheter implementera en **FirmwareUpdate** metod.

1. Välj **enheter** att navigera till den **enheter** sida i lösningen.

1. Välj den enhet som du har etablerats i listan över enheter på den **enheter** sidan:

    ![Välj den fysiska enheten](media/iot-suite-visualize-connecting/devicesselect.png)

1. Om du vill visa en lista över de metoder som du kan anropa på enheten, Välj **jobb**, sedan **metoden kör**. Du kan välja flera enheter i listan om du vill schemalägga ett jobb ska köras på flera enheter. Den **jobb** panelen visas typerna av metoden som är gemensamma för alla enheter som du har valt.

1. Välj **FirmwareUpdate**, ange Jobbnamnet på **UpdatePhysicalChiller**. Ange **Version på inbyggd programvara** till **2.0.0**, ange **Firmware URI** till **http://contoso.com/updates/firmware.bin**, och välj sedan **tillämpa**:

    ![Schemalägga firmware-uppdatering](media/iot-suite-visualize-connecting/deviceschedule.png)

1. En sekvens av meddelanden som visas i konsolen köra koden enhet medan den simulerade enheten hanterar metoden.

1. När uppdateringen är klar visas den nya versionen av inbyggd programvara på den **enheter** sidan:

    ![Uppdateringen slutfördes](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> För att spåra status för jobb i lösningen, Välj **visa**.

## <a name="next-steps"></a>Nästa steg

Artikeln [anpassa Fjärrövervaknings solution accelerator](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) beskrivs några sätt att anpassa solution accelerator.