---
title: "Förkonfigurerade lösningar i Azure IoT | Microsoft Docs"
description: "En beskrivning av de förkonfigurerade lösningarna i Azure IoT och deras arkitektur med länkar till ytterligare resurser."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: d66dece63d2ba944c8f3828ba68c6202485d47e0
ms.contentlocale: sv-se
ms.lasthandoff: 07/25/2017

---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Vad är förkonfigurerade lösningar i Azure IoT Suite?

De förkonfigurerade lösningarna i Azure IoT Suite är implementeringar av vanliga IoT-lösningsmönster som du kan distribuera till Azure via din prenumeration. Du kan använda de förkonfigurerade lösningarna:

* Som en startpunkt för dina egna IoT-lösningar.
* Mer information om vanliga mönster i utformningen och utvecklingen av IoT-lösningar.

Varje förkonfigurerad lösning är en fullständig implementering från slutpunkt till slutpunkt som använder simulerade enheter för att generera telemetri.

Du kan ladda ned den fullständiga källkoden för att anpassa och utöka lösningen så att den uppfyller dina specifika IoT-krav.

> [!NOTE]
> Om du vill distribuera någon av de förkonfigurerade lösningarna besöker du [Microsoft Azure IoT Suite][lnk-azureiotsuite]. Artikeln [Komma igång med förkonfigurerade IoT-lösningar][lnk-getstarted-preconfigured] innehåller mer information om hur du distribuerar och kör lösningarna.

Följande tabell visar hur lösningarna mappar till specifika IoT-funktioner:

| Lösning | Datainhämtning | Enhetsidentitet | Enhetshantering | Kommando och kontroll | Regler och åtgärder | Förutsägelseanalys |
| --- | --- | --- | --- | --- | --- | --- |
| [Fjärrövervakning][lnk-getstarted-preconfigured] |Ja |Ja |Ja |Ja |Ja |- |
| [Förebyggande underhåll][lnk-predictive-maintenance] |Ja |Ja |- |Ja |Ja |Ja |
| [Ansluten fabrik][lnk-getstarted-factory] |Ja |Ja |Ja |Ja |Ja |- |

* *Datainhämtning*: Inhämtning av data till molnet i hög skala.
* *Enhetsidentitet*: Hantera unika enhetsidentiteter och kontrollera enhetsåtkomst till lösningen.
* *Enhetshantering*: Hantera enhetsmetadata och utför åtgärder som omstarter av enheten och uppgraderingar av den inbyggda programvaran.
* *Kommando och kontroll*: Skicka meddelanden till en enhet från molnet för att utlösa en åtgärd på enheten.
* *Regler och åtgärder*: Lösningens serverdel använder regler för att agera på specifika data från enheten till molnet.
* *Förutsägelseanalys*: Lösningens backend-server analyserar data från enheten till molnet för att förutsäga när specifika åtgärder ska äga rum. Lösningen kan till exempel analysera telemetri från en flygplansmotor för att fastställa när motorunderhåll krävs.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Översikt över den förkonfigurerade lösningen för fjärrövervakning

Vi har valt att diskutera den förkonfigurerade lösningen för fjärrövervakning i den här artikeln eftersom den illustrerar många vanliga designelement som finns i de andra lösningarna.

Följande diagram illustrerar de viktigaste elementen i fjärrövervakningslösningen. Följande avsnitt innehåller mer information om dessa element.

![Arkitekturen i den förkonfigurerade lösningen för fjärrövervakning][img-remote-monitoring-arch]

## <a name="devices"></a>Enheter

När du distribuerar den förkonfigurerade fjärrövervakningslösningen har fyra simulerade enheter som simulerar en kylningsenhet redan konfigurerats i lösningen. Dessa simulerade enheter har en inbyggd temperatur- och fuktighetsmodell som genererar telemetri. Dessa simulerade enheter ingår:

- Visa flödet av data slutpunkt till slutpunkt genom lösningen.
- Ange en lämplig källa för telemetri.
- Ange ett mål för metoderna eller kommandona om du är en backend-utvecklare som använder lösningen som en startpunkt för en anpassad implementering.

De simulerade enheterna i lösningen kan svara på följande kommunikation från moln till enhet:

