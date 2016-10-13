<properties
 pageTitle="Förkonfigurerade lösningar i Azure IoT | Microsoft Azure"
 description="En beskrivning av de förkonfigurerade lösningarna i Azure IoT och deras arkitektur med länkar till ytterligare resurser."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/09/2016"
 ms.author="dobett"/>


# Vad är förkonfigurerade lösningar i Azure IoT Suite?

De förkonfigurerade lösningarna i Azure IoT Suite är implementeringar av vanliga IoT-lösningsmönster som du kan distribuera till Azure via din prenumeration. Du kan använda de förkonfigurerade lösningarna:

- Som en startpunkt för dina egna IoT-lösningar.
- Mer information om vanliga mönster i utformningen och utvecklingen av IoT-lösningar.

Varje förkonfigurerad lösning är en fullständig implementering från slutpunkt till slutpunkt som använder simulerade enheter för att generera telemetri.

Förutom att distribuera och köra lösningarna i Azure kan du hämta den fullständiga källkoden och sedan anpassa och utöka lösningen efter dina specifika IoT-krav.

> [AZURE.NOTE] Om du vill distribuera någon av de förkonfigurerade lösningarna besöker du [Microsoft Azure IoT Suite][lnk-azureiotsuite]. Artikeln [Komma igång med förkonfigurerade IoT-lösningar][lnk-getstarted-preconfigured] innehåller mer information om hur du distribuerar och kör en av lösningarna.

Följande tabell visar hur lösningarna mappar till specifika IoT-funktioner:

| Lösning | Datainhämtning | Enhetsidentitet | Kommando och kontroll | Regler och åtgärder | Förutsägelseanalys |
|------------------------|-----|-----|-----|-----|-----|
| [Fjärrövervakning][lnk-getstarted-preconfigured] | Ja | Ja | Ja | Ja | -   |
| [Förebyggande underhåll][lnk-predictive-maintenance] | Ja | Ja | Ja | Ja | Ja |

- *Datainhämtning*: Inhämtning av data till molnet i hög skala.
- *Enhetsidentitet*: Hantera unika identiteter för varje ansluten enhet.
- *Kommando och kontroll*: Skicka meddelanden till en enhet från molnet för att utlösa en åtgärd på enheten.
- *Regler och åtgärder*: Lösningens serverdel använder regler för att agera på specifika data från enheten till molnet.
- *Förutsägelseanalys*: Lösningens serverdel analyserar data från enheten till molnet för att förutsäga när specifika åtgärder ska äga rum. Lösningen kan till exempel analysera telemetri från en flygplansmotor för att fastställa när motorunderhåll krävs.

## Översikt över den förkonfigurerade lösningen för fjärrövervakning

Vi har valt att diskutera den förkonfigurerade lösningen för fjärrövervakning i den här artikeln eftersom den illustrerar många vanliga designelement som finns i de andra lösningarna.

Följande diagram illustrerar de viktigaste elementen i fjärrövervakningslösningen. Avsnitten nedan innehåller mer information om dessa element.

![Arkitekturen i den förkonfigurerade lösningen för fjärrövervakning][img-remote-monitoring-arch]

## Enheter

När du distribuerar den förkonfigurerade fjärrövervakningslösningen har fyra simulerade enheter som simulerar en kylningsenhet redan konfigurerats i lösningen. Dessa simulerade enheter har en inbyggd temperatur- och fuktighetsmodell som genererar telemetri. De simulerade enheterna är med för att illustrera dataflödet från slutpunkt till slutpunkt via lösningen och för att ge tillgång till en bra telemetrikälla och ett mål för kommandon om du är en backend-utvecklare som använder lösningen som utgångspunkt för en anpassad implementering.

Första gången en enhet ansluter till IoT Hub i den förkonfigurerade fjärrövervakningslösningen innehåller meddelandet med enhetsinformation som skickas till IoT-hubben en lista med alla kommandon som enheten kan svara på. I den förkonfigurerade fjärrövervakningslösningen används följande kommandon: 

- *Pinga enhet*: Enheten svarar på det här kommandot med en bekräftelse. Detta är användbart för att kontrollera att enheten fortfarande är aktiv och lyssnar.
- *Starta telemetri*: Instruerar enheten att börja skicka telemetri.
- *Stoppa telemetri*: Instruerar enheten att sluta skicka telemetri.
- *Ändra temperaturvärden*: Styr vilka simulerade telemetrivärden för temperatur som enheten skickar. Detta är användbart för att testa backend-logiken.
- *Diagnostiktelemetri*: Styr om enheten ska skicka den externa temperaturen som telemetri.
- *Ändra enhetstillstånd*: Anger metadataegenskapen för enhetens tillstånd som rapporteras av enheten. Detta är användbart för att testa backend-logiken.

