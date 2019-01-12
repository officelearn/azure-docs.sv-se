---
title: Security framework - Azure IoT Edge | Microsoft Docs
description: Lär dig om säkerhet, autentisering och auktorisering standarder som användes för att utveckla Azure IoT Edge och bör övervägas när du utformar din lösning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: b174d7f9b4b8438687512a90dc7a65b5649f758a
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229902"
---
# <a name="security-standards-for-azure-iot-edge"></a>Säkerhetskrav för Azure IoT Edge

Flytta dina data och analyser till en intelligent gräns skapar risk scenarier som Azure IoT Edge är tänkt att lösa. IoT Edge-säkerhetsstandarder är avsedda att erbjuda flexibilitet för olika riskprofiler och distributionsscenarier men samtidigt erbjuda samma skydd som du förväntar dig från alla Azure-tjänster. 

Azure IoT Edge kan köras på olika maskinvara gör och modeller, stöder flera operativsystem och gäller för olika distributionsscenarier. Utvärdera risken för en distributionsscenariot beror på många överväganden inklusive lösning ägarskap, distributionens geografiska omfattning, känsliga data, sekretess, programmet lodräta och administrativa krav. I stället för att erbjuda konkreta lösningar för specifika scenarier, är IoT Edge en utökningsbar security framework baserat på väl förankrad principer som har utformats för att skala. 
 
