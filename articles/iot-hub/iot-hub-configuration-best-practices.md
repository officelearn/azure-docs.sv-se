---
title: Metodtips för enhetskonfiguration för Azure IoT Hub | Microsoft-dokument
description: Lär dig mer om metodtips för att använda automatisk enhetshantering för att minimera repetitiva och komplexa uppgifter som är involverade i hanteringen av IoT-enheter i stor skala.
author: chrisgre
ms.author: chrisgre
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a3b70af71c2ce19835ac2ef8fc8ceed79ca5fe1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889533"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Metodtips för enhetskonfiguration i en IoT-lösning

Automatisk enhetshantering i Azure IoT Hub automatiserar många repetitiva och komplexa uppgifter för att hantera stora enhetsflottor under hela deras livscykler. Den här artikeln definierar många av de bästa metoderna för de olika roller som ingår i att utveckla och driva en IoT-lösning.

* **IoT-maskinvarutillverkare/integratör:** Tillverkare av IoT-maskinvara, integratörer som monterar maskinvara från olika tillverkare eller leverantörer som tillhandahåller maskinvara för en IoT-distribution som tillverkas eller integreras av andra leverantörer. Involverad i utveckling och integration av firmware, inbyggda operativsystem och inbyggd programvara.

* **Utvecklareutvecklare av IoT-lösningar:** Utvecklingen av en IoT-lösning görs vanligtvis av en lösningsutvecklare. Den här utvecklaren kan ingå i ett internt team eller en systemintegratör som specialiserat sig på den här aktiviteten. IoT-lösningsutvecklaren kan utveckla olika komponenter i IoT-lösningen från grunden, integrera olika standard- eller komponenter med öppen källkod eller anpassa en [IoT-lösningsaccelerator](/azure/iot-accelerators/).

* **IoT-lösningsoperatör:** När IoT-lösningen har distribuerats kräver den långsiktiga åtgärder, övervakning, uppgraderingar och underhåll. Dessa uppgifter kan utföras av ett internt team som består av it-specialister, maskinvaruoperationer och underhållsteam och domänspecialister som övervakar det korrekta beteendet hos den övergripande IoT-infrastrukturen.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Förstå automatisk enhetshantering för att konfigurera IoT-enheter i stor skala

Automatisk enhetshantering innehåller de många fördelarna med [enhetstvillingar](iot-hub-devguide-device-twins.md) och [modultvillingar](iot-hub-devguide-module-twins.md) för att synkronisera önskade och rapporterade tillstånd mellan molnet och enheterna. [Automatiska enhetskonfigurationer](iot-hub-auto-device-config.md) uppdaterar automatiskt stora uppsättningar tvillingar och sammanfattar förlopp och efterlevnad. Följande steg på hög nivå beskriver hur automatisk enhetshantering utvecklas och används:

* **IoT-maskinvarutillverkaren/integratören** implementerar enhetshanteringsfunktioner i ett inbäddat program med hjälp av [enhetstvillingar](iot-hub-devguide-device-twins.md). Dessa funktioner kan omfatta uppdateringar av inbyggd programvara, installation och uppdatering av programvara samt inställningshantering.

* **IoT-lösningsutvecklaren** implementerar hanteringslagret för enhetshantering med hjälp av [enhetstvillingar](iot-hub-devguide-device-twins.md) och [automatiska enhetskonfigurationer](iot-hub-auto-device-config.md). Lösningen bör omfatta att definiera ett operatörsgränssnitt för att utföra enhetshanteringsuppgifter.

* **IoT-lösningsoperatören** använder IoT-lösningen för att utföra enhetshanteringsuppgifter, särskilt för att gruppera enheter tillsammans, initiera konfigurationsändringar som uppdateringar av inbyggd programvara, övervaka förlopp och felsöka problem som uppstår.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT-maskinvarutillverkare/integratör

Följande är bästa praxis för maskinvarutillverkare och integratörer som arbetar med inbäddad programvaruutveckling:

