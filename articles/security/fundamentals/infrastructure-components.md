---
title: Komponenter och gränser för Azure-informationssystem
description: Den här artikeln innehåller en allmän beskrivning av Microsoft Azure-arkitekturen och hanteringen.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727198"
---
# <a name="azure-information-system-components-and-boundaries"></a>Komponenter och gränser för Azure-informationssystem
Den här artikeln innehåller en allmän beskrivning av Azure-arkitekturen och hanteringen. Azure-systemmiljön består av följande nätverk:

- Microsoft Azure-produktionsnätverk (Azure-nätverk)
- Microsofts företagsnätverk (corpnet)

Separata IT-team ansvarar för drift och underhåll av dessa nätverk.

## <a name="azure-architecture"></a>Azure-arkitektur
Azure är en molnbaserad datorplattform och infrastruktur för att skapa, distribuera och hantera program och tjänster via ett nätverk av datacenter. Microsoft hanterar dessa datacenter. Baserat på antalet resurser som du anger skapar Azure virtuella datorer (VMs) baserat på resursbehov. Dessa virtuella datorer körs på en Azure hypervisor, som är avsedd för användning i molnet och inte är tillgänglig för allmänheten.

På varje Azure fysisk servernod finns det en hypervisor som körs direkt över maskinvaran. Hypervisorn delar upp en nod i ett variabelt antal virtuella gäst-datorer. Varje nod har också en rot-VM, som kör värdoperativsystemet. Windows-brandväggen är aktiverad på varje virtuell dator. Du definierar vilka portar som är adresserbara genom att konfigurera tjänstdefinitionsfilen. Dessa portar är de enda som är öppna och adresserbara, internt eller externt. All trafik och åtkomst till disken och nätverket medieras av hypervisor- och rotoperativsystemet.

På värdlagret kör virtuella Azure-datorer en anpassad och härdade version av den senaste Windows Server. Azure använder en version av Windows Server som endast innehåller de komponenter som krävs för att vara värd för virtuella datorer. Detta förbättrar prestanda och minskar attackytan. Maskingränser tillämpas av hypervisorn, vilket inte beror på operativsystemets säkerhet.

### <a name="azure-management-by-fabric-controllers"></a>Azure-hantering av infrastrukturstyrenheter

I Azure grupperas virtuella datorer som körs på fysiska servrar (blad/noder) i kluster på cirka 1 000. De virtuella datorerna hanteras oberoende av en skalad och redundant plattformsprogramkomponent som kallas fabric controller (FC).

Varje FC hanterar livscykeln för program som körs i klustret och avsättningar och övervakar maskinvarans hälsotillstånd under dess kontroll. Den körs autonoma åtgärder, till exempel reinkarnera VM-instanser på felfria servrar när den fastställer att en server har misslyckats. FC utför också programhanteringsåtgärder, till exempel distribuera, uppdatera och skala ut program.

Datacentret är indelat i kluster. Kluster isolerar fel på FC-nivå och förhindrar att vissa felklasser påverkar servrar utanför klustret där de uppstår. FCs som betjänar ett visst Azure-kluster är grupperade i ett FC-kluster.

### <a name="hardware-inventory"></a>Maskinvaruinventering

FC förbereder en inventering av Azure-maskinvara och nätverksenheter under konfigurationsprocessen för bootstrap. Alla nya maskinvaru- och nätverkskomponenter som kommer in i Azure-produktionsmiljön måste följa konfigurationsprocessen för bootstrap. FC ansvarar för att hantera hela lagret som anges i konfigurationsfilen datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>FC-hanterade operativsystemavbildningar

Operativsystemets team tillhandahåller avbildningar, i form av virtuella hårddiskar, som distribueras på alla värd- och gäst-virtuella datorer i Azure-produktionsmiljön. Teamet konstruerar dessa basavbildningar genom en automatiserad offlinebyggprocess. Grundavbildningen är en version av operativsystemet där kärnan och andra kärnkomponenter har ändrats och optimerats för att stödja Azure-miljön.

Det finns tre typer av tyghanterade operativsystemavbildningar:

- Värd: Ett anpassat operativsystem som körs på virtuella värd-datorer.
- Inbyggt: Ett inbyggt operativsystem som körs på klienter (till exempel Azure Storage). Det här operativsystemet har ingen hypervisor.
- Gäst: Ett gästoperativsystem som körs på gäst-virtuella datorer.

