---
title: Enheten configuration bästa praxis för Azure IoT Hub | Microsoft Docs
description: Lär dig mer om metodtips för att konfigurera IoT-enheter i skala
author: chrisgre
manager: briz
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 7314c5ec05bec61e5bbbc406b6a39a7c5a8f011f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036267"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Metodtips för enhetskonfiguration inom en IoT-lösning

Automatisk enhetshantering i Azure IoT Hub automatiserar många återkommande och komplicerade uppgifter för att hantera stora enheten flottor via deras livscykler i sin helhet. Den här artikeln definierar många av bästa praxis för de olika rollerna som ingår i utveckling och drift av en IoT-lösning.

* **IoT maskinvara tillverkare/integrator:** tillverkare av IoT-maskinvara, dietetisk montering maskinvara från olika tillverkare och leverantörer som tillhandahåller maskinvara för en IoT-distribution tillverkade eller integrerad med andra leverantörer. Delta i utvecklings- och integration av inbyggd programvara, embedded-operativsystem och inbäddade programvara.
* **IoT-lösningen utvecklare:** utvecklingen av en IoT-lösning bör göras av en för lösningsutvecklare. Den här utvecklare kan vara en del av en interna team eller en systemintegreraren som har specialiserat sig på den här aktiviteten. IoT-lösningen utvecklare kan utveckla olika komponenter i IoT-lösningen från början, integrera olika komponenter som standard eller öppen källkod eller anpassa en [IoT lösningsaccelerator][lnk-solution].
* **IoT-lösningen operator:** efter IoT-lösningen distribueras kräver den långsiktiga driften, övervakningen, uppgraderingar och underhåll. Dessa uppgifter kan utföras av en intern team som består av information technology specialister, maskinvara åtgärder och underhåll team och domän specialister som övervakar korrekt beteende för övergripande IoT-infrastrukturen.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Förstå automatiska enhetshantering för att konfigurera IoT-enheter i skala

Automatisk enhetshantering innehåller de många fördelarna med [enhet twins] [ lnk-device-twins] och [modulen twins] [ lnk-module-twins] att synkronisera önskad och rapporterat tillstånd mellan molnet och enheter.  [Automatisk enhetskonfigurationer] [ lnk-auto-device-config] uppdatera stora mängder twins automatiskt och summerize förlopp och kompatibilitet. Följande anvisningar beskrivs hur automatisk hantering av enheter har utvecklats och som används:

* Den **IoT maskinvara tillverkare/integrator** implementerar funktioner för enhetshantering i ett inbäddat program som använder [enhet twins][lnk-device-twins]. Dessa funktioner kan innehålla uppdateringar av inbyggd programvara, Programvaruinstallation och uppdatering och hantering av inställningar.
* Den **IoT-lösningen developer** implementerar hanteringslager av enheten hanteringsåtgärder med [enhet twins] [ lnk-device-twins] och [automatisk enhet konfigurationer][lnk-auto-device-config]. Lösningen bör innehålla definierar ett gränssnitt för operatorn för att utföra hanteringsuppgifter för enheten.
* Den **IoT-lösningen operatorn** använder IoT-lösningen för att utföra enhetens hanteringsuppgifter, särskilt för att gruppera enheter tillsammans, initiera konfigurationsändringar som uppdateringar av inbyggd programvara, övervaka och felsöka problem som uppstå.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT maskinvara tillverkare/integrator

Följande är bästa praxis för maskinvarutillverkare och dietetisk inbäddad programvara utvecklingen:

* **Implementera [enhet twins][lnk-device-twins]:** enhet twins Aktivera synkronisering önskad konfiguration från molnet och för rapportering aktuella konfigurations- och egenskaper.  Det bästa sättet att implementera enheten twins inom embedded-program är via den [Azure IoT SDK][lnk-azure-sdk].  Enheten twins som passar bäst för konfigurationen eftersom de: en. stöd för dubbelriktad kommunikation b. Tillåt för båda anslutet och frånkopplat tillstånd c. Följ principen om slutliga konsekvensen d. är helt queriable i molnet
* **Struktur enheten dubbla för enhetshantering:** enheten dubbla strukturera så att egenskaper för enhet är logiskt grupperade i avsnitt. Detta aktiverar konfigurationsändringar isoleras utan att påverka andra avsnitt i dubbla. Till exempel skapa ett avsnitt i Egenskaper för inbyggd programvara, ett annat avsnitt programmet, och ett tredje avsnitt för nätverksinställningar. 
* **Rapportera attributen som är användbara för enhetshantering:** attribut, bland annat fysisk enhet märke och modell, inbyggd programvara, operativsystem, serienummer, och andra identifierare är användbara för rapportering och som parametrar för mål konfigurationsändringar.
* **Definiera de huvudsakliga tillstånd för att rapportera status och Förlopp:** översta tillstånd ska räknas upp så att de kan rapporteras till operatorn. Till exempel rapporteras en firmware-uppdatering status som aktuella, hämta, använda, pågår och fel.  Definiera ytterligare fält för mer information om varje tillstånd.  

## <a name="iot-solution-developer"></a>IoT-lösningen utvecklare

Följande är bästa praxis för IoT-lösningsutvecklare som skapar system baserat i Azure:

