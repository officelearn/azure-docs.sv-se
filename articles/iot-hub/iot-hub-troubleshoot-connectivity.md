---
title: Diagnostisera och felsöka kopplar bort med Azure IoT Hub
description: Lär dig att diagnostisera och felsöka vanliga fel med enhetsanslutning för Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: eb186f4b6e1d742c9cae51e68b3d3dbda1bb751c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400422"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Identifiera och felsöka kopplar bort med Azure IoT Hub

Problem med nätverksanslutningen för IoT-enheter kan vara svårt att felsöka eftersom det finns många möjliga felpunkter. Enhetssidan programlogiken, fysiska nätverk, protokoll, maskinvara och Azure IoT Hub kan alla orsaka problem. Det här dokumentet innehåller rekommendationer om hur du identifiera och felsöka problem med nätverksanslutningen enhet från molnet till sida (i stället för enhetssidan).

## <a name="get-alerts-and-error-logs"></a>Få aviseringar och felloggar

Använda Azure Monitor för att få aviseringar och skriva loggar när enhetsanslutningar släppa.

### <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar 

Aktivera diagnostik för IoT Hub för att logga enheten anslutningshändelser och fel. 

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till IoT-hubben.
1. Välj **diagnostikinställningar**.
1. Välj sedan **slå på diagnostik**.
1. Kontrollera att du aktiverar **anslutningar** loggar samlas in. 
1. För att underlätta analys, bör du aktivera **skicka till Log Analytics** ([finns i prisinformationen](https://azure.microsoft.com/pricing/details/log-analytics/)). Ett exempel senare i artikeln använder Log Analytics.

   ![Rekommenderade inställningar][2]

Mer information finns i [övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Konfigurera aviseringar för anslutna enheter count-mått

Om du vill få aviseringar när Koppla från enheter, konfigurera aviseringar för den *anslutna enheter* mått. 

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till din IoT-hubb.
1. Välj **aviseringar (klassisk)**.
1. Klicka på **Lägg till måttavisering (klassisk)**.
1. Fyll i formuläret och välj **OK**. 

   ![Rekommenderade metrisk varning][3]

Mer information finns i [vad är klassiska aviseringar i Microsoft Azure?](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="resolve-common-connectivity-errors"></a>Lös vanliga anslutningsfel

När diagnostiska loggar och aviseringar för anslutna enheter är aktiverat kan få du aviseringar om något går fel. Det här avsnittet beskriver hur du löser vanliga problem när du får en avisering. Stegen nedan förutsätter att du har konfigurerat Log Analytics för dina diagnostikloggar. 

1. Få din arbetsyta **Log Analytics** i Azure-portalen.
1. Klicka på **Loggsöknings**.
1. Om du vill isolera anslutning felloggar för IoT-hubb, ange den här frågan i rutan Tryck **kör**.

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Om du får resultat, leta efter den `OperationName`, `ResultType` (felkod:) och `ResultDescription` (ett felmeddelande) om du vill visa mer information om felet.

   ![Exempel på fellogg][4]

1. Du kan använda den här tabellen för att förstå och lösa vanliga fel.

    | Fel | Rotorsak | Lösning |
    |---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 404104 DeviceConnectionClosedRemotely | Anslutningen stängdes av enheten men IoT Hub vet inte varför. Vanliga orsaker kan vara MQTT/AMQP anslutningsförlust timeout och internet. | Kontrollera att enheten kan ansluta till IoT Hub med [testar anslutningen](tutorial-connectivity.md). Om anslutningen är bra, men enheten kopplas från periodvis, se till att implementera logik som rätt keep alive enheten för ditt val av protokollet (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | IoT Hub gick inte att autentisera anslutningen. | Kontrollera att SAS eller andra säkerhetstoken som du använder inte gått ut. [Azure IoT SDK: er](iot-hub-devguide-sdks.md) automatiskt generera token utan att kräva särskild konfiguration. |
    | 409002 LinkCreationConflict | Det finns flera anslutningar för samma enhet. När en ny anslutningsbegäran gäller för en enhet, stängs den tidigare med det här felet i IoT Hub. | Se till att utfärda en ny begäran om anslutning om anslutningen bryts. |
    | 500001 ServerError | IoT Hub råkade ut för ett problem på serversidan. Mest sannolikt att problemet är tillfälligt. När IoT Hub-teamet fungerar svårt att underhålla [serviceavtalet](https://azure.microsoft.com/support/legal/sla/iot-hub/), små undergrupper av noder för IoT Hub kan ibland uppstår tillfälliga fel. När enheten försöker ansluta till en nod som har problem, får du detta felmeddelande. | För att lösa tillfälliga fel, utfärda ett nytt försök från enheten. Att [automatiskt hantera återförsök](iot-hub-reliability-features-in-sdks.md), kontrollera att du använder den senaste versionen av den [Azure IoT SDK: er](iot-hub-devguide-sdks.md).<br><br>Bästa metoder för hantering av tillfälliga fel och återförsök Se [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults.md).  <br><br>Kontrollera om problemet kvarstår efter återförsök [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) och [Azure-Status](https://azure.microsoft.com/status/history/) om IoT Hub har ett känt problem. Om det finns några kända problem och problemet kvarstår [supporten](https://azure.microsoft.com/support/options/) för vidare studier. |
    | 500008 GenericTimeout | IoT Hub gick inte att slutföra begäran innan den avbryts. Det här felet är förmodligen övergående som 500001 ServerError. | Följ felsökningsstegen för 500001 ServerError orsak och åtgärda felet.|

## <a name="other-steps-to-try"></a>Du gör

Om stegen ovan inte hjälpte, är här några saker att prova:

* Om du har åtkomst till problematiska enheterna, fysiskt eller via en fjärranslutning (till exempel SSH), följer du de [enhetssidan felsökningsguide för](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) fortsätta felsökning.
* Kontrollera att enheterna är **aktiverad** i Azure portal > din IoT-hubb > IoT-enheter.
* Få hjälp från [Azure IoT Hub-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), eller [Azure-supporten](https://azure.microsoft.com/support/options/).

Lämna en kommentar nedan för att förbättra dokumentationen för alla användare, om den här guiden hjälpte dig.

<!-- Images -->
[1]: ../../includes/media/iot-hub-diagnostics-settings/turnondiagnostics.png
[2]: ./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png
[3]: ./media/iot-hub-troubleshoot-connectivity/metric-alert.png
[4]: ./media/iot-hub-troubleshoot-connectivity/diag-logs.png
