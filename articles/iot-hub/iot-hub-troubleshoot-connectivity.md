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
ms.openlocfilehash: 6cc5e45ab28a1c83125a37cefb289b1662096eb0
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648827"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Identifiera och felsöka kopplar bort med Azure IoT Hub

Problem med nätverksanslutningen för IoT-enheter kan vara svårt att felsöka eftersom det finns många möjliga felpunkter. Enhetssidan programlogiken, fysiska nätverk, protokoll, maskinvara och Azure IoT Hub kan alla orsaka problem. Den här artikeln innehåller rekommendationer om hur du identifiera och felsöka problem med nätverksanslutningen enhet från molnet på klientsidan (i stället för enheten sida).

## <a name="get-alerts-and-error-logs"></a>Få aviseringar och felloggar

Använda Azure Monitor för att få aviseringar och skriva loggar när enhetsanslutningar släppa.

### <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar

Aktivera diagnostik för IoT Hub för att logga enheten anslutningshändelser och fel.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Bläddra till din IoT-hubb.
1. Välj **diagnostikinställningar**.
1. Välj **slå på diagnostik**.
1. Aktivera **anslutningar** loggar samlas in.
1. För enklare analys, aktivera **skicka till Log Analytics** ([finns i prisinformationen](https://azure.microsoft.com/pricing/details/log-analytics/)). Se exempel under [lösa anslutningsfel](#resolve-connectivity-errors).

   ![Rekommenderade inställningar][2]

Mer information finns i [övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Konfigurera aviseringar för den _anslutna enheter_ antal mått

Om du vill få aviseringar när Koppla från enheter, konfigurera aviseringar för den **anslutna enheter (förhandsversion)** mått.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Bläddra till din IoT-hubb.
1. Välj **aviseringar**.
1. Välj **ny aviseringsregel**.
1. Välj **Lägg till villkor**, välj sedan ”anslutna enheter (förhandsversion)”.
1. Slut ställa in din önskade trösklar och avisering alternativ genom att följande anvisningarna.

Mer information finns i [vad är klassiska aviseringar i Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Lösa anslutningsfel

När du aktiverar diagnostikloggar och varningar för anslutna enheter kan få du aviseringar när ett fel uppstår. Det här avsnittet beskriver hur du löser vanliga problem när du får en avisering. Stegen nedan förutsätter att du har konfigurerat Azure Monitor-loggar för dina diagnostikloggar.

1. Få din arbetsyta **Log Analytics** i Azure-portalen.
1. Välj **Loggsöknings**.
1. Om du vill isolera anslutning felloggar för IoT-hubb, anger du följande fråga och välj sedan **kör**:

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Om du får resultat, leta efter `OperationName`, `ResultType` (felkod:) och `ResultDescription` (ett felmeddelande) om du vill visa mer information om felet.

   ![Exempel på fellogg][4]

1. Du kan använda den här tabellen för att förstå och lösa vanliga fel.

    | Fel | Rotorsak | Lösning |
    |---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 404104 DeviceConnectionClosedRemotely | Anslutningen stängdes av enheten, men IoT Hub vet inte varför. Vanliga orsaker kan vara MQTT/AMQP anslutningsförlust timeout och internet. | Kontrollera att enheten kan ansluta till IoT Hub med [testar anslutningen](tutorial-connectivity.md). Om anslutningen är bra, men enheten kopplas från periodvis, se till att implementera logik som rätt keep alive enheten för ditt val av protokollet (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | IoT Hub gick inte att autentisera anslutningen. | Kontrollera att SAS eller andra säkerhetstoken som du använder inte gått ut. [Azure IoT SDK: er](iot-hub-devguide-sdks.md) automatiskt generera token utan att kräva särskild konfiguration. |
    | 409002 LinkCreationConflict | En enhet har fler än en anslutning. När en ny anslutningsbegäran gäller för en enhet, stängs den tidigare med det här felet i IoT Hub. | I de flesta fallen en enhet identifierar ett frånkoppling och försöker återupprätta anslutningen, men IoT Hub fortfarande tar hänsyn till den anslutna enheten. IoT Hub stängs den tidigare anslutningen och loggar det här felet. Det här felet visas vanligtvis som en sidoeffekt av ett annat, tillfälliga fel, så att leta efter andra fel i loggarna till felsökningen. Annars kan du se till att utfärda en ny begäran om anslutning om anslutningen bryts. |
    | 500001 ServerError | IoT Hub råkade ut för ett problem på serversidan. Mest sannolikt att problemet är tillfälligt. När IoT Hub-teamet fungerar svårt att underhålla [serviceavtalet](https://azure.microsoft.com/support/legal/sla/iot-hub/), små undergrupper av noder för IoT Hub kan ibland uppstår tillfälliga fel. När enheten försöker ansluta till en nod som har problem, får du detta felmeddelande. | För att lösa tillfälliga fel, utfärda ett nytt försök från enheten. Att [automatiskt hantera återförsök](iot-hub-reliability-features-in-sdks.md#connection-and-retry), kontrollera att du använder den senaste versionen av den [Azure IoT SDK: er](iot-hub-devguide-sdks.md).<br><br>Bästa metoder för hantering av tillfälliga fel och återförsök Se [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).  <br><br>Kontrollera om problemet kvarstår efter återförsök [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) och [Azure-Status](https://azure.microsoft.com/status/history/) om IoT Hub har ett känt problem. Om det finns några kända problem och problemet kvarstår [supporten](https://azure.microsoft.com/support/options/) för vidare studier. |
    | 500008 GenericTimeout | IoT Hub gick inte att slutföra begäran innan den avbryts. Precis som en 500001 ServerError är förmodligen övergående i det här felet. | Följ felsökningsstegen för en 500001 ServerError till den grundläggande orsaken och åtgärda felet.|

## <a name="other-steps-to-try"></a>Du gör

Om föregående steg inte hjälper kan du:

* Om du har åtkomst till problematiska enheterna, fysiskt eller via en fjärranslutning (till exempel SSH), följer du de [enhetssidan felsökningsguide för](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) fortsätta felsökning.
* Kontrollera att enheterna är **aktiverad** i Azure portal > din IoT-hubb > IoT-enheter.
* Få hjälp från [Azure IoT Hub-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), eller [Azure-supporten](https://azure.microsoft.com/support/options/).

Lämna en kommentar i feedback nedan för att förbättra dokumentationen för alla användare, om den här guiden hjälpte dig.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du löser problem finns [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
* Läs mer om Azure IoT SDK och hantera återförsök, se [hur du hanterar anslutningar och tillförlitlig meddelandehantering med hjälp av Azure IoT Hub device-SDKs](iot-hub-reliability-features-in-sdks.md#connection-and-retry).

<!-- Images -->
[1]: ../../includes/media/iot-hub-diagnostics-settings/turnondiagnostics.png
[2]: ./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png
[3]: ./media/iot-hub-troubleshoot-connectivity/metric-alert.png
[4]: ./media/iot-hub-troubleshoot-connectivity/diag-logs.png
