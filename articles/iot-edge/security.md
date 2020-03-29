---
title: Säkerhetsramverk – Azure IoT Edge | Microsoft-dokument
description: Lär dig mer om säkerhets-, autentiserings- och auktoriseringsstandarderna som användes för att utveckla Azure IoT Edge och bör betraktas som du utformar din lösning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3212493963805de3c8845ec494d87fc92d72998a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760121"
---
# <a name="security-standards-for-azure-iot-edge"></a>Säkerhetsstandarder för Azure IoT Edge

Azure IoT Edge tar itu med de risker som är inneboende när du flyttar dina data och analyser till den intelligenta kanten. IoT Edge-säkerhetsstandarderna balanserar flexibilitet för olika distributionsscenarier med det skydd som du förväntar dig av alla Azure-tjänster.

IoT Edge körs på olika märken och modeller av maskinvara, stöder flera operativsystem och gäller för olika distributionsscenarier. I stället för att erbjuda konkreta lösningar för specifika scenarier är IoT Edge ett utökningsbart säkerhetsramverk baserat på välgrundade principer som är utformade för skala. Risken för ett distributionsscenario beror på många faktorer, bland annat:

* Ägande av lösningar
* Distribution geografi
* Datakänslighet
* Sekretess
* Programmet vertikalt
* Myndighetskrav

