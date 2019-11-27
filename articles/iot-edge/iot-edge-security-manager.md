---
title: Lär dig hur säkerhetshanteraren skyddar enheter, software – Azure IoT Edge | Microsoft Docs
description: Hanterar den IoT Edge-enhet security effekten av åtgärder och integriteten för säkerhetstjänster.
services: iot-edge
keywords: säkerhet, säker element, enklaven, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 90cb7cf0a30ea0ebfe00454288de25ddf6e58d52
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457526"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge-säkerhetshanteraren

Azure IoT Edge security manager är en väl begränsad säkerhet kärna för att skydda IoT Edge-enhet och alla dess komponenter genom att abstrahera säker silicon maskinvara. Det är blick punkten för säkerhets härdning och ger teknisk integrerings plats för OEM (Original Equipment Manufacturer).

![Azure IoT Edge-säkerhetshanteraren](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge-säkerhetshanteraren syftar till att skydda integriteten för IoT Edge-enhet och alla åtgärder för inbyggd programvara. Säkerhets hanteraren övergår till förtroende från underliggande maskin varu rot för betrodd maskin vara (om tillgängligt) för att starta IoT Edge körning och övervaka pågående åtgärder.  IoT Edge Security Manager är program vara som arbetar tillsammans med säker Silicon-maskinvara (där det är tillgängligt) för att tillhandahålla högsta möjliga säkerhets garanti.  

Ansvars områdena för IoT Edge Security Manager är, men är inte begränsade till:

* Säker och mätt hur startas Azure IoT Edge-enhet.
* Enhetsetablering identitet och övergång av förtroende om tillämpligt.
* Vara värd för och skydda enhetskomponenter i molntjänster som Device Provisioning-tjänsten.
* Etablera IoT Edge moduler med unika identiteter på ett säkert sätt.
* Gatekeepern enhet maskinvara förtroenderoten via notarie-tjänster.
* Övervakar du integriteten hos IoT Edge-åtgärder vid körning.

IoT Edge-säkerhetshanteraren omfattar tre komponenter:

* Daemon för IoT Edge-säkerhet.
* Maskinvara security modulen plattform abstraction Layer (HSM PAL).
* Valfritt men rekommenderas maskinvara silicon roten för förtroende eller HSM.

## <a name="the-iot-edge-security-daemon"></a>Daemon för IoT Edge-säkerhet

IoT Edge Security daemon ansvarar för de logiska åtgärderna i IoT Edge Security Manager. Den representerar en betydande del av den betrodda dator basen för den IoT Edge enheten. 

### <a name="design-principles"></a>Designprinciper

IoT Edge Security daemon följer två grundläggande principer: maximera drifts integriteten och minimera överdriven storlek och omsättning.

#### <a name="maximize-operational-integrity"></a>Maximera operativa integritet

IoT Edge Security daemon fungerar med högsta möjliga integritet i försvars förmågan hos alla eventuella rot-och maskin varu förtroenden. Med rätt integrering roten av förtroende maskinvara mäter och övervakar daemonen security statiskt och vid körning för att motstå manipulation.

Fysisk åtkomst är alltid ett hot mot IoT-enheter. Förtroende för maskin varu roten spelar en viktig roll i att skydda integriteten för IoT Edge Security daemon.  Maskin varu roten i förtroendet levereras i två sorter:

* säker element för skydd av känslig information som hemligheter och kryptografiska nycklar.
* säker enklaver för skydd av hemligheter som nycklar och känsliga arbetsbelastningar som mätning och fakturering.

Det finns två typer av körnings miljöer som kan använda maskin varu roten:

* Standard miljön eller den avancerade körnings miljön (REE) som förlitar sig på användningen av säkra element för att skydda känslig information.
* Den betrodda körnings miljön (TEE) som förlitar sig på användningen av säker enklaven-teknik för att skydda känslig information och ge skydd till program körning.

För enheter som använder säker enclaves som maskin varu rot bör känslig logik i IoT Edge Security daemon finnas inuti enklaven.  Icke-känsliga delar av Security daemon kan ligga utanför TEE.  I samtliga fall bör de ursprungliga design tillverkarna (ODM) och OEM-tillverkare (Original Equipment Manufacturer) utöka förtroendet från sin HSM för att mäta och försvara integriteten hos IoT Edge Security daemon vid start och körning.

#### <a name="minimize-bloat-and-churn"></a>Minimera överdriven storlek och omsättning

En annan kärn princip för IoT Edge Security daemon är att minimera omsättningen.  För den högsta förtroende nivån kan IoT Edge säkerhetsdaemonen vara nära kopplad till enhetens maskin varu rot och arbeta som inbyggd kod.  Det är vanligt att de här typerna av Realizations uppdaterar daemon-programvaran via maskin varu rotens säkra uppdaterings vägar (till skillnad från operativ system som tillhandahålls uppdaterings mekanismer), vilket kan vara svårt i vissa scenarier.  Även om säkerhets förnyelse rekommenderas för IoT-enheter, kan omfattande uppdaterings krav eller stora uppdaterings nytto laster expandera hot ytan på många sätt.  Exempel är hoppar över av uppdateringar för att maximera operativ tillgänglighet eller roten av förtroende maskinvara begränsad för att bearbeta stora uppdatering nyttolaster.  Därför är designen av IoT Edge Security daemon koncis för att hålla utrymmes-och Trusted Computings-basen små och minimera uppdaterings kraven.

### <a name="architecture-of-iot-edge-security-daemon"></a>Arkitektur för IoT Edge security daemon

![Daemon för Azure IoT Edge-säkerhet](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge Security daemon drar nytta av alla tillgängliga maskin varu roten för förtroende teknik för säkerhets härdning.  Det ger också till gång till en Split-World-åtgärd mellan en standard-/Rich körnings miljö (REE) och en TEE (Trusted Execution Environment) när maskin varu teknikerna erbjuder betrodda körnings miljöer. Rollbaserade gränssnitt gör det möjligt för huvud komponenterna i IoT Edge att garantera integriteten hos IoT Edges enheten och dess åtgärder.

#### <a name="cloud-interface"></a>Molngränssnitt

Cloud Interface gör det möjligt för IoT Edge Security daemon att komma åt moln tjänster som till exempel molnet till enhets säkerhet som säkerhets förnyelse.  Till exempel använder IoT Edge Security daemon för närvarande det här gränssnittet för att komma åt Azure IoT Hub [Device Provisioning-tjänsten](https://docs.microsoft.com/azure/iot-dps/) för livs cykel hantering av enhetens identitet.  

#### <a name="management-api"></a>Hanterings-API

IoT Edge Security daemon erbjuder ett hanterings-API, som anropas av IoT Edge-agenten när en IoT Edge-modul skapas/startas/stoppas/tas bort. Security daemon lagrar "registreringar" för alla aktiva moduler. Dessa registreringar mappa en modul identitet till vissa egenskaper för modulen. Några exempel för de här egenskaperna är process-ID (pid) för den process som körs i behållaren eller hashen för innehållet i docker-behållaren.

Dessa egenskaper används av arbets belastnings-API: et (beskrivs nedan) för att kontrol lera att anroparen har behörighet att utföra en åtgärd.

Hanterings-API: et är ett privilegie rad API som endast kan anropas från den IoT Edge agenten.  Eftersom IoT Edge security daemon startar och startar IoT Edge-agenten, kan det skapa en implicit registrering för IoT Edge-agenten när den har godkänt dem att IoT Edge-agenten inte har manipulerats. Samma attesterings process som används för arbets belastnings-API: t begränsar också åtkomsten till hanterings-API: t till endast den IoT Edge agenten.

#### <a name="container-api"></a>Behållaren API

Behållar-API: et samverkar med det behållar system som används för modul hantering, som Moby eller Docker.

#### <a name="workload-api"></a>Arbetsbelastningen API

Arbets belastnings-API: et är tillgängligt för alla moduler. Det ger bevis på identitet, antingen som en HSM-rotad signerad token eller ett X509-certifikat och motsvarande förtroende paket till en modul. Trust-paketet innehåller CA-certifikat för alla andra servrar som modulerna ska ha förtroende för.

IoT Edge Security daemon använder en attesterings process för att skydda detta API. När en modul anropar det här API: et försöker säkerhets demon att hitta en registrering för identiteten. Om detta lyckas använder egenskaperna för registreringen för att mäta modulen. Om resultatet av mätnings processen matchar registreringen genereras ett nytt identitets bevis. Motsvarande CA-certifikat (förtroende bunt) returneras till modulen.  Modulen använder det här certifikatet för att ansluta till andra moduler på IoT-hubb eller starta en server. När den signerade token eller certifikatet närmar sig upphör att gälla, är det ansvaret för modulen att begära ett nytt certifikat. 

### <a name="integration-and-maintenance"></a>Integrering och underhåll

Microsoft underhåller huvud kod basen för [IoT Edge Security daemon på GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Installation och uppdateringar

Installation och uppdateringar av IoT Edge Security daemon hanteras via operativ systemets pakethanteringssystem. IoT Edge enheter med förtroende för maskin varu roten bör ge ytterligare härdning av daemonens integritet genom att hantera dess livs cykel genom hanterings systemen för säker start och uppdateringar. Enhets tillverkarna bör utforska dessa vägar baserat på deras respektive enhets funktioner.

#### <a name="versioning"></a>Versionshantering

IoT Edge-körningen spårar och rapporterar versionen av IoT Edge security daemon. Versionen rapporteras som *runtime. Platform. version* -attributet för den rapporterade egenskapen för den IoT Edge agent-modulen.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Security module platform HAL (HSM PAL)

HSM-PAL avlägsnar alla roten av förtroende maskinvara för att isolera utvecklare eller användare av IoT Edge från deras komplexitet.  Den innehåller en kombination av procedurer för application programming interface (API) och kommunikation mellan domäner, till exempel kommunikation mellan en standard körnings miljö och en säker enklaven.  Den faktiska implementeringen av HSM-PAL beror på den säkra maskinvaran används. Den befintliga funktionen möjliggör användning av praktiskt taget all säker Silicon-maskinvara.

## <a name="secure-silicon-root-of-trust-hardware"></a>Säker silicon roten förtroende maskinvara

Säker silicon är nödvändigt att förtroendeankare förtroende i IoT Edge-enhet maskinvara.  Säker silicon finnas i olika att inkludera Trusted Platform Module (TPM), inbäddade säker Element (eSE), ARM TrustZone, Intel SGX och anpassade säker silicon tekniker.  Användning av säker kisel roten av förtroende i enheter rekommenderas med tanke på de hot som är kopplade till IoT-enheters fysiska tillgänglighet.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge security manager-integrering och underhåll

IoT Edge Security Manager syftar till att identifiera och isolera de komponenter som skyddar Azure IoT Edge plattformens säkerhet och integritet för anpassad härdning. Tredje part, som enhets tillverkare, bör använda anpassade säkerhetsfunktioner som är tillgängliga med enhetens maskin vara.  I avsnittet Nästa steg finns länkar som visar hur du kan förstärka Azure IoT Security Manager med Trusted Platform Module (TPM) på Linux-och Windows-plattformar. Dessa exempel använder program vara eller virtuella TPM: er, men används direkt för att använda diskreta TPM-enheter.  

## <a name="next-steps"></a>Nästa steg

Läs bloggen om [att skydda den intelligenta gränsen](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Skapa och etablera en [IoT Edge enhet med en virtuell TPM på en virtuell Linux-dator](how-to-auto-provision-simulated-device-linux.md).

Skapa och etablera en [IoT Edge enhet med en simulerad TPM i Windows](how-to-auto-provision-simulated-device-windows.md).