Den här artikeln innehåller en översikt över IoT Edge security framework. Mer information finns i [skydda en intelligent gräns](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standarder

Standarder för enklare enkel granskning och genomförandet, som båda hallmarks säkerhet. En säkerhetslösning bör lånar ut sig själv till granskning under utvärderingen kan skapa förtroende och får inte vara ett gränsvärde till distribution. Utformning av framework att skydda Azure IoT Edge bygger på beprövade och beprövade security protokoll för kännedom om och återanvändning. 

## <a name="authentication"></a>Autentisering

När du distribuerar en IoT-lösning som du behöver veta att endast betrodda aktörer, enheter och komponenter har åtkomst till din lösning. Kunskapen erbjuder säker redovisningen för deltagare att du har aktiverat grund för åtkomst.  Azure IoT Edge uppnår den här kunskapen via autentisering.  Den primära mekanismen för autentisering för Azure IoT Edge-plattformen är certifikatbaserad autentisering.  Den här mekanismen härleds från en uppsättning standarder för informationssäkerhet Public Key Infrastructure (PKiX) av Internet Engineering Task Force (IETF).     

Alla enheter, moduler och aktörer interagera med Azure IoT Edge-enhet om fysiskt eller via en nätverksanslutning bör ha unika certifikat identiteter. Inte alla scenariot eller en komponent kan låna ut sig själv till certifikatbaserad autentisering. Utökningsbarhet för security framework erbjuder säker alternativ i dessa scenarier. 

## <a name="authorization"></a>Auktorisering

Principen om lägsta behörighet säger att användare och komponenterna i ett system bör har endast åtkomst till den minsta uppsättningen resurser och data som behövs för att utföra sina roller. Enheter, moduler och aktörer ska komma åt de resurser och data inom behörighetsomfånget för deras och endast när det är arkitektoniskt tillåten. Vissa behörigheter kan konfigureras med rätt behörighet och andra tillämpas arkitektoniskt.  Till exempel tillåtas en modul via Privilegierade konfiguration att initiera en anslutning till Azure IoT Hub. Det finns dock ingen anledning varför en modul i en Azure IoT Edge-enhet ska komma åt läsningen av en modul i en annan Azure IoT Edge-enhet.

Andra för auktorisering omfattar certifikatsignering rättigheter, rollbaserad åtkomstkontroll (RBAC) och andra mogen för auktorisering. 

## <a name="attestation"></a>Attestering

Attestering säkerställer integriteten hos software bits.  Det är viktigt för att upptäcka och förhindra av skadlig kod.  Azure IoT Edge security framework klassificerar attestering under tre huvudkategorier:

* Statisk attestering
* Runtime-attestering
* Programvara attestering

### <a name="static-attestation"></a>Statisk attestering

Statisk attestering verifieras integriteten för alla program på en enhet, inklusive operativsystemet, alla körningar och konfigurationsinformation på enheten strömkälla. Det kallas ofta för säker start. Security framework för Azure IoT Edge-enheter sträcker sig till tillverkare och införlivar säker maskinvarukapacitet som garanterar statiska attestering processer. Här ingår säker start och säker inbyggd programvara uppgradera.  Arbeta i nära samarbete med silicon leverantörer eliminerar överflödiga firmware lager, så minimerar threat ytan. 

### <a name="runtime-attestation"></a>Runtime-attestering

När ett system har slutfört en process för säker start och är igång, skulle väl utformad system identifiera försök att mata in skadlig kod och vidta rätt motåtgärder. Angrepp av skadlig kod kan rikta systemets portar och gränssnitt för att få åtkomst till systemet. Eller, om skadliga aktörer har fysisk åtkomst till en enhet som de kan manipulera själva enheten eller använda sidokanal attacker för att få åtkomst. Sådana malcontent som kan vara i form av skadlig kod eller obehöriga konfigurationsändringar, matas in efter startprocessen så statiska attestering inte identifiera den. Motåtgärder erbjuds eller används av enhetens maskinvara hjälper till att undanröja sådana hot.  Explicit anropar security framework för Azure IoT Edge för tillägg som bekämpa runtime hot.  

### <a name="software-attestation"></a>Programvara attestering

Alla felfritt system, inklusive intelligenta edge-system måste godkänna korrigeringar och uppgraderingar.  Säkerhet är viktigt för processer annars de kan vara möjliga hotvektorer.  Security framework för Azure IoT Edge-anrop för uppdateringar via mäts och signerad paket för att säkerställa integritet och autentisera källan för paketen.  Den här standarden gäller för alla operativsystem och program programvara bits. 

## <a name="hardware-root-of-trust"></a>Maskinvara förtroenderoten

För många intelligenta edge-enheter, särskilt enheter på platser där potentiella skadliga aktörer har fysisk åtkomst till enheten, är maskinvara enhetssäkerhet senaste defense för skydd. Förvanskningstålig enhet maskinvara är avgörande för sådana distributioner. Azure IoT Edge uppmanar säker silicon maskinvaruleverantörer som erbjuder olika varianter av maskinvara förtroenderoten för hantering av olika riskprofiler och distributionsscenarier. Maskinvara förtroende kan komma från vanliga protokoll säkerhetskrav som Trusted Platform Module (ISO/IEC 11889) och Trusted Computing Group's Device identifierare Composition Engine (DICE). Skydda enklaven tekniker som TrustZones- och programvara Guard tillägg (SGX) får maskinvara förtroende. 

## <a name="certification"></a>Certifiering

Azure IoT Edge-ramverket innehåller certifieringskraven för att hjälpa kunder att fatta välgrundade beslut vid inköp Azure IoT Edge-enheter för deras distribution.  Grundläggande att dessa krav är certifieringar som hör till säkerhetsanspråk och certifieringar som rör verifiering av hur säkerhet.  Exempelvis skulle en säkerhet anspråk certifiering informera att IoT Edge-enheten använder säker maskinvara som är kända för att motstå Start-attacker. En certifiering för verifiering kunde meddela säker maskinvara implementerades korrekt för att erbjuda detta värde i enheten.  Med beaktande av principen för enkelhetens skull försöker ramverket hålla arbetet med certifiering minimal.   

## <a name="extensibility"></a>Utökningsbarhet

Utökningsbarhet är en första klassens medborgare i Azure IoT Edge security framework.  Det står skäl att säkerhet bör utvecklas parallellt till adressen nya scenarier med IoT-teknik som driver på olika typer av omvandlingar för företag.  Azure IoT Edge security framework börjar med en stabil grund som den bygger i utökningsbarhet i olika dimensioner som ska ingå: 

* Säkerhet för förstapartstjänster som Device Provisioning-tjänsten för Azure IoT Hub.
* Tjänster från tredje part som hanterade säkerhetstjänster för olika program branscher (som industriella eller hälsovård) eller teknik fokus (t.ex. säkerhetsövervakning i nät nätverk eller silicon maskinvara attestering services) via ett omfattande nätverk med partner.
* Äldre system att inkludera onlineåterställningspunkter med alternativa säkerhetsstrategier som använder säker teknik än certifikat för autentisering och Identitetshantering.
* Säker maskinvara för införandet av teknik för säker maskinvara och silicon partner bidrag.

I slutändan högsta framgång i skydd av en intelligent gräns som resultat av samarbetsfunktioner bidrag från en öppen community drivs av det gemensamma intresset på säkra IoT.  Dessa bidrag kan vara i form av säker tekniker eller tjänster.  Azure IoT Edge security framework erbjuder en stabil grund för säkerhet som kan utökas för maximal täckning som erbjuder samma nivå av förtroende och integriteten i en intelligent gräns som med Azure-molnet.  

## <a name="next-steps"></a>Nästa steg

Läs mer om hur Azure IoT Edge är [skydda en intelligent gräns](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