Värden och de inbyggda FC-hanterade operativsystemen är utformade för användning i molnet och är inte allmänt tillgängliga.

#### <a name="host-and-native-operating-systems"></a>Värd- och inbyggda operativsystem

Värdvärden och inbyggda är härdade operativsystemavbildningar som är värdar för fabric-agenterna och körs på en beräkningsnod (körs som första virtuella datorer på noden) och lagringsnoder. Fördelen med att använda optimerade basavbildningar av värd och inbyggt är att det minskar den yta som exponeras av API:er eller oanvända komponenter. Dessa kan medföra höga säkerhetsrisker och öka operativsystemets fotavtryck. Operativsystem med reducerat fotavtryck innehåller endast de komponenter som krävs för Azure.

#### <a name="guest-operating-system"></a>Gästoperativsystem

Virtuella Azure-komponenter som körs på virtuella gästoperativsystem har ingen möjlighet att köra Fjärrskrivbordsprotokoll. Alla ändringar av konfigurationsinställningarna för baslinjen måste gå igenom hanteringsprocessen för ändring och frisläppning.

## <a name="azure-datacenters"></a>Azure-datacenter
McIO-teamet (Microsoft Cloud Infrastructure and Operations) hanterar de fysiska infrastruktur- och datacenterresurserna för alla Microsofts onlinetjänster. MCIO ansvarar främst för att hantera de fysiska och miljömässiga kontrollerna inom datacenter, samt hantera och stödja yttre perimeternätverksenheter (till exempel kantroutrar och datacenterroutrar). MCIO ansvarar också för att konfigurera minsta servermaskinvara på rack i datacentret. Kunder har ingen direkt interaktion med Azure.

## <a name="service-management-and-service-teams"></a>Servicehanterings- och serviceteam
Olika teknikgrupper, så kallade serviceteam, hanterar supporten för Azure-tjänsten. Varje serviceteam ansvarar för ett supportområde för Azure. Varje serviceteam måste göra en tekniker tillgänglig dygnet 24 x 7 för att undersöka och lösa fel i tjänsten. Serviceteam har som standard inte fysisk åtkomst till maskinvaran som fungerar i Azure.

Serviceteamen är:

- Plattform för program
- Azure Active Directory
- Azure Compute
- Azure-nät
- Molntekniska tjänster
- ISSD: Säkerhet
- Multifaktorautentisering
- SQL Database
- Lagring

## <a name="types-of-users"></a>Typer av användare
Anställda (eller entreprenörer) på Microsoft anses vara interna användare. Alla andra användare anses vara externa användare. Alla interna Azure-användare har sin medarbetarstatus kategoriserad med en känslighetsnivå som definierar deras åtkomst till kunddata (åtkomst eller ingen åtkomst). Användarbehörighet till Azure (auktoriseringsbehörighet efter autentisering) beskrivs i följande tabell:

| Roll | Interna eller externa | Känslighetsnivå | Behöriga privilegier och funktioner som utförs | Åtkomsttyp
| --- | --- | --- | --- | --- |
| Azure datacenteringenjör | Intern | Ingen åtkomst till kunddata | Hantera lokalens fysiska säkerhet. Utför patruller in och ut ur datacentret och övervaka alla startpunkter. Eskortera till och från datacentret viss icke-rensad personal som tillhandahåller allmänna tjänster (t.ex. matsal eller rengöring) eller IT-arbete i datacentret. Utför rutinmässig övervakning och underhåll av nätverksmaskinvara. Utför incidenthantering och break-fix-arbete med hjälp av en mängd olika verktyg. Utför rutinmässig övervakning och underhåll av den fysiska maskinvaran i datacenter. Tillgång till miljö på begäran från fastighetsägare. Kan utföra kriminaltekniska undersökningar, logga incidentrapporter och kräva obligatorisk säkerhetsutbildning och policykrav. Operativt ägande och underhåll av kritiska säkerhetsverktyg, till exempel skannrar och logginsamling. | Beständig åtkomst till miljön. |
| Azure incident triage (snabbinsatstekniker) | Intern | Tillgång till kunddata | Hantera kommunikation mellan MCIO, support och ingenjörsteam. Triage-plattformsincidenter, distributionsproblem och tjänstbegäranden. | Just-in-time tillgång till miljön, med begränsad beständig tillgång till icke-kundsystem. |
| Azure-distributionstekniker | Intern | Tillgång till kunddata | Distribuera och uppgradera plattformskomponenter, programvara och schemalagda konfigurationsändringar till stöd för Azure. | Just-in-time tillgång till miljön, med begränsad beständig tillgång till icke-kundsystem. |
| Azure-support för kundbrott (klient) | Intern | Tillgång till kunddata | Felsöka och diagnostisera avbrott och fel på plattformen för enskilda beräkningsklienter och Azure-konton. Analysera fel. Driva kritiska korrigeringar till plattformen eller kunden och driva tekniska förbättringar över supporten. | Just-in-time tillgång till miljön, med begränsad beständig tillgång till icke-kundsystem. |
| Azure live-platstekniker (övervakningstekniker) och incident | Intern | Tillgång till kunddata | Diagnostisera och minska plattformshälsan med hjälp av diagnostikverktyg. Korrigeringar av enheter för volymdrivrutiner, reparationsartiklar till följd av avbrott och hjälp till återställningsåtgärder för avbrott. | Just-in-time tillgång till miljön, med begränsad beständig tillgång till icke-kundsystem. |
|Azure-kunder | Extern | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |

