<properties
 pageTitle="Översikt över IoT Hub-enhetshantering | Microsoft Azure"
 description="Den här artikeln innehåller en översikt över enhetshantering i Azure IoT Hub: livscykel för företagsenhet, omstart, fabriksåterställning, uppdatering av inbyggd programvara, konfiguration, enhetstvillingar, frågor, jobb"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/03/2016"
 ms.author="bzurcher"/>


# <a name="overview-of-azure-iot-hub-device-management-(preview)"></a>Översikt över enhetshantering i Azure IoT Hub (förhandsversion)

## <a name="introduction"></a>Introduktion

Azure IoT Hub innehåller funktionerna och en modell för utökningsbarhet som gör det möjligt enhets- och backend-utvecklare att bygga robusta lösningar för IoT-enhetshantering. IoT-enheter sträcker sig från begränsade sensorer och enskilda mikrostyrenheter till kraftfulla gateways som dirigerar kommunikation för grupper av enheter.  Dessutom varierar användningsfall och krav för IoT-operatörer avsevärt mellan olika branscher.  Trots variationen ger Azure IoT Hub-enhetshantering funktioner, mönster och kodbibliotek för att serva en mängd olika enheter och slutanvändare.

En viktig del av att skapa en lyckad IoT-lösning för företaget är att tillhandahålla en strategi för hur operatörer kontinuerligt hanterar sin samling med enheter. IoT-operatörer behöver enkla och tillförlitliga verktyg och program som låter dem fokusera på mer strategiska aspekter av sina arbetsuppgifter. Den här artikeln innehåller:

- En kort översikt över metoden för enhetshantering i Azure IoT Hub.
- En beskrivning av gemensamma principer för hantering av enheten.
- En beskrivning av enhetens livscykel.
- En översikt över vanliga mönster för hantering av enheten.

## <a name="iot-device-management-principles"></a>Principer för IoT-enhetshantering

IoT medför en unik uppsättning utmaningar för enhetshantering, och en lösning i företagsklass måste ta hänsyn till följande principer:

![Översikt över principer för Azure IoT Hub-enhetshantering][img-dm_principles]

- **Skala och automatisering**: IoT-lösningar kräver enkla verktyg som kan automatisera rutiner och som låter en mycket liten personalstyrka hantera miljontals enheter. I det dagliga arbetet förväntas operatörer fjärrhantera enhetsåtgärder gruppvis och endast få aviseringar när problem som kräver direkta åtgärder uppstår.

- **Insyn och kompatibilitet**: IoT-enhetsmiljön är ovanligt mångfaldig. Hanteringsverktygen måste skräddarsys för att hantera en mängd olika enhetsklasser, plattformar och protokoll. Operatörer måste ha stöd för många typer av enheter, från de mest begränsade inbäddade enkelprocesschippen till kraftfulla och helt funktionella datorer.

- **Kontextmedvetenhet**: IoT-miljöer är dynamiska och föränderliga. Tjänstens tillförlitlighet är avgörande. Enhetshanteringsåtgärder ta SLA-underhållsfönster, nätverks- och krafttillstånd, användningsförutsättningar och enhetens plats i åtanke så att driftavbrotten inte påverkar viktiga företagsrutiner eller skapar farliga förutsättningar.

- **Stöd för många roller**: stöd för IoT-åtgärdernas unika arbetsflöden och processer är avgörande. Driftpersonalen måste arbeta harmoniskt med de givna begränsningarna för interna IT-avdelningar.  De måste också hitta hållbara sätt för att exponera information om enhetsåtgärder i realtid för chefer och andra företagsledarroller. 

## <a name="iot-device-lifecycle"></a>Livscykel för IoT-enhet

Det finns en uppsättning av allmänna enhetshanteringsfaser som är gemensamma för alla företags IoT-projekt. Det finns fem faser i livscykeln för IoT-enheten i Azure IoT:

![De fem Azure IoT-livscyklerna är: planera, etablera, konfigurera, övervaka, dra tillbaka][img-device_lifecycle]

I var och en av dessa fem faser finns det flera enhetsoperatorskrav som måste uppfyllas för att tillhandahålla en komplett lösning:

- **Planera**: Operatörer kan skapa ett schema med enhetens metadata. Schemat kan användas till att snabbt och korrekt skicka frågor till eller fokusera på en enhetsgrupp för massåtgärder. Du kan använda enhetstvillingen för att lagra dessa enhetsmetadata i form av taggar och egenskaper.

    *Mer läsning*: [Kom igång med enhetstvillingar][lnk-twins-getstarted], [Förstå enhetstvillingar][lnk-twins-devguide], [Så här använder du tvillingegenskaper][lnk-twin-properties]

- **Etablera**: Etablera säkert nya enheter i IoT Hub där operatörerna kan se enhetens funktioner direkt.  Använd IoT Hub-enhetsregistret för att skapa flexibla enhetsidentiteter och autentiseringsuppgifter och utför åtgärden gruppvis med hjälp av ett jobb. Skapa enheter om du vill rapportera deras kapacitet och villkor via enhetens egenskaper i enhetstvillingen.

    *Mer läsning*: [Hantera enhetsidentiteter][lnk-identity-registry], [Bulk management of device identities] (Masshantering av enhetsidentiteter)[lnk-bulk-identity], [Så här använder du tvillingegenskaper][lnk-twin-properties]

