---
title: Övervaka och felsöka frånkopplingar med Azure IoT Hub
description: Lär dig att övervaka och felsöka vanliga fel med enhetsanslutning för Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: bed6736fda0c1815964f9017adb1e6fffa9335d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110686"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Övervaka, diagnostisera och felsöka frånkopplingar med Azure IoT Hub

Anslutningsproblem för IoT-enheter kan vara svåra att felsöka eftersom det finns många möjliga felpunkter. Programlogik, fysiska nätverk, protokoll, maskinvara, IoT Hub och andra molntjänster kan alla orsaka problem. Möjligheten att identifiera och identifiera källan till ett problem är avgörande. En IoT-lösning i stor skala kan dock ha tusentals enheter, så det är inte praktiskt att kontrollera enskilda enheter manuellt. Använd övervakningsfunktionerna i IoT Hub via Azure Monitor för att identifiera, diagnostisera och felsöka dessa problem i stor skala. Dessa funktioner är begränsade till vad IoT Hub kan observera, så vi rekommenderar också att du följer metodtips för dina enheter och andra Azure-tjänster.

## <a name="get-alerts-and-error-logs"></a>Få aviseringar och felloggar

Använd Azure Monitor för att få aviseringar och skriva loggar när enheter kopplas från.

### <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar

Om du vill logga enhetsanslutningshändelser och fel aktiverar du diagnostik för IoT Hub. Vi rekommenderar att du aktiverar dessa loggar så tidigt som möjligt, för om diagnostikloggar inte är aktiverade, när enheten kopplas in, har du ingen information att felsöka problemet med.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Bläddra till din IoT-hubb.

3. Välj **diagnostikinställningar**.

4. Välj **Aktivera diagnostik**.

5. Aktivera **anslutningar** loggar som ska samlas in.

6. För enklare analys, aktivera **Skicka till Logganalys** ([se priser](https://azure.microsoft.com/pricing/details/log-analytics/)). Se exemplet under [Lös anslutningsfel](#resolve-connectivity-errors).

   ![Rekommenderade inställningar](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Mer information finns [i Övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Ställ in aviseringar för enhetsfrånkoppling i stor skala

Om du vill få aviseringar när enheter kopplas från konfigurerar du aviseringar på måttet **Anslutna enheter (förhandsversion).**

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Bläddra till din IoT-hubb.

3. Välj **Aviseringar**.

4. Välj **Ny aviseringsregel**.

5. Välj **Lägg till villkor**och välj sedan "Anslutna enheter (förhandsgranskning)".

6. Ställ in tröskelvärde och aviseringar genom att följa uppmaningar.

Mer information finns [i Vad är aviseringar i Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Identifiera enskilda enheter frånkopplingar

Om du vill identifiera *frånkopplingar per enhet,* till exempel när du behöver veta att en fabrik just har gått offline, [konfigurerar du frånkoppling av enheten med Event Grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Lösa anslutningsfel

När du aktiverar diagnostikloggar och aviseringar för anslutna enheter får du aviseringar när fel uppstår. I det här avsnittet beskrivs hur du letar efter vanliga problem när du får en avisering. Stegen nedan förutsätter att du har konfigurerat Azure Monitor-loggar för dina diagnostikloggar.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Bläddra till din IoT-hubb.

1. Välj **Loggar**.

1. Om du vill isolera anslutningsfelloggar för IoT Hub anger du följande fråga och väljer sedan **Kör:**

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Om det finns resultat `OperationName` `ResultType` letar du efter `ResultDescription` , (felkod) och (felmeddelande) för att få mer information om felet.

   ![Exempel på fellogg](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Följ stödlinjerna för problemlösning för de vanligaste felen:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Jag försökte stegen, men de fungerade inte

Om de föregående stegen inte hjälpte kan du prova:

* Om du har åtkomst till de problematiska enheterna, antingen fysiskt eller på distans (som SSH), följer du [felsökningsguiden på enhetssidan](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) för att fortsätta felsökningen.

* Kontrollera att dina enheter är **aktiverade** i Azure-portalen > din IoT-hubb > IoT-enheter.

* Om enheten använder MQTT-protokollet kontrollerar du att port 8883 är öppen. Mer information finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Få hjälp från [Azure IoT Hub-forum,](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub) [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)eller [Azure-support](https://azure.microsoft.com/support/options/).

Lämna en kommentar i feedbackavsnittet nedan om den här guiden inte hjälpte dig för att förbättra dokumentationen för alla.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du löser tillfälliga problem finns i [Övergående felhantering](/azure/architecture/best-practices/transient-faults).

* Mer information om Azure IoT SDK och hantera återförsök finns i [Hantera anslutning och tillförlitliga meddelanden med Azure IoT Hub-enheten SDK: er](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
