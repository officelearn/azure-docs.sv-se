---
title: Azure IoT Edge-säkerhetshanteraren – Azure IoT Edge
description: Hanterar säkerhetspositionen för IoT Edge-enheten och säkerhetstjänsternas integritet.
services: iot-edge
keywords: säkerhet, säkert element, enklav, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548704"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge-säkerhetshanterare

Azure IoT Edge-säkerhetshanteraren är en välbesänkt säkerhetskärna för att skydda IoT Edge-enheten och alla dess komponenter genom att abstrahera den säkra kiselmaskinvaran. Säkerhetschefen är i fokus för säkerhetshärdning och tillhandahåller teknikintegrationspunkt till oem-tillverkare (Original Equipment Manufacturers).

![Azure IoT Edge-säkerhetshanterare](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge säkerhetshanterare syftar till att försvara integriteten hos IoT Edge-enheten och alla inneboende programvaruoperationer. Säkerhetshanteraren överför förtroende från underliggande maskinvarurot för förtroendemaskinvara (om tillgängligt) till bootstrap i IoT Edge-körningen och övervakar pågående åtgärder.  IoT Edge security manager är programvara som arbetar tillsammans med säker kiselhårdvara (om tillgängligt) för att hjälpa till att leverera högsta möjliga säkerhetsgarantier.  

IoT Edge-säkerhetshanterarens ansvar ingår, men är inte begränsat till:

* Säker och uppmätt bootstrapping av Azure IoT Edge-enheten.
* Etablering av enhetsidentitet och övergång av förtroende i förekommande fall.
* Vara värd för och skydda enhetskomponenter för molntjänster som enhetsetableringstjänst.
* Etablera IoT Edge-moduler med unika identiteter på ett säkert sätt.
* Gatekeeper till enheten hårdvara rot av förtroende genom notarietjänster.
* Övervaka integriteten för IoT Edge-åtgärder vid körning.

IoT Edge-säkerhetshanteraren innehåller tre komponenter:

* IoT Edge säkerhetsdemon.
* Sammanfattning av maskinvarusäkerhetsmodulplattformens abstraktionslager (HSM PAL).
* Valfri men rekommenderas starkt hårdvara kisel rot av förtroende eller HSM.

## <a name="the-iot-edge-security-daemon"></a>Säkerhetsdemonen IoT Edge

Säkerhetsdemonen IoT Edge ansvarar för de logiska åtgärderna för IoT Edge-säkerhetshanteraren. Den representerar en betydande del av den betrodda datorbasen för IoT Edge-enheten.

### <a name="design-principles"></a>Designprinciper

IoT Edge-säkerhetsdemonen följer två grundläggande principer: maximera den operativa integriteten och minimera svälla och churn.

#### <a name="maximize-operational-integrity"></a>Maximera den operativa integriteten

IoT Edge-säkerhetsdemonen fungerar med högsta möjliga integritet inom försvarskapaciteten för en viss rot av förtroendemaskinvara. Med korrekt integration mäter roten av förtroendehårdvara och övervakar säkerhetsdemonen statiskt och vid körning för att motstå manipulering.

Fysisk åtkomst är alltid ett hot mot IoT-enheter. Maskinvaruroten av förtroende spelar en viktig roll för att försvara integriteten hos IoT Edge-säkerhetsdemonen.  Hårdvara rot av förtroende finns i två sorter:

* säkra element för att skydda känslig information som hemligheter och kryptografiska nycklar.
* säkra enklaver för skydd av hemligheter som nycklar och känsliga arbetsbelastningar som mätning och fakturering.

Det finns två typer av körningsmiljöer för att använda maskinvaruroten för förtroende:

* Standardmiljön eller den avancerade körningsmiljön (REE) som är beroende av användning av säkra element för att skydda känslig information.
* Den betrodda körningsmiljön (TEE) som är beroende av användningen av säker enklavteknik för att skydda känslig information och erbjuda skydd för programkörning.

För enheter som använder säkra enklaver som maskinvarurot av förtroende bör känslig logik inom IoT Edge-säkerhetsdemon vara inne i enklaven.  Icke-känsliga delar av säkerhetsdemonen kan vara utanför TEE.  I vilket fall som helst bör originaldesigntillverkare (ODM) och oem-tillverkare (Original Equipment Manufacturers) utöka förtroendet från sin HSM för att mäta och försvara integriteten hos IoT Edge-säkerhetsdemonen vid uppstart och körning.

#### <a name="minimize-bloat-and-churn"></a>Minimera svälla och churn

En annan grundläggande princip för IoT Edge säkerhetsdemon är att minimera churn.  För den högsta nivån av förtroende kan IoT Edge-säkerhetsdemonen tätt kopplas ihop med enhetens maskinvarurot av förtroende och fungerar som inbyggd kod.  Det är vanligt att dessa typer av insikter för att uppdatera demon programvara genom hårdvaran roten till förtroende säkra uppdateringsvägar (i motsats till OS som uppdateringsmekanismer), vilket kan vara utmanande i vissa scenarier.  Säkerhetsförnyelse rekommenderas för IoT-enheter, men överdrivna uppdateringskrav eller stora uppdateringsnyttolaster kan utöka hotytan på många sätt.  Exempel är att hoppa över uppdateringar för att maximera drifttillgängligheten eller roten av förtroendemaskinvara som är för begränsad för att bearbeta stora uppdateringsnyttolaster.  Som sådan är utformningen av IoT Edge säkerhetsdemon kortfattad för att hålla fotavtryck och betrodda datorbas liten och för att minimera uppdateringskraven.

### <a name="architecture-of-iot-edge-security-daemon"></a>Arkitektur för IoT Edge-säkerhetsdemon

![Azure IoT Edge säkerhetsdemon](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge-säkerhetsdemonen drar nytta av alla tillgängliga maskinvarurot av förtroendeteknik för säkerhetshärdning.  Det möjliggör också delad värld-drift mellan en standard /rik körningsmiljö (REE) och en betrodd körningsmiljö (TEE) när maskinvarutekniker erbjuder betrodda körningsmiljöer. Rollspecifika gränssnitt gör det möjligt för huvudkomponenterna i IoT Edge att säkerställa integriteten hos IoT Edge-enheten och dess verksamhet.

#### <a name="cloud-interface"></a>Molngränssnitt

Molngränssnittet gör det möjligt för IoT Edge-säkerhetsdemonen att komma åt molntjänster som molnkompförekomst till enhetssäkerhet som säkerhetsförnyelse.  Till exempel använder IoT Edge-säkerhetsdemonen för närvarande det här gränssnittet för att komma åt Azure IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) för livscykelhantering av enhetsidentitet.  

#### <a name="management-api"></a>Hanterings-API

IoT Edge-säkerhetsdemon erbjuder ett hanterings-API, som anropas av IoT Edge-agenten när du skapar/startar/stoppar/tar bort en IoT Edge-modul. Säkerhetsdemon lagrar "registreringar" för alla aktiva moduler. Dessa registreringar mappar en moduls identitet till vissa egenskaper för modulen. Dessa modulegenskaper omfattar till exempel processidentifieraren (pid) för processen som körs i behållaren och hash-värdet för dockerbehållarens innehåll.

Dessa egenskaper används av arbetsbelastnings-API:et (beskrivs nedan) för att kontrollera att anroparen är auktoriserad för en åtgärd.

Hanterings-API:et är ett privilegierat API som endast kan anropas från IoT Edge-agenten.  Eftersom IoT Edge-säkerhetsdemonen bootstraps och startar IoT Edge-agenten verifierar den att IoT Edge-agenten inte har manipulerats, då kan den skapa en implicit registrering för IoT Edge-agenten. Samma attestation-process som arbetsbelastnings-API:et använder begränsar också åtkomsten till hanterings-API:et till endast IoT Edge-agenten.

#### <a name="container-api"></a>Api för behållare

Behållar-API:et interagerar med det behållarsystem som används för modulhantering, till exempel Moby eller Docker.

#### <a name="workload-api"></a>API för arbetsbelastning

Arbetsbelastnings-API:et är tillgängligt för alla moduler. Det ger identitetsbevis, antingen som en HSM-rotad signerad token eller ett X509-certifikat, och motsvarande förtroendepaket till en modul. Förtroendepaketet innehåller CA-certifikat för alla andra servrar som modulerna bör lita på.

Säkerhetsdemonen IoT Edge använder en attestation-process för att skydda det här API:et. När en modul anropar det här API:et försöker säkerhetsdemonen hitta en registrering för identiteten. Om det lyckas används egenskaperna för registreringen för att mäta modulen. Om resultatet av mätprocessen matchar registreringen genereras ett nytt identitetsbevis. Motsvarande CERTIFIKATUTFÄRDARCERTIFIKAT (förtroendepaket) returneras till modulen.  Modulen använder det här certifikatet för att ansluta till IoT Hub, andra moduler eller starta en server. När den signerade token eller certifikatet närmar sig förfallodatum är det modulens ansvar att begära ett nytt certifikat.

### <a name="integration-and-maintenance"></a>Integration och underhåll

Microsoft har huvudkodbasen för [säkerhetsdemonen IoT Edge på GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Installation och uppdateringar

Installation och uppdateringar av säkerhetsdemonen IoT Edge hanteras via operativsystemets pakethanteringssystem. IoT Edge-enheter med maskinvarurot av förtroende bör ge ytterligare härdning till integriteten hos demonen genom att hantera dess livscykel genom de säkra start- och uppdateringshanteringssystemen. Enhetstillverkare bör utforska dessa vägar baserat på deras respektive enhetsfunktioner.

#### <a name="versioning"></a>Versionshantering

IoT Edge-körningen spårar och rapporterar versionen av säkerhetsdemonen IoT Edge. Versionen rapporteras som *attributet runtime.platform.version* för egenskapen IoT Edge agent module reported.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Abstraktionslager för maskinvarusäkerhetsmodul (HSM PAL)

HSM PAL abstraherar all rot av förtroende hårdvara för att isolera utvecklaren eller användaren av IoT Edge från deras komplexitet.  Den innehåller en kombination av API (Application Programming Interface) och transdomänkommunikationsförfaranden, till exempel kommunikation mellan en standardkörningsmiljö och en säker enklav.  Den faktiska implementeringen av HSM PAL beror på den specifika säkra maskinvara som används. Dess existens möjliggör användning av praktiskt taget alla säkra kisel hårdvara.

## <a name="secure-silicon-root-of-trust-hardware"></a>Säker kiselrot av förtroendehårdvara

Säkert kisel är nödvändigt för att förankra förtroendet inuti IoT Edge-enhetens maskinvara.  Säkert kisel finns i olika att inkludera Trusted Platform Module (TPM), embedded Secure Element (eSE), ARM TrustZone, Intel SGX och anpassade säkra kiseltekniker.  Användning av säker kisel rot av förtroende för enheter rekommenderas med tanke på de hot som är förknippade med fysisk tillgänglighet för IoT-enheter.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integration och underhåll av IoT Edge-säkerhetschef

IoT Edge-säkerhetshanteraren syftar till att identifiera och isolera de komponenter som försvarar säkerheten och integriteten för Azure IoT Edge-plattformen för anpassad härdning. Tredje part, som enhetstillverkare, bör använda sig av anpassade säkerhetsfunktioner som är tillgängliga med deras enhetsmaskinvara.  Se nästa steg avsnitt för länkar som visar hur du härda Azure IoT säkerhetshanteraren med Trusted Platform Module (TPM) på Linux och Windows-plattformar. Dessa exempel använder programvara eller virtuella TPM:er men gäller direkt för att använda diskreta TPM-enheter.  

## <a name="next-steps"></a>Nästa steg

Läs bloggen om [att säkra den intelligenta kanten](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Skapa och etablera en [IoT Edge-enhet med en virtuell TPM på en virtuell Linux-dator](how-to-auto-provision-simulated-device-linux.md).

Skapa och etablera en [IoT Edge-enhet med en simulerad TPM i Windows](how-to-auto-provision-simulated-device-windows.md).
