---
title: Enhetshantering med Azure IoT Hub | Microsoft Docs
description: "Översikt över enhetshantering i Azure IoT Hub: livscykel för företagsenhet och enhetshanteringsmönster som omstart, fabriksåterställning, uppdatering av inbyggd programvara, konfiguration, enhetstvillingar, frågor, jobb."
services: iot-hub
documentationcenter: 
author: bzurcher
manager: timlt
editor: 
ms.assetid: a367e715-55f6-4593-bd68-7863cbf0eb81
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: 458b316ba76ff7cace56eb21eaf105233e508741
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="overview-of-device-management-with-iot-hub"></a>Översikt över enhetshantering med IoT Hub
## <a name="introduction"></a>Introduktion
Azure IoT Hub innehåller funktionerna och en modell för utökningsbarhet som gör det möjligt enhets- och backend-utvecklare att bygga robusta lösningar för enhetshantering. Enheter är allt från begränsade sensorer och enskilda mikrostyrenheter för ett särskilt ändamål till kraftfulla gateways som dirigerar kommunikationen för grupper av enheter.  Dessutom varierar användningsfall och krav för IoT-operatörer avsevärt mellan olika branscher.  Trots variationen ger enhetshantering med IoT Hub funktioner, mönster och kodbibliotek för att serva en mängd olika enheter och slutanvändare.

En viktig del av att skapa en lyckad IoT-lösning för företaget är att tillhandahålla en strategi för hur operatörer kontinuerligt hanterar sin samling med enheter. IoT-operatörer behöver enkla och tillförlitliga verktyg och program som låter dem fokusera på mer strategiska aspekter av sina arbetsuppgifter. Den här artikeln innehåller:

* En kort översikt över metoden för enhetshantering i Azure IoT Hub.
* En beskrivning av gemensamma principer för hantering av enheten.
* En beskrivning av enhetens livscykel.
* En översikt över vanliga mönster för hantering av enheten.

## <a name="device-management-principles"></a>Principer för enhetshantering
IoT medför en unik uppsättning utmaningar för enhetshantering, och en lösning i företagsklass måste ta hänsyn till följande principer:

![Bild över principer för enhetshantering][img-dm_principles]

* **Skala och automatisering**: IoT-lösningar kräver enkla verktyg som kan automatisera rutiner och som låter en mycket liten personalstyrka hantera miljontals enheter. I det dagliga arbetet förväntas operatörer fjärrhantera enhetsåtgärder gruppvis och endast få aviseringar när problem som kräver direkta åtgärder uppstår.
* **Insyn och kompatibilitet**: Enhetsmiljön är ovanligt mångfaldig. Hanteringsverktygen måste skräddarsys för att hantera en mängd olika enhetsklasser, plattformar och protokoll. Operatörer måste ha stöd för många typer av enheter, från de mest begränsade inbäddade enkelprocesschippen till kraftfulla och helt funktionella datorer.
* **Kontextmedvetenhet**: IoT-miljöer är dynamiska och föränderliga. Tjänstens tillförlitlighet är avgörande. Enhetshanteringsåtgärder måste ha följande faktorer i åtanke så att driftavbrotten inte påverkar viktiga företagsrutiner eller skapar farliga förutsättningar:
    * SLA-underhållsfönster
    * Nätverks- och krafttillstånd
    * Användningsförutsättningar
    * Enhetens geografiska plats
* **Stöd för många roller**: stöd för IoT-åtgärdernas unika arbetsflöden och processer är avgörande. Driftpersonalen måste arbeta harmoniskt med de givna begränsningarna för interna IT-avdelningar.  De måste också hitta hållbara sätt för att exponera information om enhetsåtgärder i realtid för chefer och andra företagsledarroller. 

## <a name="device-lifecycle"></a>Enhetens livscykel
Det finns en uppsättning av allmänna enhetshanteringsfaser som är gemensamma för alla företags IoT-projekt. Livscykeln för enheter i Azure IoT består av fem faser:

![De fem Azure IoT-livscyklerna är: planera, etablera, konfigurera, övervaka, dra tillbaka][img-device_lifecycle]

I var och en av dessa fem faser finns det flera enhetsoperatorskrav som måste uppfyllas för att tillhandahålla en komplett lösning:

* **Planera**: Operatörer kan skapa ett schema med enhetens metadata. Schemat kan användas till att snabbt och korrekt skicka frågor till eller fokusera på en enhetsgrupp för massåtgärder. Du kan använda enhetstvillingen för att lagra dessa enhetsmetadata i form av taggar och egenskaper.
  
    *Mer information*: [Kom igång med enhetstvillingar][lnk-twins-getstarted], [Förstå enhetstvillingar][lnk-twins-devguide], [How to use device twin properties ][lnk-twin-properties] (Använda egenskaper för enhetstvillingar).
