---
title: Utvecklarguide för Azure IoT Hub | Microsoft Docs
description: Azure IoT Hub developer guide innehåller diskussioner av slutpunkter, säkerhet, identitetsregistret, hantering av enheter, direkta metoder, enhetstvillingar, filöverföringar, jobb, IoT Hub-frågespråk, och meddelanden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 0246b19c0155fe2707ac780c7acb61be424d2caf
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57008939"
---
# <a name="azure-iot-hub-developer-guide"></a>Azure IoT Hub developer guide

Azure IoT Hub är en helt hanterad tjänst som hjälper dig att aktivera pålitlig och säker dubbelriktad kommunikation mellan miljontals enheter och tillhandahåller serverdelen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub ger dig:

* Säker kommunikation med hjälp av autentiseringsuppgifter för varje enhet och åtkomstkontroll.

* Flera alternativ för kommunikation från enhet till moln och från moln till enhet hyperskala.

* Frågningsbart lagring av information om varje enhet tillstånd och metadata.

* Enkelt enhetsanslutning med enhetsbibliotek för de mest populära språk och plattformar.

Den här utvecklarhandboken för IoT Hub innehåller följande artiklar:

* [Enhet-till-molnet kommunikation vägledning](iot-hub-devguide-d2c-guidance.md) val mellan meddelanden från enheten till molnet, enhetstvillingens rapporterade egenskaper och ladda upp filen.

* [Vägledning för moln till enhet kommunikation](iot-hub-devguide-c2d-guidance.md) val mellan direkta metoder, enhetstvillingens önskade egenskaper och meddelanden från molnet till enheten.

* [Enhet-till-moln och moln till enhet-meddelanden med IoT Hub](iot-hub-devguide-messaging.md) beskriver meddelandefunktioner (enhet till molnet och moln till enhet) som exponerar IoT Hub.

  * [Skicka meddelanden från enhet till moln till IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Läsa meddelanden från enheten till molnet från den inbyggda slutpunkten](iot-hub-devguide-messages-read-builtin.md).

  * [Använda anpassade slutpunkter och routningsregler för meddelanden från enheten till molnet](iot-hub-devguide-messages-read-custom.md).

  * [Skicka meddelanden från moln till enhet från IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Skapa och läsa IoT Hub-meddelanden](iot-hub-devguide-messages-construct.md).

* [Ladda upp filer från en enhet](iot-hub-devguide-file-upload.md) beskriver hur du kan ladda upp filer från en enhet. Artikeln innehåller även information om ämnen som meddelanden uppladdningen kan skicka.

* [Hantera enhetsidentiteter i IoT Hub](iot-hub-devguide-identity-registry.md)beskriver vilken information varje IoT-hubbens identitetslagringar för registret. Artikeln beskriver också hur du kan komma åt och ändra den.

* [Kontrollera åtkomsten till IoT Hub](iot-hub-devguide-security.md) beskriver säkerhetsmodellen används för att ge åtkomst till IoT Hub funktioner för både enheter och komponenter i molnet. Artikeln innehåller information om hur du använder token och X.509-certifikat, samt information om de behörigheter som du kan ge.

* [Använda enhetstvillingar för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md) beskriver den *enhetstvillingen* begrepp. Artikeln också beskrivs enhetstvillingar funktioner att innehålla, till exempel synkronisera en enhet med dess enhetstvilling. Artikeln innehåller information om de data som lagras i enhetstvillingen.

* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md) beskriver livscykeln för en direkt metod. Artikeln beskriver hur du anropa metoder på en enhet från din serverdelsapp och hanterar den direkta metoden på enheten.

* [Schemalägg jobb på flera enheter](iot-hub-devguide-jobs.md) beskriver hur du kan schemalägga jobb på flera enheter. Artikeln beskriver hur du skickar in jobb som utför uppgifter som att köra en direkt metod, uppdatera en enhet med hjälp av en enhetstvilling. Det beskriver också hur du frågar efter status för ett jobb.

* [Referera till – Välj ett kommunikationsprotokoll](iot-hub-devguide-protocols.md) beskriver kommunikationsprotokoll att IoT Hub har stöd för kommunikation mellan enheten och visas de portar som ska vara öppet.

* [Referens – IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körning och hanteringsåtgärder. Artikeln beskriver också hur du kan skapa ytterligare slutpunkter i IoT hub och hur du använder en fält-gateway för anslutningen till din IoT Hub-slutpunkter i scenarier som inte är standard.

* [Referens – IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md) beskriver den IoT Hub-frågespråk som gör att du kan hämta information från din hubb om enhetstvillingar och jobb.

* [Referens - kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md) sammanfattar kvoter i IoT Hub-tjänsten och den begränsning som uppstår när du överskrider en kvot.

* [Referera - priser](iot-hub-devguide-pricing.md) innehåller allmän information om olika SKU: er och priser för IoT Hub och information om hur de olika funktionerna för IoT Hub mäts som meddelanden som IoT Hub.

* [Referens - enheten och tjänsten SDK: er](iot-hub-devguide-sdks.md) visar en lista över Azure IoT SDK: er för att utveckla appar för enheten och tjänsten som interagerar med din IoT-hubb. Artikeln innehåller länkar till online API-dokumentationen.

* [Referens – IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller detaljerad information om hur IoT Hub stöder MQTT-protokollet. Artikeln beskriver stödet för av MQTT-protokollet som är inbyggda i Azure IoT SDK: er och innehåller information om hur du använder MQTT-protokollet direkt.

* [Ordlista](iot-hub-devguide-glossary.md) en lista över vanliga IoT Hub-relaterade termer.