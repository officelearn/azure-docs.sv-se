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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187777"
---
## <a name="view-device-telemetry"></a>Visa enhetstelemetri

Du kan visa telemetri som skickas från enheten på sidan **Device Explorer** i lösningen.

1. Välj den enhet som du etablerade i listan över enheter på **Device Explorer** sidan. En panel visar information om enheten, inklusive ett observations område för enhetens telemetri:

    ![Se enhets information](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Välj **tryck** för att ändra visningen av telemetri:

    ![Visa tryck telemetri](media/iot-suite-visualize-connecting/devicespressure.png)

1. Om du vill visa diagnostikinformation om enheten rullar du ned till **diagnostik**:

    ![Visa enhetsdiagnostik](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agera på din enhet

Om du vill anropa metoder på dina enheter använder du sidan **Device Explorer** i lösningen för fjärrövervakning. I till exempel fjärrstyrda **styrenheter** för övervakning implementerar du en metod för **omstart** .

1. Välj **enheter** för att navigera till **Device Explorer** sidan i lösningen.

1. Välj den enhet som du etablerade i listan över enheter på **Device Explorer** sidan:

    ![Välj din riktiga enhet](media/iot-suite-visualize-connecting/devicesselect.png)

1. Om du vill visa en lista över de metoder som du kan anropa på enheten väljer du **jobb**och sedan **metoder**. Om du vill schemalägga ett jobb att köras på flera enheter kan du välja flera enheter i listan. Panelen **jobb** visar de olika typerna av metoden som är gemensamma för alla enheter som du har valt.

1. Välj **starta om**, ange jobb namnet till **RebootPhysicalChiller** och välj sedan **Använd**:

    ![Schemalägga uppdatering av inbyggd program vara](media/iot-suite-visualize-connecting/deviceschedule.png)

1. En sekvens med meddelanden visas i-konsolen som kör din enhets kod medan den simulerade enheten hanterar metoden.

> [!NOTE]
> Om du vill spåra jobbets status i lösningen väljer du **Visa jobb status**.

## <a name="next-steps"></a>Nästa steg

I artikeln [anpassar du lösnings acceleratorn för fjärrövervakning](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) beskrivs några sätt att anpassa Solution Accelerator.