Den här artikeln innehåller en översikt över säkerhetsramverket för IoT Edge. Mer information finns i [Säkra den intelligenta kanten](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standarder

Standarder främjar enkel granskning och enkel genomförande, som båda är kännetecken för säkerhet. En säkerhetslösning bör lämpar sig för granskning under utvärdering för att bygga förtroende och bör inte vara ett hinder för distribution. Utformningen av ramverket för att skydda Azure IoT Edge baseras på beprövade och branschbeprövade säkerhetsprotokoll för förtrogenhet och återanvändning.

## <a name="authentication"></a>Autentisering

När du distribuerar en IoT-lösning måste du veta att endast betrodda aktörer, enheter och moduler har åtkomst till din lösning. Certifikatbaserad autentisering är den primära mekanismen för autentisering för Azure IoT Edge-plattformen. Denna mekanism härleds från en uppsättning standarder som styr infrastruktur för offentliga nycklar (PKiX) av Internet Engineering Task Force (IETF).

Alla enheter, moduler och aktörer som interagerar med Azure IoT Edge-enheten bör ha unika certifikatidentiteter. Den här vägledningen gäller oavsett om interaktionerna är fysiska eller via en nätverksanslutning. Alla scenarier eller komponenter kan inte lämpar sig för certifikatbaserad autentisering, så säkerhetsramverkets utökningsbarhet erbjuder säkra alternativ.

Mer information finns i [Azure IoT Edge-certifikatanvändning](iot-edge-certs.md).

## <a name="authorization"></a>Auktorisering

Principen om lägsta behörighet säger att användare och komponenter i ett system endast ska ha åtkomst till den minsta uppsättning resurser och data som behövs för att utföra sina roller. Enheter, moduler och aktörer bör endast komma åt resurser och data inom deras behörighetsområde, och endast när det är arkitektoniskt tillåtet. Vissa behörigheter kan konfigureras med tillräckliga privilegier och andra tillämpas arkitektoniskt. Vissa moduler kan till exempel ha behörighet att ansluta till Azure IoT Hub. Det finns dock ingen anledning till varför en modul i en IoT Edge-enhet ska komma åt tvillingen till en modul i en annan IoT Edge-enhet.

Andra auktoriseringssystem omfattar certifikatsigneringsrättigheter och rollbaserad åtkomstkontroll (RBAC).

## <a name="attestation"></a>Intyg

Attestation säkerställer integriteten hos programvarubitar, vilket är viktigt för att upptäcka och förhindra skadlig kod. Azure IoT Edge-säkerhetsramverket klassificerar attestering under tre huvudkategorier:

* Statiskt intyg
* Körningsintyg
* Programintyg

### <a name="static-attestation"></a>Statiskt intyg

Statiskt attestation verifierar integriteten för all programvara på en enhet under uppladdning, inklusive operativsystem, alla körningar och konfigurationsinformation. Eftersom statiskt intyg inträffar under uppstart kallas det ofta säker start. Säkerhetsramverket för IoT Edge-enheter sträcker sig till tillverkare och innehåller säkra maskinvarufunktioner som säkerställer statiska attestationprocesser. Dessa processer inkluderar säker start och säker uppgradering av den inbyggda programvaran. Att arbeta i nära samarbete med kiselleverantörer eliminerar överflödiga firmwarelager, så minimerar hotytan.

### <a name="runtime-attestation"></a>Körningsintyg

När ett system har slutfört en säker startprocess, väl utformade system bör upptäcka försök att injicera skadlig kod och ta ordentlig motåtgärder. Malware attacker kan rikta systemets portar och gränssnitt. Om skadliga aktörer har fysisk åtkomst till en enhet kan de manipulera själva enheten eller använda sidokanalsattacker för att få åtkomst. Sådana missnöjda, oavsett om skadlig kod eller obehörig konfiguration ändras, kan inte identifieras av statiskt intyg eftersom det injiceras efter startprocessen. Motåtgärder som erbjuds eller upprätthålls av enhetens maskinvara hjälper till att avvärja sådana hot. Säkerhetsramverket för IoT Edge kräver uttryckligen tillägg som bekämpar körningshot.  

### <a name="software-attestation"></a>Programintyg

Alla hälsosamma system, inklusive intelligenta kantsystem, behöver patchar och uppgraderingar. Säkerhet är viktigt för uppdateringsprocesser, annars kan de vara potentiella hotvektorer. Säkerhetsramverket för IoT Edge kräver uppdateringar genom uppmätta och signerade paket för att säkerställa integriteten hos och autentisera paketens källa. Den här standarden gäller för alla operativsystem och programbitar.

## <a name="hardware-root-of-trust"></a>Maskinvarurot av förtroende

För många intelligenta kantenheter, särskilt enheter som fysiskt kan nås av potentiella skadliga aktörer, är maskinvarusäkerhet det sista skyddet för skydd. Tamper resistent hårdvara är avgörande för sådana distributioner. Azure IoT Edge uppmuntrar leverantörer av säker kiselmaskinvara att erbjuda olika smaker av maskinvarurot av förtroende för att hantera olika riskprofiler och distributionsscenarier. Maskinvaruförtroende kan komma från vanliga säkerhetsstandarder som Trusted Platform Module (ISO/IEC 11889) och Trusted Computing Groups device identifier composition engine (DICE). Säker enklavteknik som TrustZones och Software Guard Extensions (SGX) ger också maskinvaruförtroende.

## <a name="certification"></a>Certifiering

För att hjälpa kunder att fatta välgrundade beslut när de upphandlar Azure IoT Edge-enheter för distributionen innehåller IoT Edge-ramverket certifieringskrav. Grundläggande för dessa krav är certifieringar som hänför sig till säkerhetsanspråk och certifieringar som hänför sig till validering av säkerhetsimplementeringen. En säkerhetsanspråkscertifiering innebär till exempel att IoT Edge-enheten använder säker maskinvara som är känd för att motstå startattacker. En valideringscertifiering innebär att den säkra maskinvaran har implementerats korrekt för att erbjuda det här värdet i enheten. I enlighet med enkelhetsprincipen försöker ramen hålla certifieringsbördan minimal.

## <a name="extensibility"></a>Utökningsbarhet

Med IoT-teknik som driver olika typer av affärsomvandlingar bör säkerheten utvecklas parallellt för att hantera nya scenarier. Azure IoT Edge-säkerhetsramverket börjar med en solid grund på vilken den bygger i utökningsbarhet i olika dimensioner för att inkludera:

* Säkerhetstjänster från första part som enhetsetableringstjänsten för Azure IoT Hub.
* Tredjepartstjänster som hanterade säkerhetstjänster för olika applikationsvertikaseller (som industri eller hälso- och sjukvård) eller teknikfokus (som säkerhetsövervakning i nätnät eller kiselhårdvara som är Partner.
* Äldre system som inkluderar eftermontering med alternativa säkerhetsstrategier, som att använda annan säker teknik än certifikat för autentisering och identitetshantering.
* Säker maskinvara för införande av nya säkra maskinvarutekniker och bidrag från kiselpartner.

I slutändan kräver säkra den intelligenta kanten samarbetsbidrag från en öppen gemenskap som drivs av det gemensamma intresset av att säkra IoT. Dessa bidrag kan vara i form av säker teknik eller tjänster. Azure IoT Edge-säkerhetsramverket erbjuder en solid grund för säkerhet som är utökningsbar för maximal täckning för att erbjuda samma nivå av förtroende och integritet i den intelligenta kanten som med Azure-molnet.  

## <a name="next-steps"></a>Nästa steg

Läs mer om hur Azure IoT Edge [skyddar den intelligenta kanten](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
