---
title: Enheten configuration Metodtips för Azure IoT Hub | Microsoft Docs
description: Lär dig mer om bästa praxis för att konfigurera IoT-enheter i stor skala
author: chrisgre
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 5eb0ba659961d809d0ae471034b03263f87e3894
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985506"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Metodtips för enhetskonfiguration i en IoT-lösning

Automatisk enhetshantering i Azure IoT Hub automatiserar många repetitiva och komplexa uppgifter för att hantera stora enheten fjärranläggning över hela deras livscykler. Den här artikeln definierar många av de bästa metoderna för olika roller som ingår i att utveckla och driva en IoT-lösning.

* **IoT maskinvara tillverkare/integrator:** tillverkare av IoT-maskinvara, integrerare att sätta samman maskinvara från olika tillverkare eller leverantörer som tillhandahåller maskinvara för en IoT-distribution som tillverkas eller integrerad av andra leverantörer. Delta i utveckling och integration av inbyggd programvara, embedded-operativsystem och inbäddade programvara.

* **IoT-lösning developer:** utvecklingen av en IoT-lösning normalt görs av en för lösningsutvecklare. Den här utvecklaren kan inte ingå i en interna team eller en systemintegrerare som specialiserar sig på den här aktiviteten. IoT-lösning utvecklare kan utveckla olika komponenter i IoT-lösning från grunden, integrera olika komponenter för standard- eller öppen källkod eller anpassa en [IoT lösningsaccelerator](/azure/iot-accelerators/).

* **IoT-lösning operatorn:** efter IoT-lösningen har distribuerats, det krävs långsiktig drift, övervakning, uppgraderingar och underhåll. Dessa uppgifter kan göras av en intern grupp som består av information teknikexperter, maskinvara åtgärder och underhåll team och domän specialister som övervakar rätt beteendet för den övergripande IoT-infrastrukturen.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Förstå automatisk enhetshantering för att konfigurera IoT-enheter i stor skala

Automatisk enhetshantering innehåller de många fördelarna med [enhetstvillingar](iot-hub-devguide-device-twins.md) och [modultvillingar](iot-hub-devguide-module-twins.md) att synkronisera önskad och rapporterade tillstånd mellan molnet och enheter. [Automatisk enhetskonfigurationer] [lnk-auto-enhet-config] uppdatera stora mängder twins automatiskt och summerize förlopp och efterlevnad. Följande anvisningar beskriver hur automatisk hantering av enheter är utvecklade och användas:

* Den **IoT maskinvara tillverkare/integrator** implementerar enhetshanteringsfunktionerna i ett inbäddat program med hjälp av [enhetstvillingar](iot-hub-devguide-device-twins.md). Dessa funktioner kan innehålla uppdateringar av inbyggd programvara, Programvaruinstallation och uppdatering och hantering av inställningar.

* Den **IoT-lösning developer** implementerar hanteringslager av åtgärder för enhetshantering med hjälp av [enhetstvillingar](iot-hub-devguide-device-twins.md) och [automatisk enhetskonfigurationer](iot-hub-auto-device-config.md). Lösningen ska inkludera definierar ett gränssnitt för operatorn för att utföra hanteringsuppgifter för enheten.

* Den **IoT-lösning operatorn** använder IoT-lösning för att utföra hanteringsuppgifter på enheten, särskilt för att gruppera enheter tillsammans, initiera konfigurationsändringar som uppdateringar av inbyggd programvara, övervaka och felsöka problem som uppstå.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT maskinvara tillverkare/integrator

Här följer bästa praxis för maskinvarutillverkare och integrerare som hanterar inbäddade programutveckling:

