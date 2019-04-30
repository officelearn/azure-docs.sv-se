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
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449747"
---
## <a name="view-device-telemetry"></a>Visa enhetstelemetri

Du kan visa telemetri som skickas från din enhet den **Device Explorer** sidan i lösningen.

1. Välj den enhet som du har etablerat i listan över enheter på den **Device Explorer** sidan. En panel visar information om enheten till exempel en rityta för enhetstelemetri som:

    ![Visa information om enhet](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Välj **tryck** ändra visningen av telemetri:

    ![Visa tryck telemetri](media/iot-suite-visualize-connecting/devicespressure.png)

1. Om du vill visa diagnostisk information om din enhet, bläddra ned till **diagnostik**:

    ![Visa enhet diagnostik](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agera på din enhet

Om du vill anropa metoder på dina enheter, använda den **Device Explorer** sida i lösningen för fjärrövervakning. Till exempel i lösningen för fjärrövervakning **kylaggregat** enheter implementera en **omstart** metod.

1. Välj **enheter** att navigera till den **Device Explorer** sidan i lösningen.

1. Välj den enhet som du har etablerat i listan över enheter på den **Device Explorer** sidan:

    ![Välj den verkliga enheten](media/iot-suite-visualize-connecting/devicesselect.png)

1. Om du vill visa en lista över de metoder som du kan anropa på enheten, Välj **jobb**, sedan **metoder**. Om du vill schemalägga ett jobb ska köras på flera enheter, kan du välja flera enheter i listan. Den **jobb** panelen visas typerna av metoden som är gemensamma för alla enheter som du har valt.

1. Välj **omstart**, inställd på jobbnamnet **RebootPhysicalChiller** och välj sedan **tillämpa**:

    ![Schemalägg uppdatering av inbyggd programvara](media/iot-suite-visualize-connecting/deviceschedule.png)

1. En serie meddelanden visas i konsolen som kör enhetskoden när den simulerade enheten hanterar metoden.

> [!NOTE]
> Om du vill spåra statusen för jobbet i lösningen, Välj **Visa jobbstatus**.

## <a name="next-steps"></a>Nästa steg

Artikeln [anpassa lösningsacceleratorn för fjärrövervakning](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) beskriver några sätt att anpassa solution accelerator.