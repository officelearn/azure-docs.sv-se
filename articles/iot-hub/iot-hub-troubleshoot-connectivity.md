---
title: Övervaka och Felsök från kopplingar med Azure IoT Hub
description: Lär dig att övervaka och felsöka vanliga fel med enhets anslutning för Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: abce9387d4c23cd6cb5ecf73e3d5c8428d83d459
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83833667"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Övervaka, diagnostisera och Felsök från koppling med Azure IoT Hub

Anslutnings problem för IoT-enheter kan vara svåra att felsöka eftersom det finns många möjliga fel punkter. Program logik, fysiska nätverk, protokoll, maskin vara, IoT Hub och andra moln tjänster kan orsaka problem. Det är viktigt att kunna identifiera och hitta källan till ett problem. En IoT-lösning i skala kan dock ha tusentals enheter, så det är inte praktiskt att kontrol lera enskilda enheter manuellt. För att hjälpa dig att identifiera, diagnostisera och felsöka problemen i stor skala, använder du övervaknings funktionerna IoT Hub tillhandahåller Azure Monitor. Dessa funktioner är begränsade till vad IoT Hub kan se, så vi rekommenderar också att du följer de rekommenderade metoderna för att övervaka dina enheter och andra Azure-tjänster.

## <a name="get-alerts-and-error-logs"></a>Hämta aviseringar och fel loggar

Använd Azure Monitor för att få aviseringar och skriva loggar när enheter kopplas från.

### <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar

Om du vill logga händelser och fel i enhets anslutningen aktiverar du diagnostik för IoT Hub. Vi rekommenderar att du aktiverar dessa loggar så tidigt som möjligt, eftersom om diagnostikloggar inte är aktive rad och det inte går att koppla från enheten, kommer du inte att ha någon information för att felsöka problemet med.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Bläddra till din IoT-hubb.

3. Välj **diagnostikinställningar**.

4. Välj **Aktivera diagnostik**.

5. Aktivera **anslutnings** loggar som ska samlas in.

6. För enklare analys aktiverar **du skicka till Log Analytics** ([Se prissättning](https://azure.microsoft.com/pricing/details/log-analytics/)). Se exemplet under [lösa anslutnings fel](#resolve-connectivity-errors).

   ![Rekommenderade inställningar](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Mer information finns i [övervaka hälso tillståndet för Azure IoT Hub och diagnostisera problem snabbt](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Konfigurera aviseringar för enhets anslutning i skala

Konfigurera aviseringar på måttet **anslutna enheter (förhands granskning)** om du vill få aviseringar när enheter kopplas från.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Bläddra till din IoT-hubb.

3. Välj **aviseringar**.

4. Välj **Ny aviseringsregel**.

5. Välj **Lägg till villkor**och välj sedan "anslutna enheter (förhands granskning)".

6. Konfigurera tröskelvärde och aviseringar genom att följa instruktionerna nedan.

Mer information finns i [Vad är aviseringar i Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Identifiera enskilda enheter från kopplingar

Om du vill identifiera koppling *per enhet* , till exempel när du behöver veta att en fabrik precis gick offline, [konfigurerar du enhets kopplings händelser med event Grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Lösa anslutnings fel

När du aktiverar diagnostikloggar och aviseringar för anslutna enheter får du aviseringar när fel inträffar. I det här avsnittet beskrivs hur du söker efter vanliga problem när du får en avisering. Stegen nedan förutsätter att du har konfigurerat Azure Monitor loggar för dina diagnostikloggar.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Bläddra till din IoT-hubb.

1. Välj **loggar**.

1. Om du vill isolera anslutnings fel loggar för IoT Hub anger du följande fråga och väljer sedan **Kör**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Om det finns resultat kan du söka efter `OperationName` , `ResultType` (felkod) och `ResultDescription` (fel meddelande) för att få mer information om felet.

   ![Exempel på fel logg](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Följ rikt linjerna för problemlösning för de vanligaste felen:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Jag försökte utföra stegen, men de fungerade inte

Om föregående steg inte hjälper kan du prova:

* Om du har åtkomst till de problematiska enheterna, antingen fysiskt eller via fjärr anslutning (som SSH), följer du [fel söknings guiden på enhets sidan](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) för att fortsätta fel sökningen.

* Kontrol lera att enheterna är **aktiverade** i Azure Portal > din iot Hub > IoT-enheter.

* Om enheten använder MQTT-protokoll kontrollerar du att port 8883 är öppen. Mer information finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Få hjälp från [Microsoft Q&en fråge sida för Azure IoT Hub](https://docs.microsoft.com/answers/topics/azure-iot-hub.html), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)eller [Azure-support](https://azure.microsoft.com/support/options/).

Du kan förbättra dokumentationen för alla genom att lämna en kommentar i avsnittet feedback nedan om den här guiden inte hjälper dig.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du löser tillfälliga problem finns i [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).

* Mer information om Azure IoT SDK och hur du hanterar återförsök finns i [hantera anslutningar och Reliable Messaging med hjälp av Azure IoT Hub-enhets-SDK](iot-hub-reliability-features-in-sdks.md#connection-and-retry): er.