* **Implementera [enhetstvillingar](iot-hub-devguide-device-twins.md):** enhetstvillingar Aktivera synkronisering av önskad konfiguration från molnet och att rapportera aktuell konfiguration och enhetens egenskaper. Det bästa sättet att implementera enhetstvillingar inom embedded-program är via den [Azure IoT SDK: er](https://github.com/Azure/azure-iot-sdks). Enhetstvillingar är bäst lämpade för konfiguration eftersom de:

    * Stöd för dubbelriktad kommunikation.
    * Tillåt både ansluten och frånkopplade enhetstillstånd.
    * Följ principen om eventuell konsekvens.
    * Är helt enhetsmetadata som stöder frågor i molnet.

* **Strukturera enhetstvillingen för enhetshantering:** enhetstvillingen bör struktureras så att management enhetsegenskaper är logiskt grupperade i avsnitt. Detta gör ändringar i konfigurationen är isolerade utan att påverka andra avsnitt i läsningen. Till exempel skapa ett avsnitt i önskade egenskaper för inbyggd programvara, ett annat avsnitt för programvara, och en tredje avsnittet nätverksinställningar. 

* **Rapportera enhetsattribut som är användbara för enhetshantering:** attribut som fysisk enhet märke och modell, inbyggd programvara, operativsystem, serienummer, och andra identifierare är användbara för rapportering och som parametrar för mål ändringar i konfigurationen.

* **Definiera de huvudsakliga tillstånden för rapportering status och Förlopp:** översta tillstånd ska räknas upp så att de rapporteras till operatorn. En uppdatering av inbyggd programvara skulle till exempel rapportera status som aktuell, hämtar, tillämpar, pågår och fel. Definiera fler fält för mer information på varje tillstånd.

## <a name="iot-solution-developer"></a>IoT-lösning developer

Här följer några Metodtips för IoT-lösningsutvecklare som skapar system baserade i Azure:

* **Implementera [enhetstvillingar](iot-hub-devguide-device-twins.md):** enhetstvillingar Aktivera synkronisering av önskad konfiguration från molnet och att rapportera aktuell konfiguration och enhetens egenskaper. Det bästa sättet att implementera enhetstvillingar i lösningar för molnprogram är via den [Azure IoT SDK: er](https://github.com/Azure/azure-iot-sdks). Enhetstvillingar är bäst lämpade för konfiguration eftersom de:

    * Stöd för dubbelriktad kommunikation.
    * Tillåt både ansluten och frånkopplade enhetstillstånd. 
    * Följ principen om eventuell konsekvens.
    * Är helt enhetsmetadata som stöder frågor i molnet.

* **Ordnar enheter med hjälp av device twin taggar:** lösningen ska tillåta operatorn att definiera kvalitet ringar eller andra uppsättningar enheter baserat på olika distributionsmetoder, till exempel Kanarieöarna. Enheten organisation kan implementeras i din lösning med hjälp av device twin taggar och [frågor](iot-hub-devguide-query-language.md). Enheten organisation är nödvändigt att tillåta att konfigurationen appversioner på ett säkert sätt och korrekt.

* **Implementera [automatisk enhetskonfigurationer](iot-hub-auto-device-config.md):** automatisk enhetskonfigurationer distribuera och övervaka konfigurationsändringar till stora mängder IoT-enheter via enhetstvillingar. Automatisk enhetskonfigurationer rikta uppsättningar enhetstvillingar via den **rikta villkoret** som är en fråga på enhet twin taggar eller rapporterade egenskaper. Den **rikta innehåll** är uppsättningen med önskade egenskaper som anges i de aktuella enhetstvillingar. Rikta innehåll ska justeras med enheten twin struktur som definieras av IoT maskinvara tillverkare/dataintegreraren. 

   Den **mått** är frågor på enhetstvilling rapporterade egenskaper och även ska justeras med enheten twin struktur som definieras av IoT maskinvara tillverkare/dataintegreraren. Automatisk enhetskonfigurationer också har du fördelen med IoT Hub som utför åtgärder mot enhetstvillingar med en hastighet som aldrig kommer att överskrida den [begränsningsgränserna](iot-hub-devguide-quotas-throttling.md) för enheten twin läsningar och uppdateringar.

* **Använd den [Device Provisioning-tjänsten](../iot-dps/how-to-manage-enrollments.md):** lösningsutvecklare bör använda Device Provisioning-tjänsten ska tilldelas nya enheter device twin taggar, så att de konfigureras automatiskt av  **automatisk enhetskonfigurationer** som riktas mot enhetstvillingar som returnerar med taggen. 

## <a name="iot-solution-operator"></a>Operator för IoT-lösning

Här följer några Metodtips för IoT-lösning operatörer som använder en IoT-lösning bygger på Azure:

* **Organisera enheter för hantering:** IoT-lösning bör definiera eller tillåta skapandet av kvalitet ringar eller andra uppsättningar enheter baserat på olika distributionsmetoder, till exempel Kanarieöarna. Uppsättningar med enheter används lansera konfigurationsändringar och utföra andra åtgärder för enhetshantering i skala.

* **Utför ändringar i konfigurationen med hjälp av en fasad lansering:** en fasad lansering är en övergripande processen där en operatör distribuerar ändringar till en breddat uppsättning IoT-enheter. Målet är att göra ändringar gradvis för att minska risken för att göra många skala större ändringar.  Operatorn som ska använda lösningens gränssnitt för att skapa en [automatisk enhetskonfiguration](iot-hub-auto-device-config.md) och Sök mål villkoret ska rikta en grunduppsättning med enheter (till exempel en kontrollvärde grupp). Operatorn bör sedan Verifiera konfigurationsändring i den första uppsättningen av enheter. 

   När verifieringen är klar, uppdateras operatorn automatisk enhetskonfigurationen med ett större antal enheter. Operatorn bör också ange prioritet för att konfigurationen ska vara högre än andra konfigurationer som är riktat till dessa enheter. Distributionen kan övervakas med hjälp av mätvärden som rapporterats av automatisk enhetskonfigurationen. 

* **Utföra återställningar vid fel eller felkonfigurationer:** en automatisk enhetskonfiguration som orsakar fel eller felaktiga konfigurationer kan återställas genom att ändra den **riktar in sig på villkor** så att enheterna utan längre Uppfyll villkoret målobjekt. Se till att en annan automatisk konfiguration av lägre prioritet fortfarande är avsett för dessa enheter. Kontrollera att återställningen har utförts genom att visa mått: återställas tillbaka konfigurationen ska inte längre visa status för untargeted enheter och andra konfigurationen mått ska nu innehålla antal för de enheter som fortfarande är mål.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du implementerar enhetstvillingar i [förstå och använda enhetstvillingar i IoT Hub](iot-hub-devguide-device-twins.md).

* Gå igenom stegen för att skapa, uppdatera eller ta bort en automatisk enhetskonfiguration i [konfigurera och övervaka IoT-enheter skalenligt](iot-hub-auto-device-config.md).

* Implementera ett mönster för uppdatering av inbyggd programvara med hjälp av enhetstvillingar och automatisk enhetskonfigurationer i [självstudie: Implementera en enhet uppdateringsprocessen](tutorial-firmware-update.md). 
