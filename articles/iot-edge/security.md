---
title: Säkerhet i Azure IoT-Edge | Microsoft Docs
description: Säkerhet, autentisering och auktorisering av IoT-gränsenheterna
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f198efe9ff5e4862a3bbe872ab50e5848c9dbb5c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030588"
---
# <a name="securing-azure-iot-edge"></a>Att säkra Azure IoT-kant

Att säkra intelligent gränsen är nödvändigt att förtroendet för driften av en slutpunkt till slutpunkt IoT-lösning. Azure IoT-gräns är utformad för säkerhet som kan utökas till olika risker profiler, distributionsscenarier, och ger samma skydd som du förväntar dig från alla Azure-tjänster.

Azure IoT-Edge körs på olika maskinvara stöder både Linux och Windows och gäller för olika distributionsscenarier.  Bedömda risken beror på många saker, inklusive lösning ägarskap, distribution geografi, data känslighet, sekretess, lodräta och regelmässig programkrav.  I stället för att erbjuda konkreta lösningar på specifika scenarier kan det vara bra att utforma en utökningsbar säkerhetsramverk baserat på väl grounded principer som har utformats för att skala. 
 
Den här artikeln innehåller en översikt över security-ramverket. Mer information finns i [skydda intelligent kanten][lnk-edge-blog].

## <a name="standards"></a>Standarder

Standarder befordra enkel granskning och enkel implementering, vilka är hallmark säkerhet.  En väl utformad arkitektur säkerhetslösning bör låna ut sig själva granskning under utvärderingen för att skapa förtroende och får inte vara ett gränsvärde till distributionen.  Design av framework att skydda Azure IoT kant utgår från Tidstestat och industrin beprövade säkerhet protokoll som ska använda förtrogenhet och återanvändning. 

## <a name="authentication"></a>Autentisering

Utan en osäker känna till vilka aktörer, enheter och komponenter deltar i leveransen av värdet via en slutpunkt till slutpunkt IoT-lösning är av yttersta vikt i att skapa förtroende.  Kunskapen erbjuder säker ansvar att aktivera grunden för åtkomstkontroll.  Azure IoT-Edge uppnår den här kunskapen via autentisering.  Den primära mekanismen för autentisering för Azure IoT kant-plattformen är certifikatbaserad autentisering.  Den här mekanismen härleds från en uppsättning standarder för infrastruktur för offentliga nycklar (PKiX) av Internet Engineering Task Force (IETF).     

Azure IoT kant säkerhetsramverk anropar för unikt certifikat identiteter för alla enheter, moduler (behållare som kapslar in logik i enheten) och aktörer som interagerar med Azure IoT gränsenheten vara den fysiskt eller via en nätverksanslutning.  Inte alla scenario eller komponent kan låna ut sig själva certifikatbaserad autentisering som erbjuder utökningsbarhet security-ramverket för säker vägar för logi. 

## <a name="authorization"></a>Auktorisering

Möjligheten att delegera behörighet och kontrollera åtkomsten är avgörande för att uppnå en grundläggande säkerhetsprincipen – principen om minsta behörighet.  Enheter, moduler och aktörer kan få åtkomst till resurser och data i definitionsområdet behörighet och endast när det är arkitektoniskt tillåtna.  Det innebär att vissa behörigheter kan konfigureras med tillräcklig behörighet och andra arkitektoniskt tillämpas.  Till exempel när en modul tillåtas genom Privilegierade konfiguration att initiera en anslutning till Azure IoT Hub, finns det ingen anledning varför en modul i en Azure IoT-enhet ska komma åt dubbla av en modul i en annan Azure IoT-enhet.  Därför är skulle det senare arkitektoniskt förhindras. 

Andra auktorisering inkluderar certifikat för signering rättigheter och rollbaserad åtkomstkontroll (RBAC).  Utökning av framework säkerhet tillåter införandet av andra mogen för auktorisering. 

## <a name="attestation"></a>Attestering

Attesteringen säkerställer integriteten hos programvara bits.  Det är viktigt för att upptäcka och förhindra skadlig kod.  Azure IoT kant säkerhetsramverk klassificerar attestering enligt tre huvudkategorier:

* Statisk hälsoattestering
* Attesteringen av Runtime
* Attesteringen av programvara

### <a name="static-attestation"></a>Statisk hälsoattestering

Statisk attestering är att kontrollera integriteten hos alla programvara bitar inklusive operativsystem, alla körningar och påslagning av konfigurationsinformation på enheten.  Den kallas ofta för säker start.  Säkerhetsramverk för Azure IoT-gränsenheterna utökar silicon leverantörer och inkluderar säker ingrained maskinvarukapacitet att garantera statiska attestering processer. Här ingår säker start och säker inbyggd programvara uppgradera processer.  Arbeta i nära samarbete med silicon leverantörer eliminerar överflödiga firmware lager därigenom minimera ytan hot. 

### <a name="runtime-attestation"></a>Attesteringen av Runtime

