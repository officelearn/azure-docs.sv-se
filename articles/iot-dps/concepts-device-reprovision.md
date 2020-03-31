---
title: Etablerande tjänst för Azure IoT Hub-enhet – enhetsbegrepp
description: Beskriver begrepp för ometablering av enheter för AZURE IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2bf369b784cddf307abc59d2b8766fc8a87e0985
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975354"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT Hub Device ometablering begrepp

Under livscykeln för en IoT-lösning är det vanligt att flytta enheter mellan IoT-hubbar. Orsakerna till det här steget kan vara följande scenarier:

* **Geolatency :** När en enhet flyttas mellan platser förbättras nätverksfördröjningen genom att enheten migreras till en närmare IoT-hubb.

* **Multi-arrende**: En enhet kan användas inom samma IoT-lösning och omtilldelas till en ny kund eller kundwebbplats. Den här nya kunden kan servas med en annan IoT-hubb.

* **Ändring av lösning:** En enhet kan flyttas till en ny eller uppdaterad IoT-lösning. Den här omtilldelningen kan kräva att enheten kommunicerar med en ny IoT-hubb som är ansluten till andra backend-komponenter.

* **Karantän**: Liknar en lösningsändring. En enhet som inte fungerar, komprometteras eller är inaktuell kan tilldelas en IoT-hubb som bara kan uppdatera och komma tillbaka i enlighet. När enheten fungerar som den ska migreras den sedan tillbaka till huvudnavet.

Ometablera stöd inom enhetsetableringstjänsten åtgärdar dessa behov. Enheter kan automatiskt tilldelas nya IoT-hubbar baserat på principen om återetablering som är konfigurerad på enhetens registreringspost.

## <a name="device-state-data"></a>Data om enhetstillstånd

Enhetstillståndsdata består av [enhetens tvilling-](../iot-hub/iot-hub-devguide-device-twins.md) och enhetsfunktioner. Dessa data lagras i instansen Enhetsetableringstjänst och IoT-hubben som en enhet har tilldelats.

![Etablering med enhetsetableringstjänsten](./media/concepts-device-reprovisioning/dps-provisioning.png)

När en enhet först etableras med en enhetsetableringstjänstinstans görs följande:

1. Enheten skickar en etableringsbegäran till en enhetsetableringstjänstinstans. Tjänstinstansen autentiserar enhetsidentiteten baserat på en registreringspost och skapar den första konfigurationen av enhetstillståndsdata. Tjänstinstansen tilldelar enheten till en IoT-hubb baserat på registreringskonfigurationen och returnerar den IoT-hubbtilldelningen till enheten.

2. Etableringstjänstinstansen ger en kopia av alla ursprungliga enhetstillståndsdata till den tilldelade IoT-hubben. Enheten ansluter till den tilldelade IoT-hubben och startar åtgärder.

Med tiden kan enhetstillståndsdata på IoT-hubben uppdateras av [enhetsåtgärder](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) och [backend-åtgärder](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). Den ursprungliga enhetstillståndsinformationen som lagras i instansen Enhetsetableringstjänster förblir orörd. Den här orörda enhetstillståndsdata är den första konfigurationen.

![Etablering med enhetsetableringstjänsten](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Beroende på scenariot, när en enhet flyttas mellan IoT-hubbar, kan det också vara nödvändigt att migrera enhetstillstånd som uppdaterats på den tidigare IoT-hubben över till den nya IoT-hubben. Den här migreringen stöds av principer för ometablering i enhetsetableringstjänsten.

## <a name="reprovisioning-policies"></a>Ometablera principer

Beroende på scenariot skickar en enhet vanligtvis en begäran till en etableringstjänstinstans vid omstart. Den stöder också en metod för att manuellt utlösa etablering på begäran. Principen om återetablering för en registreringspost avgör hur enhetsetableringstjänstinstansen hanterar dessa etableringsbegäranden. Principen avgör också om enhetstillståndsdata ska migreras under återetablering. Samma principer är tillgängliga för enskilda registreringar och registreringsgrupper:

* **Etablera om och migrera data:** Den här principen är standard för nya registreringsposter. Den här principen vidtar åtgärder när enheter som är associerade med registreringsposten skickar en ny begäran (1). Beroende på konfigurationen av registreringsposten kan enheten tilldelas en annan IoT-hubb. Om enheten ändrar IoT-hubbar tas enhetsregistreringen med den första IoT-hubben bort. Den uppdaterade enhetstillståndsinformationen från den första IoT-hubben migreras till den nya IoT-hubben (2). Under migreringen rapporteras enhetens status som **Tilldela**.

    ![Etablering med enhetsetableringstjänsten](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Etablera om och återställ till den första konfigurationen**: Den här principen vidtar åtgärder när enheter som är associerade med registreringsposten skickar en ny etableringsbegäran (1). Beroende på konfigurationen av registreringsposten kan enheten tilldelas en annan IoT-hubb. Om enheten ändrar IoT-hubbar tas enhetsregistreringen med den första IoT-hubben bort. De inledande konfigurationsdata som etableringstjänstinstansen togs emot när enheten etablerades tillhandahålls till den nya IoT-hubben (2). Under migreringen rapporteras enhetens status som **Tilldela**.

    Den här principen används ofta för en fabriksåterställning utan att ändra IoT-hubbar.

    ![Etablering med enhetsetableringstjänsten](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Etablera**aldrig om : Enheten tilldelas aldrig om till ett annat nav. Den här principen finns för hantering av bakåtkompatibilitet.

### <a name="managing-backwards-compatibility"></a>Hantera bakåtkompatibilitet

Före september 2018 hade enhetstilldelningar till IoT-hubbar ett klibbigt beteende. När en enhet gick tillbaka genom etableringsprocessen, skulle den bara tilldelas tillbaka till samma IoT-hubb.

För lösningar som har fått ett beroende av det här beteendet innehåller etableringstjänsten bakåtkompatibilitet. Detta underhålls för närvarande för enheter enligt följande kriterier:

1. Enheterna ansluter till en API-version innan det finns tillgång till inbyggt ometableringsstöd i enhetsetableringstjänsten. Se API-tabellen nedan.

2. Registreringsposten för enheterna har ingen princip för ometablering inställd på dem.

Den här kompatibiliteten säkerställer att tidigare distribuerade enheter upplever samma beteende som finns under den första testningen. Spara inte en ometableringsprincip för dessa registreringar för att bevara det tidigare beteendet. Om en ometableringsprincip har angetts har återetableringsprincipen företräde framför beteendet. Genom att tillåta att principen om etablering har företräde kan kunder uppdatera enhetens beteende utan att behöva reimage enheten.

Följande flödesschema hjälper till att visa när det finns ett problem:

![flödesschema för bakåtkompatibilitet](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

I följande tabell visas API-versionerna innan det finns ett internt ometableringsstöd i enhetsetableringstjänsten:

| REST API | C SDK | Python SDK |  SDK för Node | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 och tidigare](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 och tidigare](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 och tidigare](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 eller tidigare](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 eller tidigare](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 eller tidigare](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Dessa värden och länkar kommer sannolikt att ändras. Detta är bara ett platshållarförsök för att avgöra var versionerna kan bestämmas av en kund och vilka de förväntade versionerna kommer att vara.

## <a name="next-steps"></a>Nästa steg

* [Så här återetableras enheter](how-to-reprovision.md)
