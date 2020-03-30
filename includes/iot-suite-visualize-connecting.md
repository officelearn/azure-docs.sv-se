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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187777"
---
## <a name="view-device-telemetry"></a>Visa enhetstelemetri

Du kan visa den telemetri som skickas från enheten på sidan **Enhetsutforskaren** i lösningen.

1. Välj den enhet som du har etablerat i listan över enheter på sidan **Enhetsutforskaren.** En panel visar information om enheten, inklusive en ritordning av enhetens telemetri:

    ![Se enhetsinformation](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Välj **Tryck för** att ändra telemetridisplayen:

    ![Visa trycktelemetri](media/iot-suite-visualize-connecting/devicespressure.png)

1. Om du vill visa diagnostikinformation om enheten bläddrar du ned till **Diagnostik:**

    ![Visa enhetsdiagnostik](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agera på din enhet

Om du vill anropa metoder på dina enheter använder du sidan **Enhetsutforskaren** i lösningen För fjärrövervakning. I lösningen För fjärrövervakning **implementerar chiller-enheter** till exempel en **omstartsmetod.**

1. Välj **Enheter** för att navigera till sidan **Enhetsutforskaren** i lösningen.

1. Välj den enhet som du har etablerat i listan över enheter på sidan **Enhetsutforskaren:**

    ![Välj din riktiga enhet](media/iot-suite-visualize-connecting/devicesselect.png)

1. Om du vill visa en lista över de metoder du kan anropa på enheten väljer du **Jobb**och sedan **Metoder**. Om du vill schemalägga ett jobb så att det körs på flera enheter kan du välja flera enheter i listan. På panelen **Jobb** visas de typer av metoder som är gemensamma för alla enheter som du har valt.

1. Välj **Starta om**, ange jobbnamnet på **RebootPhysicalChiller** och välj sedan **Verkställ:**

    ![Schemalägg uppdateringen av den inbyggda programvaran](media/iot-suite-visualize-connecting/deviceschedule.png)

1. En sekvens av meddelanden visas i konsolen som kör enhetskoden medan den simulerade enheten hanterar metoden.

> [!NOTE]
> Om du vill spåra status för jobbet i lösningen väljer du **Visa jobbstatus**.

## <a name="next-steps"></a>Nästa steg

I artikeln [Anpassa lösningsacceleratorn för fjärrövervakning](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) beskrivs några sätt att anpassa lösningsacceleratorn.