- *Metoder ([direkta metoder][lnk-direct-methods])*: En dubbelriktad kommunikationsmetod där en ansluten enhet förväntas svara omedelbart.
- *Kommandon (meddelande från moln till enhet)*: En enkelriktad kommunikationsmetod där en enhet hämtar kommandot från en beständig kö.

En jämförelse av de olika metoderna finns i [Cloud-to-device communications guidance][lnk-c2d-guidance] (Vägledning för kommunikation från moln till enhet).

Första gången en enhet ansluter till IoT Hub i den förkonfigurerade lösningen skickar den ett meddelande med enhetsinformation till hubben. Det här meddelandet räknar upp de metoder som enheten kan svara på. Simulerade enheter stöder följande metoder i den förkonfigurerade fjärrövervakningslösningen:

* *Initiate Firmware Update* (Påbörja uppdatering av inbyggd programvara): den här metoden påbörjar en asynkron uppgift på enheten för att utföra en uppdatering av den inbyggda programvaran. De asynkrona åtgärderna använder rapporterade egenskaper för att leverera statusuppdateringar till lösningens instrumentpanel.
* *Starta om*: den här metoden gör att den simulerade enheten startas om.
* *FactoryReset*: den här metoden utlöser en fabriksåterställning på den simulerade enheten.

Första gången en enhet ansluter till IoT Hub i den förkonfigurerade lösningen skickar den ett meddelande med enhetsinformation till hubben. Det här meddelandet räknar upp de kommandon som enheten kan svara på. Simulerade enheter stöder följande kommandon i den förkonfigurerade fjärrövervakningslösningen:

* *Pinga enhet*: Enheten svarar på det här kommandot med en bekräftelse. Det här kommandot är användbart för att kontrollera att enheten fortfarande är aktiv och lyssnar.
* *Starta telemetri*: Instruerar enheten att börja skicka telemetri.
* *Stoppa telemetri*: Instruerar enheten att sluta skicka telemetri.
* *Ändra temperaturvärden*: Styr vilka simulerade telemetrivärden för temperatur som enheten skickar. Det här kommandot är användbart för att testa backend-logiken.
* *Diagnostiktelemetri*: Styr om enheten ska skicka den externa temperaturen som telemetri.
* *Ändra enhetstillstånd*: Anger metadataegenskapen för enhetens tillstånd som rapporteras av enheten. Det här kommandot är användbart för att testa backend-logiken.

Du kan lägga till fler simulerade enheter i lösningen som skickar samma telemetri och som svarar på samma metoder och kommandon.

Utöver att svara på kommandon och metoder använder lösningen [enhetstvillingar][lnk-device-twin]. Enheterna använder enhetstvillingar för att rapportera egenskapsvärden till lösningens backend-server. Lösningens instrumentpanel använder enhetstvillingar för att ange nya önskade egenskapsvärden på enheter. Under uppdateringen av inbyggd programvara rapporterar till exempel den simulerade enheten uppdateringens status med hjälp av rapporterade egenskaper.

## <a name="iot-hub"></a>IoT Hub

I den här förkonfigurerade lösningen motsvarar IoT Hub-instansen *moln-gatewayen* i en typisk [IoT-lösningsarkitektur][lnk-what-is-azure-iot].

En IoT-hubb tar emot telemetri från enheterna på en enda slutpunkt. En IoT-hubb har också enhetsspecifika slutpunkter där varje enhet kan ta emot de kommandon som skickas till den.

IoT-hubben ser till att den mottagna telemetrin är tillgänglig via den telemetriavlästa slutpunkten på tjänstsidan.

Kapaciteten för enhetshantering av IoT Hub hjälper dig att hantera dina enhetsegenskaper från lösningsportalen och schemalägga jobb som utför åtgärder som:

- Starta om enheter
- Ändra enhetstillstånd
- Uppdateringar av inbyggd programvara

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Den förkonfigurerade lösningen använder tre [Azure Stream Analytics][lnk-asa]-jobb (ASA) för att filtrera telemetriströmmen från enheterna:

