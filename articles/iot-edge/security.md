---
title: Säkerhet i Azure IoT Edge | Microsoft Docs
description: Säkerhet, autentisering och auktorisering av IoT Edge-enheter
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 890189f6b6e18ef10f274324915cc7dee019f1f0
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249143"
---
# <a name="securing-azure-iot-edge"></a>Skydda Azure IoT Edge

Att skydda en intelligent gräns är nödvändigt att förtroende för driften av en IoT-lösning från slutpunkt till slutpunkt. Azure IoT Edge är utformat för säkerhet som kan utökas till olika riskprofiler, distributionsscenarier, och erbjuder samma skydd som du förväntar dig från alla Azure-tjänster.

Azure IoT Edge körs på en annan maskinvara, stöder både Linux och Windows och gäller för olika distributionsscenarier.  Utvärderade risk beror på många överväganden inklusive lösning ägarskap, distributionens geografiska omfattning, känsliga data, sekretess, programmet lodräta och administrativa krav.  I stället för att erbjuda konkreta lösningar på specifika scenarier, det vara bra att utforma en utökningsbar security framework baserat på väl förankrad principer som har utformats för att skala. 
 
Den här artikeln innehåller en översikt över security framework. Mer information finns i [skydda en intelligent gräns](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standarder

Standarder för enklare enkel granskning och genomförandet, som är hallmark säkerhet.  En väl utformad arkitektur säkerhetslösning bör lånar ut sig själv till granskning under utvärderingen kan skapa förtroende och får inte vara ett gränsvärde till distribution.  Design av framework för att skydda Azure IoT Edge utgår från beprövade och beprövade security protokoll som ska använda kännedom om och återanvändning. 

## <a name="authentication"></a>Autentisering

Det är avgörande för att skapa förtroende att känna till utan tvivel vilka aktörer, enheter och komponenter deltar i värdeleverans via en IoT-lösning från slutpunkt till slutpunkt.  Kunskapen erbjuder säker redovisningen för deltagare att du har aktiverat grund för åtkomst.  Azure IoT Edge uppnår den här kunskapen via autentisering.  Den primära mekanismen för autentisering för Azure IoT Edge-plattformen är certifikatbaserad autentisering.  Den här mekanismen härleds från en uppsättning standarder för informationssäkerhet Public Key Infrastructure (PKiX) av Internet Engineering Task Force (IETF).     

Azure IoT Edge security framework-anrop för unikt certifikat identiteter för alla enheter, moduler (behållare som kapslar in logik i enheten) och aktörer interagera med Azure IoT Edge-enhet, oavsett om det är fysiskt eller via en nätverksanslutning.  Inte alla scenario eller komponenten vägs själva certifikatbaserad autentisering som utökningsbarhet för security framework erbjuder säker vägar för. 

## <a name="authorization"></a>Auktorisering

Möjligheten att delegera behörighet och kontrollera åtkomsten är avgörande för att uppnå en grundläggande säkerhetsprincipen – principen om lägsta behörighet.  Enheter, moduler och aktörer kan få åtkomst till resurser och data inom behörighetsomfånget för deras och endast när det är arkitektoniskt tillåten.  Det innebär att vissa behörigheter kan konfigureras med tillräckliga privilegier och andra arkitektoniskt tillämpas.  Till exempel när en modul kan ha behörighet till Privilegierade konfiguration att initiera en anslutning till Azure IoT Hub, finns det ingen anledning varför en modul i en Azure IoT Edge-enhet ska komma åt läsningen av en modul i en annan Azure IoT Edge-enhet.  Därför skulle det senare arkitektoniskt förhindras. 

Andra för auktorisering inkluderar certifikat för tokensignering rättigheter och rollbaserad åtkomstkontroll (RBAC).  Utökningsbarhet för security framework tillåter införandet av andra mogen för auktorisering. 

## <a name="attestation"></a>Attestering

Attestering säkerställer integriteten hos software bits.  Det är viktigt för att upptäcka och förhindra av skadlig kod.  Azure IoT Edge security framework klassificerar attestering under tre huvudkategorier:

* Statisk attestering
* Runtime-attestering
* Programvara attestering

### <a name="static-attestation"></a>Statisk attestering

Statisk attestering är verifiering av integriteten hos alla programvara-bitar, inklusive operativsystem, alla körningar och konfigurationsinformation på enheten strömkälla.  Det kallas ofta för säker start.  Security framework för Azure IoT Edge-enheter sträcker sig till silicon leverantörer och införlivar säker förankrade maskinvarukapacitet att garantera statiska attestering processer. Här ingår säker start och säker inbyggd programvara uppgradera processer.  Arbeta i nära samarbete med silicon leverantörer eliminerar överflödiga firmware lager därigenom minimera hotet ytan. 

### <a name="runtime-attestation"></a>Runtime-attestering

När ett system har slutfört en verifierade startprocessen och är igång och körs, att väl utformad säkra system skulle identifiera försöker att mata in skadlig kod via system-portar och gränssnitt och vidta rätt motåtgärder.  Det är möjligt att mata in malcontent sätt än enhetens användargränssnitt som manipulering för intelligenta edge-enheter i fysiska förvaring av skadliga aktörer och sidokanal attacker.   Sådana malcontent som kan vara i form av skadlig kod eller obehöriga konfigurationsändringar, normalt inte skulle kunna identifieras av säker Start-process eftersom de inträffar efter att startprocessen.  Motåtgärder erbjuds eller används av enhetens maskinvara avsevärt bidrar till warding av sådana hot.  Security framework för Azure IoT Edge anropar explicit för tillägg för bekämpningen runtime hot.     

### <a name="software-attestation"></a>Programvara attestering

Alla felfritt system, inklusive intelligenta edge-system måste vara lämpar sig för korrigeringar och uppgraderingar.  Säkerhet är viktigt för processerna som uppdatering annars de kan vara möjliga hotvektorer.  Security framework för Azure IoT Edge-anrop för uppdateringar via mäts och signerad paket för att säkerställa integritet och autentisera källan för paketen.  Detta gäller alla operativsystem och program programvara bits. 

## <a name="hardware-root-of-trust"></a>Maskinvara förtroenderoten

För många distributioner av intelligent edge-enheter, särskilt de som har distribuerats på platser där potentiella skadliga aktörer har fysisk åtkomst till enheten, är säkerheten som erbjuds av enhetens maskinvara senaste defense för skydd.  Därför förankrade förtroende i förvanskningstålig enhet maskinvara som är mest viktigt för sådana distributioner.  Security framework för Azure IoT Edge innebär samarbete av säker silicon maskinvaruleverantörer att erbjuda olika varianter av maskinvara förtroenderoten för hantering av olika riskprofiler och distributionsscenarier. Dessa inkluderar användning av säker silicon följa vanliga protokoll säkerhetskrav som Trusted Platform Module (ISO/IEC 11889) och Trusted Computing Group's Device identifierare Composition Engine (DICE).  Dessa inkluderar även säker enklaven tekniker som TrustZones och programvara Guard tillägg (SGX). 

## <a name="certification"></a>Certifiering

Azure IoT Edge-ramverket innehåller certifieringskraven för att hjälpa kunder att fatta välgrundade beslut vid inköp Azure IoT Edge-enheter för deras distribution.  Grundläggande att dessa krav är certifieringar som hör till säkerhetsanspråk och certifieringar som rör verifiering av hur säkerhet.  Exempelvis skulle en säkerhet anspråk certifiering informera att IoT Edge-enheten använder säker maskinvara som är kända för att motstå Start-attacker. En certifiering för verifiering kunde meddela säker maskinvara implementerades korrekt för att erbjuda detta värde i enheten.  Med beaktande av principen för enkelhetens skull erbjuder ramverket visionen för att hålla arbetet med certifiering minimal.   

## <a name="extensibility"></a>Utökningsbarhet

Utökningsbarhet är en första klassens medborgare i Azure IoT Edge security framework.  Det står skäl att säkerhet bör utvecklas smidigt i lockstep till adressen nya scenarier med IoT-teknik som driver på olika typer av omvandlingar för företag.  Azure IoT Edge security framework börjar med en stabil grund som den bygger i utökningsbarhet i olika dimensioner som ska ingå: 

* Säkerhet för förstapartstjänster som Device Provisioning-tjänsten för Azure IoT Hub.
* Tjänster från tredje part som hanterade säkerhetstjänster för olika program branscher (som industriella eller hälsovård) eller teknik fokus (t.ex. säkerhetsövervakning i nät nätverk eller silicon maskinvara attestering services) via ett omfattande nätverk med partner.
* Äldre system att inkludera onlineåterställningspunkter med alternativa säkerhetsstrategier som använder säker teknik än certifikat för autentisering och Identitetshantering.
* Säker maskinvara för sömlös införandet av teknik för säker maskinvara och silicon partner bidrag.

Här är några exempel av dimensionerna för utökningsbarhet och Azure IoT Edge security-ramverket är avsett att vara säker från grunden för den här utökningsbarhet. 

I slutändan högsta framgång i skydd av en intelligent gräns som resultat av samarbetsfunktioner bidrag från en öppen community drivs av det gemensamma intresset på säkra IoT.  Dessa bidrag kan vara i form av säker tekniker eller tjänster.  Azure IoT Edge security framework erbjuder en stabil grund för säkerhet som kan utökas för maximal täckning som erbjuder samma nivå av förtroende och integriteten i en intelligent gräns som med Azure-molnet.  

## <a name="next-steps"></a>Nästa steg

Läs mer om hur Azure IoT Edge är [skydda en intelligent gräns](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
