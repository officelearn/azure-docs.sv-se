---
title: Utvecklarguide för Azure IoT Hub | Microsoft-dokument
description: Utvecklarhandboken för Azure IoT Hub innehåller diskussioner om slutpunkter, säkerhet, identitetsregistret, enhetshantering, direkta metoder, enhetstvillingar, filöverföringar, jobb, IoT Hub-frågespråk och meddelanden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 4e380fdcd72ab21f00272536b9f08145dcb09bc1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728820"
---
# <a name="azure-iot-hub-developer-guide"></a>Utvecklarhandledning för Azure IoT Hub

Azure IoT Hub är en fullständigt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals enheter och en lösningsbakdel.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub ger dig:

* Säker kommunikation med hjälp av säkerhetsautentiseringsuppgifter per enhet och åtkomstkontroll.

* Flera kommunikationsalternativ för enheter till molnet och från molnet till enhet.

* Frågebar lagring av tillståndsinformation per enhet och metadata.

* Enkel enhetsanslutning med enhetsbibliotek för de mest populära språken och plattformarna.

Den här utvecklarhandboken för IoT Hub innehåller följande artiklar:

* [Kommunikationsvägledning för enhet till moln](iot-hub-devguide-d2c-guidance.md) hjälper dig att välja mellan meddelanden mellan enheter till molnet, enhetstvillingens rapporterade egenskaper och filöverföring.

* [Kommunikationsvägledning från molnet till enheten](iot-hub-devguide-c2d-guidance.md) hjälper dig att välja mellan direkta metoder, enhetstvillingens önskade egenskaper och meddelanden från molnet till enheten.

* [Enhet-till-moln- och moln-till-enhet-meddelanden med IoT Hub](iot-hub-devguide-messaging.md) beskriver meddelandefunktionerna (enhet till moln och moln-till-enhet) som IoT Hub exponerar.

  * [Skicka meddelanden från enhet till moln till IoT Hub](iot-hub-devguide-messages-d2c.md).

  * Läs meddelanden från [enheten till molnet från den inbyggda slutpunkten](iot-hub-devguide-messages-read-builtin.md).

  * [Använd anpassade slutpunkter och routningsregler för meddelanden från enhet till moln](iot-hub-devguide-messages-read-custom.md).

  * [Skicka meddelanden från molnet till enheten från IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Skapa och läs IoT Hub-meddelanden](iot-hub-devguide-messages-construct.md).

* [Ladda upp filer från en enhet](iot-hub-devguide-file-upload.md) beskriver hur du kan ladda upp filer från en enhet. Artikeln innehåller också information om ämnen som meddelanden som uppladdningsprocessen kan skicka.

* [Hantera enhetsidentiteter i IoT Hub](iot-hub-devguide-identity-registry.md)beskriver vilken information varje IoT-hubbs identitetsregister lagrar. I artikeln beskrivs också hur du kan komma åt och ändra den.

* [Kontrollåtkomst till IoT Hub](iot-hub-devguide-security.md) beskriver den säkerhetsmodell som används för att bevilja åtkomst till IoT Hub-funktioner för både enheter och molnkomponenter. Artikeln innehåller information om hur du använder token och X.509-certifikat och information om de behörigheter du kan bevilja.

* [Använd enhetstvillingar för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md) beskriver *enhetstvillingkonceptet.* I artikeln beskrivs också de funktionsenheter som exponeras, till exempel synkronisera en enhet med dess enhetstvilling. Artikeln innehåller information om data som lagras i en enhetstvilling.

* [När du anropar en direkt metod på en enhet](iot-hub-devguide-direct-methods.md) beskrivs livscykeln för en direkt metod. I artikeln beskrivs hur du anropar metoder på en enhet från backend-appen och hanterar direktmetoden på enheten.

* [Schemalägg jobb på flera enheter](iot-hub-devguide-jobs.md) beskriver hur du kan schemalägga jobb på flera enheter. Artikeln beskriver hur du skickar jobb som utför uppgifter som utför en direkt metod, uppdatera en enhet med hjälp av en enhet twin. Den beskriver också hur du frågar status för ett jobb.

* [Referens - välj ett kommunikationsprotokoll](iot-hub-devguide-protocols.md) beskriver de kommunikationsprotokoll som IoT Hub stöder för enhetskommunikation och listar de portar som ska vara öppna.

* [Referens - IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körnings- och hanteringsåtgärder. I artikeln beskrivs också hur du kan skapa ytterligare slutpunkter i IoT-hubben och hur du använder en fältgateway för att aktivera anslutning till IoT Hub-slutpunkter i scenarier som inte är standard.

* [Referens - IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md) beskriver det IoT Hub-frågespråk som gör att du kan hämta information från hubben om dina enhetstvillingar och jobb.

* [Referens - kvoter och begränsning](iot-hub-devguide-quotas-throttling.md) sammanfattar de kvoter som anges i IoT Hub-tjänsten och begränsningen som uppstår när du överskrider en kvot.

* [Referens - prissättning](iot-hub-devguide-pricing.md) ger allmän information om olika SKU:er och priser för IoT Hub och information om hur de olika IoT Hub-funktionerna mäts som meddelanden av IoT Hub.

* [Referens - Enhets- och tjänst-SDK:er](iot-hub-devguide-sdks.md) listar Azure IoT-SDK:er för utveckling av enhets- och tjänstappar som interagerar med din IoT-hubb. Artikeln innehåller länkar till api-dokumentation online.

* [Referens - IoT Hub MQTT-stöd](iot-hub-mqtt-support.md) ger detaljerad information om hur IoT Hub stöder MQTT-protokollet. Artikeln beskriver stödet för MQTT-protokollet inbyggt i Azure IoT SDK:er och innehåller information om hur du använder MQTT-protokollet direkt.

* [Ordlista](iot-hub-devguide-glossary.md) en lista över vanliga IoT Hub-relaterade termer.