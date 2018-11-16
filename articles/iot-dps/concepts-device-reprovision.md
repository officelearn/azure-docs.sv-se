---
title: Enhetskoncept för reprovisioning för Azure IoT Hub Device Provisioning-tjänsten | Microsoft Docs
description: Beskriver enheten reprovisioning begrepp för Azure IoT Hub Device Provisioning-tjänsten
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: f52e2a1095c329aabf44a846a644cc05548d4df3
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712287"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT Hub Device reprovisioning begrepp

Under livscykeln för en IoT-lösning är det vanligt att flytta enheter mellan IoT-hubbar. Orsaker till flytten kan innehålla följande scenarier:

* **Geoplats / GeoLatency**: när en enhet flyttas mellan platser Nätverksfördröjningen förbättras genom att låta enheten migreras till en närmare IoT-hubb.

* **Multitenans**: en enhet kan användas inom samma IoT-lösning och omtilldelas till en ny kund eller kundens plats. Den här nya kunden kan betjänas med hjälp av en annan IoT-hubb.

* **Lösning ändra**: en enhet kan flyttas till en ny eller uppdaterad IoT-lösning. Omtilldelningen kan kräva att enheten att kommunicera med en ny IoT-hubb som är ansluten till andra backend-komponenter.

* **Karantän**: liknar en ändring av lösningen. En enhet som fungerar, komprometteras eller inaktuella som kan tilldelas till en IoT-hubb kan bara uppdatera och få tillbaka efterlevnad. När enheten fungerar korrekt, har den sedan migreras till dess huvudsakliga hub.

Reprovisioning stöd i Device Provisioning-tjänsten adresser dessa behov. Enheter kan tilldelas automatiskt till nya IoT-hubbar baserat på reprovisioning principen som är konfigurerad på enhetens registreringspost.

## <a name="device-state-data"></a>Tillstånd för enhetsdata

Enhetens tillståndsdata består av den [enhetstvillingen](../iot-hub/iot-hub-devguide-device-twins.md) och funktioner för enheter. Dessa data lagras i instansen Device Provisioning-tjänsten och IoT-hubben som en enhet har tilldelats.

![Etablering med Device Provisioning-tjänst](./media/concepts-device-reprovisioning/dps-provisioning.png)

När en enhet har etablerats med en instans av Device Provisioning-tjänsten, utförs följande steg:

1. Enheten skickar en etableringsbegäran till en instans av Device Provisioning-tjänsten. Tjänstinstansen autentiserar enhetens identitet baserat på en registreringspost och skapar den initiala konfigurationen av enhetsdata för tillstånd. Tjänstinstansen tilldelar enheten till en IoT-hubb baserat på registreringskonfigurationen för och returnerar den IoT hub-tilldelningen till enheten.

2. Etablering tjänstinstansen får en kopia av alla tillstånd för första enhetsdata till tilldelad IoT hub. Enheten ansluter till den tilldelade IoT hub och börjar åtgärder.

Framöver kommer enhetens tillståndsdata i IoT-hubb kan uppdateras genom att [åtgärder](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) och [backend-åtgärder](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). Starttillstånd tillståndsinformationen som lagras i Device Provisioning-tjänsten databasinstans förblir orörd. Den här vara orört enhetsdata tillstånd är den inledande konfigurationen.

![Etablering med Device Provisioning-tjänst](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Beroende på scenario, som en enhet som flyttar mellan IoT-hubbar, kan det också vara nödvändigt att migrera enhetstillstånd uppdateras på föregående IoT-hubb över till den nya IoT-hubben. Den här migreringen stöds av reprovisioning principer i Device Provisioning-tjänsten.

## <a name="reprovisioning-policies"></a>Reprovisioning principer

Beroende på scenario skickar en enhet vanligtvis en begäran till en allokering tjänstinstans vid omstart. Det stöder även en metod för att manuellt starta etablering på begäran. Reprovisioning principen på en registreringspost bestämmer hur den device provisioning-tjänstinstansen hanterar dessa etablering begäranden. Principen anger också om enhetens tillståndsdata ska migreras under reprovisioning. Samma principer är tillgängliga för enskilda registreringar och registreringsgrupper:

* **Etablera och migrera data**: den här principen är standard för nya poster för registrering. Den här principen vidtar åtgärder när enheter som är associerade med registreringsposten skicka en ny begäran (1). Beroende på posten registreringskonfigurationen får enheten omtilldelas till en annan IoT-hubb. Om enheten ändras IoT-hubbar, tas enhetsregistrering med den första IoT-hubben bort. Den uppdaterade enhetsinformationen tillstånd från den första IoT-hubben kommer att migreras över till den nya IoT-hubben (2). Under migreringen, enhetens status kommer att rapporteras som **tilldela**.

    ![Etablering med Device Provisioning-tjänst](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Etablera och återställa till ursprungliga config**: den här principen vidtar åtgärder när enheter som är associerade med registreringsposten skickar in en ny begäran om etablering (1). Beroende på posten registreringskonfigurationen får enheten omtilldelas till en annan IoT-hubb. Om enheten ändras IoT-hubbar, tas enhetsregistrering med den första IoT-hubben bort. Inledande konfigurationsdata som etablering tjänstinstansen tas emot när enheten har etablerats har angetts för den nya IoT-hubben (2). Under migreringen, enhetens status kommer att rapporteras som **tilldela**.

    Den här principen används ofta för en fabriksåterställning utan att ändra IoT-hubbar.

    ![Etablering med Device Provisioning-tjänst](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Aldrig etablera**: enheten aldrig omtilldelas till en annan hubb. Den här principen har angetts för att hantera bakåtkompatibilitet kompatibilitet.

### <a name="managing-backwards-compatibility"></a>Hantera bakåtkompatibilitet kompatibilitet

Enhetstilldelningar till IoT-hubbar hade ett Fäst beteende innan September 2018. När en enhet som har gått tillbaka igenom etableringen, skulle det endast tilldelas till samma IoT-hubb.

Etableringstjänsten innehåller bakåtkompatibilitet kompatibilitet för lösningar som har tagit ett beroende på det här beteendet. Det här beteendet är för närvarande bibehålls för enheter baserat på följande villkor:

1. Enheterna som ansluter med en API-version innan du tillgängligheten för reprovisioning stöd som finns i Device Provisioning-tjänsten. Referera till tabellen API.

2. Registreringspost för enheterna som har inte en reprovisioning princip som angetts dem.

Denna kompatibilitet ser till att som tidigare har distribuerat enheter upplevelse på samma sätt som finns under den inledande testningen. För att bevara det tidigare beteendet, inte spara en princip för reprovisioning till dessa registreringar. Om en reprovisioning princip har angetts, har reprovisioning principen företräde framför beteende. Genom att låta reprovisioning principen prioriteras gör uppdatera kunderna enhetsbeteende utan att behöva återställa avbildningen av enheten.

Följande flödesschema hjälper till att visa när beteendet finns:

![bakåtkompatibilitet flödesschema för kompatibilitet](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

I följande tabell visar de API-versionerna före tillgängligheten för inbyggda reprovisioning stöd i Device Provisioning-tjänsten:

| REST-API | C SDK | Python SDK |  SDK för Node | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 och tidigare](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 och tidigare](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 och tidigare](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 eller tidigare](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 eller tidigare](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 eller tidigare](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Förmodligen kommer att ändra dessa värden och länkar. Detta är endast ett platshållaren försök att fastställa där versionerna som kan fastställas med en kund och vad de förväntade versionerna kommer att bli.

## <a name="next-steps"></a>Nästa steg

* [Hur du etablera om enheter](how-to-reprovision.md)