* **Implementera [enhetstvillingar:](iot-hub-devguide-device-twins.md)** Enhetstvillingar möjliggör synkronisering av önskad konfiguration från molnet och för rapportering av aktuella konfigurations- och enhetsegenskaper. Det bästa sättet att implementera enhetstvillingar i inbäddade program är via [Azure IoT SDK:er](https://github.com/Azure/azure-iot-sdks). Enhetstvillingar är bäst lämpade för konfiguration eftersom de:

    * Stöd för dubbelriktad kommunikation.
    * Tillåt både anslutna och frånkopplade enhetstillstånd.
    * Följ principen om eventuell konsekvens.
    * Är fullt köbara i molnet.

* **Strukturera enhetstvillingen för enhetshantering:** Enhetstvillingen bör struktureras så att enhetshanteringsegenskaperna logiskt grupperas i sektioner. Om du gör det kan konfigurationsändringar isoleras utan att andra delar av tvillingen påverkas. Skapa till exempel ett avsnitt i önskade egenskaper för inbyggd programvara, ett annat avsnitt för programvara och ett tredje avsnitt för nätverksinställningar. 

* **Rapportera enhetsattribut som är användbara för enhetshantering:** Attribut som fysiskt enhetstillslag och modell, inbyggd programvara, operativsystem, serienummer och andra identifierare är användbara för rapportering och som parametrar för inriktning av konfigurationsändringar.

* **Definiera huvudtillstånden för rapporteringsstatus och förlopp:** Toppnivåtillstånd bör räknas upp så att de kan rapporteras till operatören. En uppdatering av den inbyggda programvaran skulle till exempel rapportera status som Aktuell, Nedladdning, Tillämpning, Pågår och Fel. Definiera ytterligare fält för mer information om varje tillstånd.

## <a name="iot-solution-developer"></a>Utvecklareutvecklare av IoT-lösningar

Följande är metodtips för IoT-lösningsutvecklare som bygger system baserade i Azure:

* **Implementera [enhetstvillingar:](iot-hub-devguide-device-twins.md)** Enhetstvillingar möjliggör synkronisering av önskad konfiguration från molnet och för rapportering av aktuella konfigurations- och enhetsegenskaper. Det bästa sättet att implementera enhetstvillingar i molnlösningar program är genom [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks). Enhetstvillingar är bäst lämpade för konfiguration eftersom de:

    * Stöd för dubbelriktad kommunikation.
    * Tillåt både anslutna och frånkopplade enhetstillstånd.
    * Följ principen om eventuell konsekvens.
    * Är fullt köbara i molnet.

* **Organisera enheter med hjälp av enhetstvillingtaggar:** Lösningen bör göra det möjligt för operatören att definiera kvalitetsringar eller andra uppsättningar av enheter baserat på olika distributionsstrategier såsom kanariefågel. Enhetsorganisationen kan implementeras i din lösning med hjälp av enhetstvillingtaggar och [-frågor](iot-hub-devguide-query-language.md). Enhetsorganisationen är nödvändig för att möjliggöra konfigurationsutrullningar på ett säkert och korrekt sätt.

* **Implementera [automatiska enhetskonfigurationer:](iot-hub-auto-device-config.md)** Automatiska enhetskonfigurationer distribuerar och övervakar konfigurationsändringar till stora uppsättningar IoT-enheter via enhetstvillingar.

   Automatisk enhetskonfigurationer måluppsättningar av enhetstvillingar via **målvillkoret,** som är en fråga på enhetstvillingtaggar eller rapporterade egenskaper. **Målinnehållet** är den uppsättning önskade egenskaper som ska ställas in i de riktade enhetstvillingarna. Målinnehållet bör anpassas till enhetens dubbla struktur som definieras av IoT-maskinvarutillverkaren/integratören. Måtten är frågor om enhetstvillingrapporterade egenskaper och bör också anpassas till **enhetens** tvillingstruktur som definieras av IoT-maskinvarutillverkaren/integratören.

   Automatiska enhetskonfigurationer körs för första gången strax efter att konfigurationen har skapats och sedan med fem minuters intervall. De drar också nytta av IoT Hub utför enhetstvillingåtgärder med en hastighet som aldrig kommer att överskrida [begränsningsgränserna](iot-hub-devguide-quotas-throttling.md) för enhetstvillingläsningar och uppdateringar.

* **Använd [enhetsetableringstjänsten](../iot-dps/how-to-manage-enrollments.md):** Lösningsutvecklare bör använda enhetsetableringstjänsten för att tilldela enhetstvillingtaggar till nya enheter, så att de automatiskt konfigureras av **automatiska enhetskonfigurationer** som är inriktade på tvillingar med den taggen. 

## <a name="iot-solution-operator"></a>IoT-lösningsoperatör

Följande är metodtips för IoT-lösningsoperatörer som använder en IoT-lösning som bygger på Azure:

* **Organisera enheter för hantering:** IoT-lösningen bör definiera eller möjliggöra skapandet av kvalitetsringar eller andra uppsättningar enheter baserat på olika distributionsstrategier, till exempel kanariefågel. Uppsättningarna av enheter kommer att användas för att distribuera konfigurationsändringar och för att utföra andra åtgärder för enhetshantering i skala.

* **Utför konfigurationsändringar med hjälp av en stegvis utrullning:**  En stegvis utrullning är en övergripande process där en operatör distribuerar ändringar till en breddning av IoT-enheter. Målet är att göra ändringar gradvis för att minska risken för att göra storskaliga bryta förändringar.Operatören bör använda lösningens gränssnitt för att skapa en [automatisk enhetskonfiguration](iot-hub-auto-device-config.md) och målvillkoret bör inriktas på en första uppsättning enheter (t.ex. en kanariefågelgrupp). Operatören bör sedan validera konfigurationsändringen i den första uppsättningen enheter.

   När valideringen är klar uppdaterar operatören den automatiska enhetskonfigurationen så att den innehåller en större uppsättning enheter. Operatören bör också ange att prioriteten för konfigurationen ska vara högre än andra konfigurationer som för närvarande är inriktade på dessa enheter. Utrullningen kan övervakas med hjälp av de mått som rapporteras av den automatiska enhetskonfigurationen.

* **Utför återställningar vid fel eller felkonfigurationer:**  En automatisk enhetskonfiguration som orsakar fel eller felkonfigurationer kan återställas genom att ändra **målvillkoret** så att enheterna inte längre uppfyller målvillkoret. Kontrollera att en annan automatisk enhetskonfiguration med lägre prioritet fortfarande är avsedd för dessa enheter. Kontrollera att återställningen lyckades genom att visa måtten: Den återställda konfigurationen bör inte längre visa status för oriktade enheter och den andra konfigurationens mått bör nu innehålla antal för de enheter som fortfarande är riktade.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du implementerar enhetstvillingar i [Förstå och använd enhetstvillingar i IoT Hub](iot-hub-devguide-device-twins.md).

* Gå igenom stegen för att skapa, uppdatera eller ta bort en automatisk enhetskonfiguration i [Konfigurera och övervaka IoT-enheter i stor skala](iot-hub-auto-device-config.md).

* Implementera ett uppdateringsmönster för inbyggd programvara med hjälp av enhetstvillingar och automatiska enhetskonfigurationer i [självstudiekurs: Implementera en uppdateringsprocess för inbyggd programvara](tutorial-firmware-update.md).