* **Implementera [enhet twins][lnk-device-twins]:** enhet twins Aktivera synkronisering önskad konfiguration från molnet och för rapportering aktuella konfigurations- och egenskaper.  Det bästa sättet att implementera enheten twins i molnprogram lösningar är via den [Azure IoT SDK][lnk-azure-sdk].  Enheten twins som passar bäst för konfigurationen eftersom de: en. stöd för dubbelriktad kommunikation b. Tillåt för båda anslutet och frånkopplat tillstånd c. Följ principen om slutliga konsekvensen d. är helt queriable i molnet
* **Organisera enheter med hjälp av enheten dubbla taggar:** lösningen ska tillåta operatorn för att definiera kvalitet ringar eller andra grupper av enheter baserat på olika distributionsmetoder, till exempel Kanarieöarna. Enheten organisation kan implementeras i din lösning med hjälp av enheten dubbla taggar och [frågor][lnk-queries].  Enheten organisation är nödvändigt att tillåta att konfigurationen sammanslagning timeout på ett säkert sätt och korrekt.
* **Implementera [automatisk enhetskonfigurationer][lnk-auto-device-config]:** automatisk enhetskonfigurationer distribuera och övervaka konfigurationsändringar till stora mängder IoT-enheter via twins för enheten.  Automatisk enhetskonfigurationer ha uppsättningar med enheten twins via den **mål villkoret** som en fråga på enheten dubbla taggar eller rapporterade egenskaper. Den **rikta innehåll** är uppsättningen egenskaper som anges i de aktuella enheten twins. Rikta innehåll ska justeras med enheten dubbla struktur som definieras av IoT maskinvara tillverkare/integrator.  Den **mått** frågor om enheten dubbla rapporterade egenskaper och även ska justeras med enheten dubbla struktur som definieras av IoT maskinvara tillverkare/integrator. Automatisk enhetskonfigurationer har också fördelen med IoT-hubb som utför enheten dubbla åtgärder med en hastighet som aldrig kommer att överskrida den [throttling-begränsning] [ lnk-throttling] för enheten dubbla läsningar och uppdateringar.
* **Använd [enhet Etableringstjänsten][lnk-dps]:** utvecklare bör använda enheten Etableringstjänsten för att tilldela enheten dubbla taggar till nya enheter så att de blir automatiskt konfigurerats av **automatisk enhetskonfigurationer** som riktas mot twins med taggen. 

## <a name="iot-solution-operator"></a>IoT-lösningen operator

Följande är bästa praxis för IoT-lösningen operatörer som använder en IoT-lösningen bygger på Azure:

* **Organisera enheter för hantering:** i IoT-lösningen ska definiera eller tillåta för att skapa kvalitet ringsignaler eller andra uppsättningar av enheter baserat på olika distributionsmetoder, till exempel Kanarieöarna. Uppsättningar med enheter används lanserar ändringar i konfigurationen och utföra andra hanteringsåtgärder för med skalning enhet.
* **Utföra konfigurationsändringar med hjälp av en stegvis skala:** en stegvis skala är en övergripande process genom vilken operatör distribuerar ändringar till en breddat uppsättning IoT-enheter. Målet är att göra ändringar gradvis för att minska risken för att göra wide skala bryta ändringar.  Operatorn använda lösningens gränssnitt för att skapa en [automatisk enhetskonfiguration][lnk-auto-device-config], och målobjekt villkoret ska rikta en grunduppsättning med enheter (till exempel en Kanarieöarna grupp).  Operatorn bör sedan Verifiera konfigurationsändring under den inledande uppsättningen enheter.  När verifieringen är klar uppdateras operatorn automatisk enhetskonfigurationen om du vill inkludera en större uppsättning enheter. Operatorn bör också ange prioritet för att konfigurationen ska vara högre än andra konfigurationer som är riktat till dessa enheter.  Sammanslagning ut kan övervakas med hjälp av mätvärden som rapporterats av automatisk enhetskonfigurationen. 
* **Utföra återställningar vid fel eller felaktig konfiguration:** en automatisk enhetskonfiguration som orsakar fel eller felaktiga konfigurationer kan återställas genom att ändra den **riktad villkor** så att enheterna inte längre uppfyller villkoret målobjekt.  Se till att en annan automatisk enhetskonfiguration med lägre prioritet fortfarande är avsett för dessa enheter.  Kontrollera att återställningen lyckades genom att visa måtten: föras tillbaka konfigurationen ska inte längre visa status för untargeted enheter och andra konfigurationen mått nu innehålla för de enheter som fortfarande mål.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du implementerar enheten twins i [för att förstå och använda enheten twins i IoT-hubb][lnk-device-twins]
* Gå igenom stegen för att skapa, uppdatera eller ta bort en automatisk enhetskonfiguration i [konfigurera och övervaka IoT-enheter i skala][lnk-auto-device-config].
* Implementera ett mönster för uppdatering av inbyggd programvara med hjälp av enheten twins och automatisk enhetskonfigurationer i [Självstudier: Implementera en enhet firmware uppdateringsprocess][lnk-firmware-update].

<!-- Links -->
[lnk-device-twins]: iot-hub-devguide-device-twins.md
[lnk-module-twins]: iot-hub-devguide-module-twins.md
[lnk-auto-device-config]: iot-hub-auto-device-config.md
[lnk-firmware-update]: tutorial-firmware-update.md
[lnk-throttling]: iot-hub-devguide-quotas-throttling.md
[lnk-queries]: iot-hub-devguide-query-language.md
[lnk-dps]: ../iot-dps/how-to-manage-enrollments.md
[lnk-azure-sdk]: https://github.com/Azure/azure-iot-sdks
[lnk-solution]: https://azure.microsoft.com/features/iot-accelerators/