Azure använder unika identifierare för att autentisera organisationsanvändare och kunder (eller processer som agerar på uppdrag av organisationsanvändare). Detta gäller alla tillgångar och enheter som ingår i Azure-miljön.

### <a name="azure-internal-authentication"></a>Intern azure-autentisering

Kommunikation mellan interna Azure-komponenter skyddas med TLS-kryptering. I de flesta fall är X.509-certifikaten självsignerade. Certifikat med anslutningar som kan nås från utanför Azure-nätverket är ett undantag, liksom certifikat för FCs. FCs har certifikat som utfärdats av ett Microsoft Certificate of Authority (CA) som backas upp av en betrodd rotcertifikatutfärdare. Detta gör att FC offentliga nycklar som ska rullas över lätt. Dessutom använder Microsofts utvecklarverktyg FC:s offentliga nycklar. När utvecklare skickar in nya programavbildningar krypteras bilderna med en offentlig FC-nyckel för att skydda inbäddade hemligheter.

### <a name="azure-hardware-device-authentication"></a>Autentisering av Azure-maskinvaran

FC har en uppsättning autentiseringsuppgifter (nycklar och/eller lösenord) som används för att autentisera sig till olika maskinvaruenheter under dess kontroll. Microsoft använder ett system för att förhindra åtkomst till dessa autentiseringsuppgifter. Specifikt är transport, persistens och användning av dessa autentiseringsuppgifter utformade för att förhindra Azure-utvecklare, administratörer och säkerhetskopieringstjänster och personalåtkomst till känslig, konfidentiell eller privat information.

Microsoft använder kryptering baserat på FC:s offentliga huvudidentitetsnyckel. Detta inträffar vid FC-inställnings- och FC-omkonfigurationstider för att överföra autentiseringsuppgifterna som används för att komma åt maskinvaruenheter för nätverk. När FC behöver autentiseringsuppgifterna hämtar och dekrypterar FC dem.

### <a name="network-devices"></a>Nätverksenheter

Azure-nätverksteamet konfigurerar nätverkstjänstkonton så att en Azure-klient autentiseras till nätverksenheter (routrar, växlar och belastningsutjämnare).

## <a name="secure-service-administration"></a>Säker tjänstadministration
Azure-driftpersonal krävs för att använda säkra administratörsarbetsstationer (SAWs). Kunder kan implementera liknande kontroller med hjälp av privilegierade åtkomstarbetsstationer. Med saws använder administrativ personal ett individuellt tilldelat administrativt konto som är skilt från användarens standardanvändarkonto. SAW bygger på detta konto separation praxis genom att tillhandahålla en pålitlig arbetsstation för dessa känsliga konton.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokaler och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Azure-nätverksarkitektur](infrastructure-network.md)
- [Azure-produktionsnätverk](production-network.md)
- [Säkerhetsfunktioner i Azure SQL Database](infrastructure-sql.md)
- [Azure-produktionsåtgärder och -hantering](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Azure-infrastrukturintegritet](infrastructure-integrity.md)
- [Azure-kunddataskydd](protection-customer-data.md)
