---
title: "Utvecklarhandbok för Azure IoT-hubb | Microsoft Docs"
description: "Utvecklarhandbok för Azure IoT Hub innehåller diskussioner slutpunkter, säkerhet, identitetsregistret, hantering av enheter, direkt metoder, enheten twins, filöverföringar, jobb, frågespråket i IoT-hubb och meddelanden."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 37f9da7dcc8dd527fe0bfbf2fbcc40a3ba0e8a1c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-iot-hub-developer-guide"></a>Utvecklarhandbok för Azure IoT-hubb

Azure IoT Hub är en helt hanterad tjänst som hjälper till att aktivera tillförlitlig och säker dubbelriktad kommunikation mellan miljoner enheter och en lösning tillbaka avslutas.

Azure IoT-hubb ger dig:

* Säker kommunikation med hjälp av autentiseringsuppgifterna per enhet och åtkomstkontroll.
* Flera enhet till moln och moln till enhet storskaliga kommunikationsalternativ.
* Frågbar lagring av information om tillstånd per enhet och metadata.
* Enkelt enhetsanslutning med enhetsbibliotek för de flesta populära språk och plattformar.

Den här IoT-hubb Utvecklarhandbok innehåller följande artiklar:

* [Enhet till moln kommunikation vägledning] [ lnk-d2c-guidance] kan du välja mellan meddelanden från enhet till moln, enheten dubbla rapporterade egenskaper och ladda upp filen.
* [Moln till enhet kommunikation vägledning] [ lnk-c2d-guidance] kan du välja mellan direkt metoder, enheten dubbla egenskaper och moln till enhet meddelanden.
* [Enhet till moln och moln till enhet meddelanden med IoT-hubben] [ devguide-messaging] beskriver meddelandefunktioner (enhet till moln och moln till enhet) som visar IoT-hubb.
  * [Skicka meddelanden från enhet till moln till IoT-hubb][devguide-messages-d2c].
  * [Läsa meddelanden från enhet till moln från inbyggd slutpunkt][devguide-builtin].
  * [Använd anpassade slutpunkter och routningsregler för meddelanden från enhet till moln][devguide-custom].
  * [Skicka meddelanden moln till enhet från IoT-hubb][devguide-messages-c2d].
  * [Skapa och läsa IoT-hubb][devguide-format].
* [Överföra filer från en enhet] [ devguide-upload] beskriver hur du kan ladda upp filer från en enhet. Artikeln innehåller även information om ämnen, till exempel meddelanden överföringen kan skicka.
* [Hantera identiteter för enheten i IoT-hubb] [ devguide-identities] beskriver vilken information varje IoT-hubb identitetslagringar för registret. Artikeln beskriver också hur du kan komma åt och ändra den.
* [Kontrollera åtkomsten till IoT-hubb] [ devguide-security] beskriver säkerhetsmodell som används för att ge åtkomst till IoT-hubb funktioner för både enheter och molnet komponenter. Artikeln innehåller information om hur du använder token och X.509-certifikat och information om de behörigheter som du kan ge.
* [Använd twins för enheten för att synkronisera tillstånd och konfigurationer] [ devguide-device-twins] beskriver den *enheten dubbla* begrepp. Artikeln också beskrivs funktioner enheten twins exponera, till exempel synkronisera en enhet med dubbla sin enhet. Artikeln innehåller information om data som lagras i en delad enhet.
* [Anropa en metod som är direkt på en enhet] [ devguide-directmethods] beskriver livscykeln för en direkt metod. Artikeln beskriver hur du anropa metoder i en enhet från backend-app och hantera den direkta metoden på enheten.
* [Schemalägga jobb på flera enheter] [ devguide-jobs] beskriver hur du schemalägger jobb på flera enheter. Artikeln beskriver hur du skicka jobb som utför uppgifter som att köra en direkt metod, uppdaterar en enhet med en delad enhet. Det beskriver också hur du fråga status för ett jobb.
* [Referera - Välj ett kommunikationsprotokoll] [ devguide-protocol] beskriver kommunikationsprotokoll att IoT-hubben har stöd för kommunikation mellan och visas de portar som ska vara öppen.
* [Referens - IoT-hubbslutpunkter] [ devguide-endpoints] beskriver de olika slutpunkter som varje IoT-hubb visar för körning och hanteringsåtgärder. Artikeln beskriver också hur du kan skapa ytterligare slutpunkter i din IoT-hubb och hur du använder en gateway för fältet Aktivera anslutning till din IoT-hubb slutpunkter i scenarier som inte är standard.
* [Referens - frågespråket för IoT-hubb för enheten twins, jobb och meddelanderoutning] [ devguide-query] beskriver den IoT-hubb frågespråk som gör att du kan hämta information från din hubb om enheten twins och jobb.
* [Referens - kvoter och begränsning] [ devguide-quotas] sammanfattar kvoterna som anges i IoT-hubb-tjänsten och den begränsning som uppstår när en kvot.
* [Referera - priser] [ devguide-pricing] ger allmän information om olika SKU: er och priser för IoT-hubb och information om hur olika funktioner i IoT-hubben är avgiftsbelagda som meddelanden i IoT Hub.
* [Referens - enheten och tjänsten SDK] [ devguide-sdks] listar Azure IoT SDK: er för utveckling av enheten och tjänsten för appar som samverkar med din IoT-hubb. Artikeln innehåller länkar till online API-dokumentationen.
* [Referens - stöd för IoT-hubb MQTT] [ devguide-mqtt] innehåller detaljerad information om hur IoT-hubb stöder MQTT-protokollet. Artikeln beskriver stöd för protokollet MQTT inbyggd Azure IoT-SDK: er och innehåller information om hur du använder protokollet MQTT direkt.
* [Ordlista] [ devguide-glossary] en lista över vanliga IoT-hubb-relaterade termer.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