* **Etablera**: Etablera säkert nya enheter i IoT Hub där operatörerna kan se enhetens funktioner direkt.  Använd IoT Hub-identitetsregistret för att skapa flexibla enhetsidentiteter och autentiseringsuppgifter och utför åtgärden gruppvis med hjälp av ett jobb. Skapa enheter om du vill rapportera deras kapacitet och villkor via enhetens egenskaper i enhetstvillingen.
  
    *Mer information*: [Hantera enhetsidentiteter][lnk-identity-registry], [Bulk management of device identities][lnk-bulk-identity] (Masshantering av enhetsidentiteter), [How to use device twin properties][lnk-twin-properties] (Använda egenskaper för enhetstvillingar), [Azure IoT Hub Device Provisioning-tjänst][lnk-dps].
* **Konfigurera**: underlätta massinläsning av konfigurationsändringar och uppdaterad enhetsprogramvara utan att förlora funktion eller säkerhet. Utför dessa åtgärder för enhetshantering gruppvis genom att använda önskade egenskaper eller med direkta metoder och sändningsjobb.
  
    *Mer information*:  [Använda direkta metoder][lnk-c2d-methods], [Invoke a direct method on a device][lnk-methods-devguide] (Anropa en direkt metod på en enhet), [How to use device twin properties][lnk-twin-properties] (Använda egenskaper för enhetstvillingar), [Schemalägga och sända jobb][lnk-jobs], [Schemalägga jobb på flera enheter][lnk-jobs-devguide] (Schemalägga jobb på flera enheter).
* **Övervaka**: Övervaka enhetssamlingens allmänna funktion samt statusen för pågående åtgärder så att operatörerna blir medvetna om problem som kräver åtgärder.  Använd enhetstvillingen för att tillåta att enheterna rapporterar driftsförhållanden och status för uppdateringsåtgärder i realtid. Skapa kraftfulla instrumentpanelsrapporter som lyfter fram de mest omedelbara problemen genom att använda enhetstvillingsfrågor.
  
    *Mer information*: [How to use device twin properties][lnk-twin-properties] (Använda egenskaper för enhetstvillingar), [IoT Hub query language for device twins, jobs and message routing][lnk-query-language] (IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderuttning).
* **Inaktivera**: ersätta eller inaktivera enheter efter ett fel, uppgraderingscykel eller efter enhetens funktionstid har löpt ut.  Använd enhetstvillingen för att underhålla enhetsinformation om den fysiska enheten håller på att ersättas eller arkiveras om den har återkallats. Använd IoT Hub-identitetsregistret för att säkert återkalla enhetsidentiteter och autentiseringsuppgifter.
  
    *Mer information*: [How to use device twin properties][lnk-twin-properties] (Använda egenskaper för enhetstvillingar), [Hantera enhetsidentiteter][lnk-identity-registry].

## <a name="device-management-patterns"></a>Enhetshanteringsmönster
IoT Hub använder följande uppsättning enhetshanteringsmönster.  [Självstudiekursen om enhetshantering][lnk-get-started] beskriver i mer detalj hur du utökar dessa mönster så att de passar just ditt scenario och hur du designar nya mönster utifrån dessa basmallar.

* **Starta om** – Serverdelsappen informerar enheten via en direkt metod om att en omstart har påbörjats.  Enheten använder de rapporterade egenskaperna för att uppdatera enhetens status för omstart.
  
    ![Bild över omstartsmönster för enhetshantering][img-reboot_pattern]
* **Fabriksåterställning** – Serverdelsappen informerar enheten via en direkt metod om att en fabriksåterställning har påbörjats.  Enheten använder de rapporterade egenskaperna för att uppdatera enhetens status för fabriksåterställning.
  
    ![Bilder över fabriksåterställningsmönster för enhetshantering][img-facreset_pattern]
* **Konfiguration** – Serverdelsappen använder önskade egenskaper för att konfigurera programvaran som körs på enheten.  Enheten använder de rapporterade egenskaperna för att uppdatera enhetens konfigurationsstatus.
  
    ![Bild över konfigurationsmönster för enhetshantering][img-config_pattern]
* **Uppdatering av enhetens programvara** –Serverdelsappen informerar enheten via en direkt metod om att en uppdatering av enhetens programvara har påbörjats.  Enheten initierar en process i flera steg för att ladda ned inbyggd avbildning av programvara, tillämpa den och därefter återkoppla till IoT Hub-tjänsten.  Enheten använder de rapporterade egenskaperna under hela flerstegsprocessen för att uppdatera enhetens förlopp och status.
  
    ![Bild över mönster för uppdatering av inbyggd programvara för enhetshantering][img-fwupdate_pattern]
* **Rapportering av förlopp och status** –Lösningsserverdelen kör frågor på enhetstvillingpar över en uppsättning enheter för att ge information om pågående åtgärders status och förlopp.
  
    ![Bild över mönster för uppdatering av rapporteringsprocess och status][img-report_progress_pattern]

## <a name="next-steps"></a>Nästa steg
Du kan använda de funktioner, mönster och kodbibliotek som ingår i IoT Hub-enhetshanteringen för att skapa IoT-program som uppfyller företagets IoT-operatörskrav i varje enhets livscykelfas.

Om du vill lära dig mer om enhetshanteringsfunktionerna i IoT Hub går du självstudiekursen [Komma igång med enhetshantering][lnk-get-started].

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-node-node-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
