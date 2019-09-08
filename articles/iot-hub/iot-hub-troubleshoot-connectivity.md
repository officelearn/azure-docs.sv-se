---
title: Diagnostisera och Felsök från kopplingar med Azure IoT Hub
description: Lär dig att diagnostisera och felsöka vanliga fel med enhets anslutning för Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 3904c6390cfe8de197bae470c4ae32d22605ae6a
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801424"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Identifiera och Felsök från kopplingar med Azure IoT Hub

Anslutnings problem för IoT-enheter kan vara svåra att felsöka eftersom det finns många möjliga fel punkter. Program logik på enhets sidan, fysiska nätverk, protokoll, maskin vara och Azure IoT Hub kan alla orsaka problem. Den här artikeln innehåller rekommendationer om hur du identifierar och felsöker problem med enhets anslutningar från molnet (till skillnad från enhets sidan).

## <a name="get-alerts-and-error-logs"></a>Hämta aviseringar och fel loggar

Använd Azure Monitor för att hämta aviseringar och skriva loggar när enhets anslutningar släpps.

### <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar

Om du vill logga händelser och fel i enhets anslutningen aktiverar du diagnostik för IoT Hub.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Bläddra till din IoT-hubb.

3. Välj **diagnostikinställningar**.

4. Välj **slå på diagnostik**.

5. Aktivera **anslutnings** loggar som ska samlas in.

6. För enklare analys aktiverar **du skicka till Log Analytics** ([Se prissättning](https://azure.microsoft.com/pricing/details/log-analytics/)). Se exemplet under [lösa anslutnings fel](#resolve-connectivity-errors).

   ![Rekommenderade inställningar](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Mer information finns i [övervaka hälso tillståndet för Azure IoT Hub och diagnostisera problem snabbt](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-_connected-devices_-count-metric"></a>Konfigurera aviseringar för måttet antal _anslutna enheter_

Konfigurera aviseringar på måttet **anslutna enheter (förhands granskning)** om du vill få aviseringar när enheter kopplas från.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Bläddra till din IoT-hubb.

3. Välj **aviseringar**.

4. Välj **ny aviserings regel**.

5. Välj **Lägg till villkor**och välj sedan "anslutna enheter (förhands granskning)".

6. Slutför konfigurationen av önskade tröskelvärden och aviserings alternativ genom att följa instruktionerna.

Mer information finns i [Vad är klassiska varningar i Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Lösa anslutnings fel

När du aktiverar diagnostikloggar och aviseringar för anslutna enheter får du aviseringar när fel inträffar. I det här avsnittet beskrivs hur du löser vanliga problem när du får en avisering. Stegen nedan förutsätter att du har konfigurerat Azure Monitor loggar för dina diagnostikloggar.

1. Gå till arbets ytan för **Log Analytics** i Azure Portal.

2. Välj **loggs ökning**.

3. Om du vill isolera anslutnings fel loggar för IoT Hub anger du följande fråga och väljer sedan **Kör**:

    ```kusto
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Om det finns resultat kan du söka `OperationName`efter `ResultType` , (felkod) och `ResultDescription` (fel meddelande) för att få mer information om felet.

   ![Exempel på fel logg](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Använd den här tabellen för att förstå och lösa vanliga fel.

    | Fel | Rotorsak | Lösning |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | Anslutningen stängdes av enheten, men IoT Hub inte veta varför. Vanliga orsaker är MQTT/AMQP-tidsgräns och förlust av Internet anslutning. | Kontrol lera att enheten kan ansluta till IoT Hub genom att [testa anslutningen](tutorial-connectivity.md). Om anslutningen är korrekt, men enheten kopplas från period vis, så se till att implementera korrekt Keep Alive-enhets logik för ditt val av protokoll (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | IoT Hub inte att autentisera anslutningen. | Se till att den SAS eller andra säkerhetstoken som du använder inte har upphört att gälla. [Azure IoT SDK](iot-hub-devguide-sdks.md) : er genererar automatiskt tokens utan att kräva särskild konfiguration. |
    | 409002 LinkCreationConflict | En enhet har mer än en anslutning. När en ny anslutningsbegäran kommer till en enhet stänger IoT Hub den tidigare filen med det här felet. | I det vanligaste fallet identifierar en enhet en från koppling och försöker återupprätta anslutningen, men IoT Hub fortfarande anser att enheten är ansluten. IoT Hub stänger den tidigare anslutningen och loggar det här felet. Det här felet visas vanligt vis som en sido effekt av ett annat, tillfälligt problem. Leta efter andra fel i loggarna för att felsöka. Annars, se till att utfärda en ny anslutningsbegäran endast om anslutningen tappas. |
    | 500001 ServerError | IoT Hub stötte på ett problem på Server sidan. Förmodligen är problemet tillfälligt. Medan IoT Hub team arbetar hårt för att underhålla [service avtalet](https://azure.microsoft.com/support/legal/sla/iot-hub/)kan små del mängder av IoT Hub-noder ibland uppleva tillfälliga fel. När enheten försöker ansluta till en nod som har problem får du det här felet. | Om du vill minimera det tillfälliga felet utfärdar du ett nytt försök från enheten. Kontrol lera att du använder den senaste versionen av [Azure IoT SDK](iot-hub-devguide-sdks.md): er för att [automatiskt hantera återförsök](iot-hub-reliability-features-in-sdks.md#connection-and-retry).<br><br>Bästa praxis vid hantering av tillfälliga fel och återförsök finns i [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).  <br><br>Om problemet kvarstår efter återförsök kan du kontrol lera [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) och [Azure-status](https://azure.microsoft.com/status/history/) för att se om IoT Hub har ett känt problem. Om det inte finns några kända problem och problemet kvarstår kan du [kontakta supporten](https://azure.microsoft.com/support/options/) för ytterligare undersökning. |
    | 500008 GenericTimeout | Det gick inte att slutföra anslutningsbegäran innan tids gränsen nåddes för IoT Hub. Precis som en 500001-ServerError är det här felet troligt vis tillfälligt. | Följ fel söknings stegen för en 500001-ServerError till rotor saken och Lös det här felet.|

## <a name="other-steps-to-try"></a>Andra steg att prova

Om föregående steg inte hjälper kan du prova:

* Om du har åtkomst till de problematiska enheterna, antingen fysiskt eller via fjärr anslutning (som SSH), följer du [fel söknings guiden på enhets sidan](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) för att fortsätta fel sökningen.

* Kontrol lera att enheterna är **aktiverade** i Azure Portal > din iot Hub > IoT-enheter.

* Få hjälp från [azure IoT Hub forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)eller [Azure-supporten](https://azure.microsoft.com/support/options/).

Du kan förbättra dokumentationen för alla genom att lämna en kommentar i avsnittet feedback nedan om den här guiden inte hjälper dig.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du löser tillfälliga problem finns i [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).

* Mer information om Azure IoT SDK och hur du hanterar återförsök finns i [hantera anslutningar och Reliable Messaging med hjälp av Azure IoT Hub-enhets-SDK](iot-hub-reliability-features-in-sdks.md#connection-and-retry): er.
