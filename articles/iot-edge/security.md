---
title: Security framework - Azure IoT Edge | Microsoft Docs
description: Lär dig om säkerhet, autentisering och auktorisering standarder som användes för att utveckla Azure IoT Edge och bör övervägas när du utformar din lösning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 37d5288389c7b602eb0d13a736e289010d7e0f80
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208202"
---
# <a name="security-standards-for-azure-iot-edge"></a>Säkerhetskrav för Azure IoT Edge

Azure IoT Edge åtgärdar riskerna som är när de flyttar dina data och analyser till den intelligenta kanten. Flexibiliteten i IoT Edge säkerhets standarden är flexibel för olika distributions scenarier med det skydd som du förväntar dig från alla Azure-tjänster. 

IoT Edge körs på olika modeller av maskin vara, stöder flera operativ system och gäller för olika distributions scenarier. Risken för ett distributions scenario är beroende av faktorer som innefattar lösnings ägande, distribution geografi, data känslighet, sekretess, program vertikal och regler. I stället för att erbjuda konkreta lösningar för vissa scenarier är IoT Edge ett utöknings Bart säkerhets ramverk som baseras på välgrundade principer som har utformats för skalning. 
 
Den här artikeln innehåller en översikt över IoT Edge Security Framework. Mer information finns i [skydda den intelligenta kanten](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standarder

Standarder främjar enkel granskning och enkel implementering, som båda är Hallmarks av säkerhet. En säkerhetslösning bör lånas ut till granskning under utvärderingen för att bygga förtroende och bör inte vara en Hurdle till distribution. Designen av ramverket för att skydda Azure IoT Edge baseras på tidstestade och bransch beprövade säkerhets protokoll för att få välbekanta och återanvända. 

## <a name="authentication"></a>Autentisering

När du distribuerar en IoT-lösning måste du veta att endast betrodda aktörer, enheter och moduler har åtkomst till din lösning. Certifikatbaserad autentisering är den primära mekanismen för autentisering för Azure IoT Edges plattformen. Den här mekanismen härleds från en uppsättning standarder som styr PKiX (Public Key Infrastructure) av IETF (Internet Engineering Task Force).     

Alla enheter, moduler och aktörer som interagerar med den Azure IoT Edge enheten, oavsett om de är fysiskt eller via en nätverks anslutning, bör ha unika certifikat identiteter. Det är inte alla scenarier eller komponenter som kan lånas ut till certifikatbaserad autentisering, så utökningen av säkerhets ramverket erbjuder säkra alternativ. 

Mer information finns i [Azure IoT Edge certifikat användning](iot-edge-certs.md).

## <a name="authorization"></a>Auktorisering

Principen om minsta behörighet innebär att användare och komponenter i ett system endast ska ha åtkomst till den minsta uppsättning resurser och data som krävs för att utföra sina roller. Enheter, moduler och aktörer bör endast komma åt resurser och data inom deras behörighets omfång, och endast när de är arkitektoniskt tillåtna. Vissa behörigheter kan konfigureras med tillräckliga behörigheter och andra är arkitektoniskt framtvingade.  Vissa moduler kan till exempel ha behörighet att ansluta till Azure IoT Hub. Det finns dock ingen anledning till varför en modul på en IoT Edge enhet ska ha åtkomst till en modul i en annan IoT Edge enhet.

Andra auktoriserings scheman inkluderar certifikat signerings rättigheter och rollbaserad åtkomst kontroll (RBAC). 

## <a name="attestation"></a>Attestering

Attestering säkerställer integriteten för program varu bitar, vilket är viktigt för att upptäcka och förhindra skadlig kod.  Azure IoT Edge Security Framework klassificerar attestering under tre huvud kategorier:

* Statisk attestering
* Runtime-attestering
* Programvara attestering

### <a name="static-attestation"></a>Statisk attestering

Med statisk attestering verifieras integriteten för all program vara på en enhet vid inaktive ring, inklusive operativ systemet, alla körningar och konfigurations information. Eftersom statisk attestering sker under uppstarten kallas den ofta för säker start. Säkerhets ramverket för IoT Edge enheter utökar till tillverkare och införlivar säkra maskin varu funktioner som garanterar statiska attesterings processer. I de här processerna ingår säker start och säker uppgradering av inbyggd program vara.  Att arbeta i nära samarbete med kisel leverantörer eliminerar överflödiga lager för inbyggd program vara, så minimerar hot ytan. 

### <a name="runtime-attestation"></a>Runtime-attestering

När ett system har slutfört en säker start process bör väl utformade system identifiera försök att injicera skadlig kod och ta rätt motåtgärder. Attacker mot skadlig kod kan rikta in sig på systemets portar och gränssnitt. Om skadliga aktörer har fysisk åtkomst till en enhet kan de manipulera själva enheten eller använda sido kanal attacker för att få åtkomst. Sådana Malcontent, om ändringar av skadlig kod eller obehörig konfiguration, inte kan identifieras av en statisk attestering eftersom den matas in efter start processen. Motåtgärder som erbjuds eller framtvingas av enhetens maskin varu hjälp för att stänga av sådana hot.  Säkerhets ramverket för IoT Edge uttryckligen anropa för tillägg som bekämpar körnings hot.  

### <a name="software-attestation"></a>Programvara attestering

Alla felfria system, inklusive intelligenta Edge-system, behöver uppdateringar och uppgraderingar.  Säkerhet är viktigt för uppdaterings processer, annars kan de vara potentiella hot vektorer.  Säkerhets ramverket för IoT Edge anropar uppdateringar genom uppmätta och signerade paket för att säkerställa integriteten hos och autentisera paketens källa.  Den här standarden gäller för alla operativ system och program varu bitar för program. 

## <a name="hardware-root-of-trust"></a>Maskinvara förtroenderoten

För många intelligenta gräns enheter, särskilt enheter som kan nås fysiskt av potentiella skadliga aktörer, är maskin varu säkerhet det senaste skyddet. Manipulering av manipulerad maskin vara är avgörande för sådana distributioner. Azure IoT Edge uppmuntrar säkra Silicon Hardware-leverantörer att erbjuda olika varianter för att hantera olika risk profiler och distributions scenarier. Maskin varu förtroende kan komma från vanliga standarder för säkerhets protokoll som Trusted Platform Module (ISO/IEC 11889) och Trusted Computing Groups sammansättnings motor (tärning). Säkra enklaven-tekniker som TrustZones och Software Guard-tillägg (SGX) tillhandahåller också maskin varu förtroende. 

## <a name="certification"></a>Certifiering

För att hjälpa kunder att fatta välgrundade beslut när de lägger Azure IoT Edge enheter för distributionen, innehåller IoT Edge ramverket certifierings krav.  Grundläggande för dessa krav är certifieringar som rör säkerhets anspråk och certifieringar som avser validering av säkerhets implementeringen.  En säkerhets anspråks certifiering innebär till exempel att den IoT Edge enheten använder säker maskin vara som är känd för att motstå start attacker. Ett verifierings certifikat innebär att den säkra maskin varan har implementerats korrekt för att erbjuda detta värde i enheten.  I och med principen för enkelhets skull försöker ramverket att hålla belastningen på certifieringen minimal.   

## <a name="extensibility"></a>Utökningsbarhet

Med IoT Technology som kör olika typer av affärs transformationer bör säkerheten utvecklas parallellt för att lösa nya scenarier.  Azure IoT Edge Security Framework börjar med en solid grund som bygger på utöknings barhet i olika dimensioner för att inkludera: 

* Säkerhet för förstapartstjänster som Device Provisioning-tjänsten för Azure IoT Hub.
* Tjänster från tredje part som hanterade säkerhets tjänster för olika program är lodräta (t. ex. industriella eller hälso vårds) eller teknik fokus (t. ex. säkerhets övervakning i nät verks nätverk eller kisel tjänster för maskin varu attestering) genom ett omfattande nätverk av partner.
* Äldre system att inkludera onlineåterställningspunkter med alternativa säkerhetsstrategier som använder säker teknik än certifikat för autentisering och Identitetshantering.
* Säker maskin vara för att införa nya säkra maskin varu tekniker och avgifter för Silicon-partner.

I slutet kräver att du skyddar den intelligenta gränsen från en öppen community som drivs av det gemensamma intresset att skydda IoT.  Dessa bidrag kan vara i form av säkra tekniker och tjänster.  Azure IoT Edge Security Framework erbjuder en solid grund för säkerhet som är utöknings bar för maximal täckning för att erbjuda samma nivå av förtroende och integritet i den intelligenta kanten som med Azure-molnet.  

## <a name="next-steps"></a>Nästa steg

Läs mer om hur Azure IoT Edge är [skydda en intelligent gräns](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