Du kan lägga till fler simulerad enheter i lösningen som skickar samma telemetri och som svarar på samma kommandon. 

## IoT Hub

I den här förkonfigurerade lösningen motsvarar IoT Hub-instansen *molngatewayen* i en typisk [IoT-lösningsarkitektur][lnk-what-is-azure-iot].

En IoT-hubb tar emot telemetri från enheterna på en enda slutpunkt. En IoT-hubb har också enhetsspecifika slutpunkter där varje enhet kan ta emot de kommandon som skickas till den.

IoT-hubben ser till att den mottagna telemetrin är tillgänglig via den telemetriavlästa slutpunkten på tjänstsidan.

## Azure Stream Analytics

Den förkonfigurerade lösningen använder tre [Azure Stream Analytics][lnk-asa]-jobb (ASA) för att filtrera telemetriströmmen från enheterna:


- Jobbet *Enhetsinformation* – matar ut data till en händelsehubb som dirigerar enhetsregistreringsspecifika meddelanden, som skickas när en enhet först ansluter till eller svarar på ett kommando av typen **Ändra enhetsstatus**, till lösningens enhetsregister (en DocumentDB-databas). 
- Jobbet *Telemetri* – skickar alla råtelemetridata till Azure Blob Storage för kall lagring och beräknar telemetriaggregeringar som visas på instrumentpanelen för lösningen.
- Jobbet *Regler* – filtrerar telemetriströmmen och hämtar värden som överskrider tröskelvärden för regler och matar sedan ut dessa data till en händelsehubb. När en regel utlöses visar instrumentpanelen på lösningsportalen den här händelsen som en ny rad i larmhistoriktabellen och utlöser en åtgärd baserat på inställningarna som definierats i vyerna Regler och Åtgärder på lösningsportalen.

I den här förkonfigurerade lösningen är ASA-jobben en del av **IoT-lösningens serverdel** i en typisk [IoT-lösningsarkitektur][lnk-what-is-azure-iot].

## Händelseprocessor

I den här förkonfigurerade lösningen är händelseprocessorn en del av **IoT-lösningens serverdel** i en typisk [IoT arkitektur][lnk-what-is-azure-iot].

ASA-jobben **DeviceInfo** och **Regler** skickar sina utdata till Event Hubs för leverans till andra backend-tjänster. Lösningen använder en [EventPocessorHost][lnk-event-processor]-instans som körs i ett [Webbjobb][lnk-web-job] för att läsa meddelandena från dessa Event Hubs. **EventProcessorHost** använder data från **Enhetsinformation** för att uppdatera enhetsdata i DocumentDB-databasen och använder data från **Regler** för att anropa logikappen och uppdatera aviseringsvyn på lösningsportalen.

## Enhetsidentitetsregistret och DocumentDB

Varje IoT-hubb innehåller ett [enhetsidentitetsregister][lnk-identity-registry] som lagrar enhetsnycklar. IoT Hub använder den här informationen för att autentisera enheter – en enhet måste vara registrerad och ha en giltig nyckel innan den kan ansluta till hubben.

Den här lösningen lagrar ytterligare information om enheter, t.ex. deras tillstånd, de kommandon som de stöder och andra metadata. Lösningen använder en DocumentDB-databas för att lagra dessa lösningsspecifika enhetsdata och lösningsportalen hämtar data från den här DocumentDB-databasen för visning och redigering.

Lösningen måste också spara informationen i enhetsidentitetsregistret som synkroniseras med innehållet i DocumentDB-databasen. **EventProcessorHost** använder data från Stream Analytics-jobbet **Enhetsinformation** för att hantera synkroniseringen.

## Lösningsportal

![Instrumentpanel för lösningen][img-dashboard]

Lösningsportalen är ett webbaserat gränssnitt som distribueras till molnet som en del av den förkonfigurerade lösningen. Här kan du:

- Visa telemetri och larmhistorik på en instrumentpanel.
- Etablera nya enheter.
- Hantera och övervaka enheter.
- Skicka kommandon till specifika enheter.
- Hantera regler och åtgärder.

I den här förkonfigurerade lösningen är lösningsportalen en del av **IoT-lösningens serverdel** och en del av **bearbetnings- och affärsanslutningarna** i en typisk [IoT-lösningsarkitektur][lnk-what-is-azure-iot].

## Nästa steg

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


<!--HONumber=Oct16_HO1-->


