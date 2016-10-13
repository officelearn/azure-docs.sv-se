<properties
 pageTitle="Översikt över enhetshantering | Microsoft Azure"
 description="Översikt över enhetshantering i Azure IoT Hub"
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
 ms.date="09/16/2016"
 ms.author="bzurcher"/>




# Översikt över enhetshantering i Azure IoT Hub (förhandsversion)

## Enhetshanteringsmetod, Azure IoT

Enhetshanteringen Azure IoT Hub innehåller funktioner och utökningsmodeller för enheter och servrar. Detta drar nytta av IoT-enhetshanteringen för olika enheter och IoT-protokoll.  Enheter i IoT varierar från mycket begränsade sensorer och mikrostyrenheter med en funktion till kraftfulla gatewayer som aktiverar andra enheter och protokoll.  IoT-lösningar varierar även avsevärt i lodräta domäner och program med unika användningsfall för operatörer i varje domän.  IoT-lösningar kan använda enhetshanteringsfunktioner, mönster och kodbibliotek från IoT Hub, vilket gör det möjligt att hantera en mångfald av enheter och användare.  

## Introduktion

En viktig del av att skapa en lyckad IoT-lösning är att tillhandahålla en strategi för hur operatörer kontinuerligt hanterar sin enhetsflotta. IoT-operatörer behöver verktyg och program som är både enkla och tillförlitliga och som låter dem fokusera på mer strategiska aspekter av sina arbetsuppgifter. Azure IoT Hub ger förutsättningarna för att skapa IoT-program som underlättar de viktigaste enhetshanteringsmönstren.

Enheter anses vara hanterade av IoT Hub när de kör ett enkelt program (en s.k. enhetshanteringsagent) som ansluter enheten på ett säkert sätt till molnet. Agentkoden låter operatören på programsidan bekräfta enhetsstatusen och utföra hanteringsåtgärder, till exempel att tillämpa nätverkskonfigurationsändringar eller distribuera programvara.

## Principer för IoT-enhetshantering

IoT medför en unik uppsättning utmaningar och en lösning måste ta hänsyn till följande principer för IoT-enhetshantering:

![][img-dm_principles]

- **Skala och automatisering**: IoT kräver enkla verktyg som kan automatisera rutiner och som låter en mycket liten personalstyrka hantera miljontals enheter. I det dagliga arbetet förväntas operatörer fjärrhantera enhetsåtgärder gruppvis och endast få aviseringar när problem som kräver direkta åtgärder uppstår.

- **Insyn och kompatibilitet**: IoT-enhetsmiljön är ovanligt mångfaldig. Hanteringsverktygen måste skräddarsys för att hantera en mängd olika enhetsklasser, plattformar och protokoll. Operatörer måste ha stöd för alla enheter, från de mest begränsade inbäddade enkelprocesschippen till kraftfulla och helt funktionella datorer.

- **Kontextmedvetenhet**: IoT-miljöer är dynamiska och föränderliga. Tjänstens tillförlitlighet är avgörande. Enhetshanteringsåtgärder ta SLA-underhållsfönster, nätverks- och krafttillstånd, användningsförutsättningar och enhetens plats i åtanke så att driftavbrotten inte påverkar viktiga företagsrutiner eller skapar farliga förutsättningar.

- **Stöd för många roller**: stöd för IoT-åtgärdernas unika arbetsflöden och processer är avgörande. Operatörerna måste dessutom kunna samverka med de interna IT-avdelningarnas begränsningar och lyfta viktig information om enhetsåtgärder till chefer och ledning.

## Livscykel för IoT-enhet 

Även om IoT-projekt skiljer sig från varandra avsevärt finns det vissa gemensamma mönster för enhetshantering. Dessa mönster identifieras i Azure IoT genom IoT-enhetens livscykel som består av fem olika faser:

![][img-device_lifecycle]

1. **Planera**: Operatörer kan skapa ett schema med enhetens egenskaper. Schemat kan användas till att snabbt och korrekt skicka frågor till eller fokusera på en enhetsgrupp för massåtgärder.

    *Relaterade komponenter*: [komma igång med enhetstvillingar][lnk-twins-getstarted], [Så här hanterar du tvillingegenskaper][lnk-twin-properties]

2. **Etablera**: nya enheter autentiseras säkert i IoT Hub där operatörerna kan se enhetens funktioner och aktuella tillstånd direkt.

    *Relaterade komponenter*: [komma igång med IoT Hub][lnk-hub-getstarted], [Så här hanterar du tvillingegenskaper][lnk-twin-properties]

3. **Konfigurera**: underlätta massinläsning av konfigurationsändringar och uppdaterad enhetsprogramvara utan att förlora funktion eller säkerhet.

    *Relaterade byggblock*: [så här använder du tvillingegenskaper][lnk-twin-properties], [C2D-metoder][lnk-c2d-methods], [schema-/sändningsuppgifter][lnk-jobs]

4. **Övervaka**: enhetsflottas allmänna funktion samt statusen för pågående distributioner av uppdateringar kan övervakas så att operatörerna blir medvetna om problem som kräver åtgärder.

    *Relaterade komponenter*: [Så här använder du tvillingegenskaper][lnk-twin-properties]

5. **Inaktivera**: ersätta eller inaktivera enheter efter ett fel, uppgraderingscykel eller efter enhetens funktionstid har löpt ut.

    *Relaterade komponenter*:
    
## Enhetshanteringsmönster för IoT Hub

IoT Hub använder följande uppsättning (ursprungliga) enhetshanteringsmönster.  [Guiden][lnk-get-started] visar hur du kan utöka dessa mönster så att de passar just dina scenarier och utforma nya mönster för andra scenarier som bygger på dessa kärnmönster.

1. **Starta om** -Serverdelprogrammet informerar enheten via en D2C-metod att en omstart har påbörjats.  Enheten använder enhetstvillingens egenskaper för att uppdatera enhetens omstartsstatus. 

    ![][img-reboot_pattern]

2. **Fabriksåterställning** -Serverdelprogrammet informerar enheten via en D2C-metod att en fabriksåterställning har påbörjats.  Enheten använder enhetstvillingens egenskaper för att uppdatera statusen för fabriksåterställningen.

    ![][img-facreset_pattern]

3. **Konfiguration** - Serverdelprogrammet använder enhetstvillingens egenskaper för att konfigurera programvaran som körs på enheten.  Enheten använder enhetstvillingens rapporterade egenskaper för att uppdatera enhetens konfigurationsstatus. 

    ![][img-config_pattern]

4. **Uppdatering av enhetens programvara** -Serverdelprogrammet informerar enheten via en D2C-metod att en uppdatering av enhetens programvara har påbörjats.  Enheten initierar en process i flera steg för att ladda ned inbyggd programvara, tillämpa den och därefter återkoppla till IoT Hub-tjänsten.  Enheten använder enhetstvillingens egenskaper under hela flerstegsprocessen för att uppdatera enhetens förlopp och status. 

    ![][img-fwupdate_pattern]

5. **Rapportering av förlopp och status** -Serverdelprogrammet kör frågor på enhetstvillingpar över en uppsättning enheter för att ge information om pågående åtgärders status och förlopp.

    ![][img-report_progress_pattern]

## Nästa steg

Med byggblock från Azure IoT Hub kan utvecklare skapa IoT-program som uppfyller de unika IoT-operatörskraven för varje steg i enhetens livscykel.

Om du vill lära dig mer om enhetshanteringsfunktionerna i Azure IoT Hub går du självstudiekursen [Komma igång med enhetshantering i Azure IoT Hub][lnk-get-started].

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md


<!--HONumber=Oct16_HO1-->