När ett system har slutförts validerade startprocessen och är igång och körs, väl utvecklade säkra system skulle identifiera försöker att mata in skadlig kod via system-portar och gränssnitt och vidta lämpliga motåtgärder.  Intelligent edge enheter i fysiska förvaring av skadliga aktörer, är det möjligt att mata in malcontent sätt än enhetens användargränssnitt som manipulation via och kanal på klientsidan attacker.   Sådana malcontent som kan vara i form av skadlig kod eller obehörig konfigurationsändringar, normalt inte skulle kunna identifieras av processen för säker Start eftersom de inträffar efter att startprocessen.  Motåtgärder erbjuds eller tvingas av enhetens maskinvara avsevärt bidrar till warding av sådana hot.  Säkerhetsramverk för Azure IoT kant som explicit anropar för tillägg för combatting runtime hot.     

### <a name="software-attestation"></a>Attesteringen av programvara

Alla felfri system, inklusive intelligenta edge system måste vara lämpar sig för korrigeringsfiler och uppgraderingar.  Säkerhet är viktigt för update-processer annars de kan vara möjliga hotvektorer.  Säkerhetsramverk för Azure IoT kant-anrop för uppdateringar via mäts och signerade paket för att garantera dataintegriteten och autentisera källan paket.  Detta gäller alla operativsystem och program programvara bits. 

## <a name="hardware-root-of-trust"></a>Maskinvara förtroenderoten

För många distributioner av intelligent gränsenheterna, särskilt de som har distribuerats på platser där potentiell skadlig aktörer har fysisk åtkomst till enheten, är säkerheten som erbjuds av maskinvaran det senaste skyddet för skydd.  Förankrade förtroende i manipulationsindikerande motståndskraftiga maskinvara är mest avgörande för dessa distributioner.  Säkerhetsramverk för Azure IoT kant innebär samarbete av säker silicon maskinvaruleverantörer att erbjuda olika varianter av maskinvara förtroenderoten att passa olika risker profiler och distributionsscenarier. Dessa inkluderar användning av säker silicon efterlever vanliga protokollet säkerhetskrav som Trusted Platform Module (ISO/IEC 11889) och Trusted Computing Group's Device identifierare sammansättning motorn (RAPPORTANVÄNDARNA).  Dessa inkluderar även säker skall tekniker som TrustZones och programvara Guard tillägg (SGX). 

## <a name="certification"></a>Certifiering

Azure IoT kant framework innehåller certifieringskraven för att hjälpa kunderna att fatta välgrundade beslut om upphandling Azure IoT gränsenheterna för deras distribution.  Grundläggande att dessa krav är certifieringar som rör säkerhetsanspråk och certifikat som rör verifiering av säkerheten implementeringen.  En säkerhetscertifiering för anspråk skulle till exempel meddela att IoT gränsenheten använder säker maskinvara som är kända för stå emot Start-attacker. En certifikatutfärdare för verifiering skulle informera säker maskinvara implementerades korrekt för att erbjuda detta värde i enheten.  Enligt principen om enkelhet erbjuder ramen visionen för att hålla för certifiering minimal.   

## <a name="extensibility"></a>Utökningsbarhet

Utökningsbarhet är en förstklassig allmänheten i säkerhetsramverk Azure IoT kant.  Det står skäl att säkerhet sömlöst bör utvecklas i lockstep till adressen nya scenarier med IoT-teknik som kör olika typer av business transformationer.  Azure IoT kant säkerhetsramverk börjar med en god grund den bygger upp i utökningsbarhet i olika dimensioner att inkludera: 

* Första part säkerhetstjänster som enheten Etableringstjänsten för Azure IoT-hubb.
* Tjänster från tredje part som hanterade säkerhetstjänster för olika program lodräta annonser (till exempel industriell eller sjukvården) eller teknik fokus (t.ex. säkerhetsövervakning i nät nätverk eller silicon maskinvara attestering services) via en omfattande nätverk partners.
* Äldre datorer att inkludera onlineåterställningspunkter med alternativa säkerhetsstrategier som använder säker teknik än certifikat för autentisering och identitet management.
* Skydda maskinvara för sömlös införandet av ny teknik för säker maskinvara och silicon partner bidrag.

Här är några exempel på dimensioner utökningsbarhet och Azure IoT kant säkerhetsramverk är avsedd att vara säkra från grunden för att stödja den här utökningsbarhet. 

I slutändan resultat högsta lyckades på säkra intelligent kanten av samarbetsfunktioner bidrag från en öppen gemenskap som drivs av den gemensamma intressen för att skydda IoT.  Avgifterna kan vara i form av säker tekniker eller tjänster.  Azure IoT kant säkerhetsramverk ger en god grund för säkerhet som kan utökas för maximal täckning att erbjuda samma nivå av förtroende och integritet i intelligent kanten som med Azure-molnet.  

## <a name="next-steps"></a>Nästa steg

Läs mer om hur Azure IoT kant är [skydda intelligent kanten][lnk-edge-blog].

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 