* *DeviceInfo-jobbet* – Matar ut data till en händelsehubb som dirigerar enhetsregistreringsspecifika meddelanden till lösningens enhetsregister. Det här enhetsregistret är en Azure Cosmos DB-databas. Meddelandena skickas första gången en enhet ansluter eller som svar på kommandot **Ändra enhetstillstånd**.
* Jobbet *Telemetri* – skickar alla råtelemetridata till Azure Blob Storage för kall lagring och beräknar telemetriaggregeringar som visas på instrumentpanelen för lösningen.
* Jobbet *Regler* – filtrerar telemetriströmmen och hämtar värden som överskrider tröskelvärden för regler och matar sedan ut dessa data till en händelsehubb. När en regel utlöses visar instrumentpanelen på lösningsportalen den här händelsen som en ny rad i larmhistoriktabellen. Reglerna kan även utlösa en åtgärd baserad på inställningarna som definierats i vyerna **Regler** och **Åtgärder** på lösningsportalen.

I den här förkonfigurerade lösningen är ASA-jobben en del av **IoT-lösningens serverdel** i en typisk [IoT-lösningsarkitektur][lnk-what-is-azure-iot].

## <a name="event-processor"></a>Händelseprocessor

I den här förkonfigurerade lösningen är händelseprocessorn en del av **IoT-lösningens serverdel** i en typisk [IoT-lösningsarkitektur][lnk-what-is-azure-iot].

ASA-jobben **DeviceInfo** och **Regler** skickar sina utdata till Event Hubs för leverans till andra backend-tjänster. Lösningen använder en [EventProcessorHost][lnk-event-processor]-instans som körs i ett [WebJob][lnk-web-job] för att läsa meddelanden från dessa händelsehubbar. **EventProcessorHost** använder:
- **DeviceInfo**-data för att uppdatera enhetsdata i Cosmos DB-databasen.
- **Regler**-data för att anropa logikappen och uppdatera aviseringarna som visas i lösningsportalen.

## <a name="device-identity-registry-device-twin-and-cosmos-db"></a>Enhetsidentitetsregistret, enhetstvillingar och Cosmos DB

Varje IoT-hubb innehåller ett [enhetsidentitetsregister][lnk-identity-registry] som lagrar enhetsnycklar. IoT Hub använder den här informationen för att autentisera enheter – en enhet måste vara registrerad och ha en giltig nyckel innan den kan ansluta till hubben.

En [enhetstvilling][lnk-device-twin] är ett JSON-dokument som hanteras av IoT Hub. En enhetstvilling för en enhet innehåller:

- Rapporterade egenskaper som skickas av enheten till hubben. Du kan se de här egenskaperna i lösningsportalen.
- Önskade egenskaper som du vill skicka till enheten. Du kan ange de här egenskaperna i lösningsportalen.
- Taggar som endast finns i enhetstvillingen och inte på enheten. Du kan använda de här taggarna för att filtrera enhetslistan i lösningsportalen.

Den här lösningen använder enhetstvillingar för att hantera enhetsmetadata. Lösningen använder även Cosmos DB-databasen för att lagra ytterligare lösningsspecifika enhetsdata, till exempel kommandon som stöds av varje enhet och kommandohistorik.

Lösningen måste också spara informationen i enhetsidentitetsregistret som synkroniseras med innehållet i Cosmos DB-databasen. **EventProcessorHost** använder data från Stream Analytics-jobbet **Enhetsinformation** för att hantera synkroniseringen.

## <a name="solution-portal"></a>Lösningsportal

![lösningsportal][img-dashboard]

Lösningsportalen är ett webbaserat gränssnitt som distribueras till molnet som en del av den förkonfigurerade lösningen. Här kan du:

* Visa telemetri och larmhistorik på en instrumentpanel.
* Etablera nya enheter.
* Hantera och övervaka enheter.
* Skicka kommandon till specifika enheter.
* Anropa metoder på specifika enheter.
* Hantera regler och åtgärder.
* Schemalägg jobb som ska köras på en eller flera enheter.

I den här förkonfigurerade lösningen är lösningsportalen en del av **IoT-lösningens serverdel** och en del av **bearbetnings- och affärsanslutningarna** i en typisk [IoT-lösningsarkitektur][lnk-what-is-azure-iot].

## <a name="next-steps"></a>Nästa steg

Mer information om IoT-lösningsarkitekturer finns i [Microsoft Azure IoT-tjänster: referensarkitektur][lnk-refarch].

Nu vet du vad en förkonfigurerad lösning är och kan komma igång genom att distribuera den förkonfigurerade lösningen för *fjärrövervakning*: [Komma igång med förkonfigurerade lösningar][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-getstarted-factory]: iot-suite-connected-factory-overview.md