- **Konfigurera**: underlätta massinläsning av konfigurationsändringar och uppdaterad enhetsprogramvara utan att förlora funktion eller säkerhet. Utför dessa åtgärder för enhetshantering gruppvis genom att använda önskade egenskaper eller med direkta metoder och sändningsjobb.

    *Mer läsning*:  [Använd direktmetoder][lnk-c2d-methods], [Invoke a direct method on a device] (Anropa en direktmetod på en enhet)[lnk-methods-devguide], [Så här använder du tvillingegenskaper][lnk-twin-properties], [Schemalägg och sänd jobb][lnk-jobs], [Schemalägg jobb på flera enheter][lnk-jobs-devguide]

- **Övervaka**: Övervaka enhetssamlingens allmänna funktion samt statusen för pågående åtgärder så att operatörerna blir medvetna om problem som kräver åtgärder.  Använd enhetstvillingen för att tillåta att enheterna rapporterar driftsförhållanden och status för uppdateringsåtgärder i realtid. Skapa kraftfulla instrumentpanelsrapporter som lyfter fram de mest omedelbara problemen genom att använda enhetstvillingsfrågor.

    *Mer läsning*: [Så här använder du tvillingegenskaper][lnk-twin-properties], [Query language for twins and jobs] (Frågespråk för tvillingar och jobb)[lnk-query-language]

- **Inaktivera**: ersätta eller inaktivera enheter efter ett fel, uppgraderingscykel eller efter enhetens funktionstid har löpt ut.  Använd enhetstvillingen för att underhålla enhetsinformation om den fysiska enheten håller på att ersättas eller arkiveras om den har återkallats. Använd IoT Hub-enhetsregistret för att säkert återkalla enhetsidentiteter och autentiseringsuppgifter.

    *Mer läsning*: [Så här använder du tvillingegenskaper][lnk-twin-properties], [Hantera enhetsidentiteter][lnk-identity-registry]

## <a name="iot-hub-device-management-patterns"></a>Enhetshanteringsmönster för IoT Hub

IoT Hub använder följande uppsättning enhetshanteringsmönster.  I [självstudien om enhetshantering][lnk-get-started] får du se mer detaljerat hur du utökar dessa mönster så att de passar just ditt scenario och hur du designar nya mönster utifrån dessa kärnmallar.

- **Starta om** – Serverdelprogrammet informerar enheten via en direkt metod om att en omstart har påbörjats.  Enheten använder enhetstvillingens egenskaper för att uppdatera enhetens omstartsstatus.

    ![Omstartmönster för enhetshantering i Azure IoT Hub, bild][img-reboot_pattern]

- **Fabriksåterställning** – Serverdelprogrammet informerar enheten via en direkt metod om att en fabriksåterställning har påbörjats.  Enheten använder enhetstvillingens egenskaper för att uppdatera statusen för fabriksåterställningen.

    ![Fabriksåterställningsmönster för enhetshantering i Azure IoT Hub, bild][img-facreset_pattern]

- **Konfiguration** - Serverdelprogrammet använder enhetstvillingens egenskaper för att konfigurera programvaran som körs på enheten.  Enheten använder enhetstvillingens rapporterade egenskaper för att uppdatera enhetens konfigurationsstatus.

    ![Konfigurationsmönster för enhetshantering i Azure IoT Hub, bild][img-config_pattern]

- **Uppdatering av enhetens programvara** –Serverdelprogrammet informerar enheten via en direkt metod om att en uppdatering av enhetens programvara har påbörjats.  Enheten initierar en process i flera steg för att ladda ned inbyggd programvara, tillämpa den och därefter återkoppla till IoT Hub-tjänsten.  Enheten använder enhetstvillingens egenskaper under hela flerstegsprocessen för att uppdatera enhetens förlopp och status.

    ![Mönster för uppdatering av inbyggd programvara för enhetshantering i Azure IoT Hub, bild][img-fwupdate_pattern]

- **Rapportering av förlopp och status** –Serverdelprogrammet kör frågor på enhetstvillingpar över en uppsättning enheter för att ge information om pågående åtgärders status och förlopp.

    ![Mönster för uppdatering av rapporteringsprocess och status i Azure IoT Hub, bild][img-report_progress_pattern]

## <a name="next-steps"></a>Nästa steg

Du kan använda de funktioner, mönster och kodbibliotek som ingår i Azure IoT Hub-enhetshanteringen för att skapa IoT-program som uppfyller företagets IoT-operatorkrav i varje enhets livscykelfas.

Om du vill lära dig mer om enhetshanteringsfunktionerna i Azure IoT Hub går du självstudiekursen [Komma igång med enhetshantering i Azure IoT Hub][lnk-get-started].

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md


<!--HONumber=Oct16_HO3-->


