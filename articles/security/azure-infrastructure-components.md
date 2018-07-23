---
title: Azure information systemkomponenter och gränser
description: Den här artikeln innehåller en allmän beskrivning av Microsoft Azure-arkitektur och hantering.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: b2e8ef232e1b25c7d000f4683830ff2e188047fb
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186484"
---
# <a name="azure-information-system-components-and-boundaries"></a>Azure information systemkomponenter och gränser
Den här artikeln innehåller en allmän beskrivning av Azure-arkitektur och hantering. Azure systemmiljön består av följande nätverk:

- Microsoft Azure-produktionsnätverket (Azure network)
- Microsoft företagsnätverket (corpnet)

Separata IT-team ansvarar för drift och underhåll av dessa nätverk.

## <a name="azure-architecture"></a>Azure-arkitektur
Azure är en molnbaserad databehandling plattform och infrastruktur för att skapa, distribuera, och hantera program och tjänster via ett nätverk av datacenter. Microsoft hanterar dessa datacenter. Baserat på antalet resurser som du anger, skapar Azure virtuella datorer (VM) baserade på behovet av resursen. Dessa virtuella datorer som körs på en Azure-hypervisorn, som är avsett för använder i molnet och är inte tillgänglig för allmänheten.

På varje nod i Azure fysisk server finns det ett hypervisor-program som körs direkt via maskinvaran. Hypervisor-programmet delar en nod i ett variabelt antal virtuella gästdatorer. Varje nod har också en rot-dator som kör värdoperativsystemet. Windows-brandväggen är aktiverad på varje virtuell dator. Du definierar vilka portar som är adresserbara genom att konfigurera tjänstdefinitionsfilen. De här portarna är enda öppen och adresserbara, internt eller externt. All trafik och åtkomst till disken och nätverket är genom av hypervisor-programmet och rot-operativsystemet.

På värd-nivå, Azure virtuella datorer som kör en anpassad och strikt version av den senaste Windows-Server. Azure använder en version av Windows Server som innehåller endast de komponenterna som behövs för att värden virtuella datorer. Detta förbättrar prestanda och minskar risken för angrepp. Datorer tillämpas av hypervisor som inte är beroende av operativsystemets säkerhet.

### <a name="azure-management-by-fabric-controllers"></a>Azure-hantering av infrastrukturkontrollanter

I Azure grupperas virtuella datorer som körs på fysiska servrar (blad/noder) i kluster på ungefär 1 000. De virtuella datorerna hanteras oberoende av en plattform som skalats ut och redundant programvarukomponent som kallas infrastrukturkontrollanten (FC).

Varje FC hanterar livscykeln för program som körs i dess kluster och etablerar och övervakar hälsan för maskinvara under dess kontroll. Körs den autonom åtgärder, till exempel reincarnating VM-instanser på felfria servrar när den bestämmer att en server har misslyckats. FC utför även åtgärder för hantering av program, till exempel distribuera, uppdatera och skala ut program.

Datacentret är uppdelad i kluster. Kluster isolera fel på FC-nivå och förhindra att vissa klasser av fel påverkar servrar utanför klustret där de inträffar. FCs som har en viss Azure-kluster grupperas i ett FC-kluster.

### <a name="hardware-inventory"></a>Maskinvaruinventering

FC förbereder en förteckning över Azure maskinvaru- och -enheter under bootstrap konfigurationsprocessen. Ny maskinvara och nätverkskomponenter att ange Azure produktionsmiljön måste följa bootstrap konfigurationsprocessen. FC ansvarar för att hantera hela lagret som anges i konfigurationsfilen datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>FC-hanterade operativsystemavbildningar

Operativsystem-teamet innehåller bilder, i form av virtuella hårddiskar, distribueras på alla värdens och gästens virtuella datorer i Azure produktionsmiljön. Teamet skapar dessa Källavbildningen genom en automatisk offline build-process. Basavbildningen är en version av operativsystemet som kerneln och andra kärnkomponenter har ändrats och optimeras för att stödja Azure-miljön.

Det finns tre typer av fabric-hanterade operativsystemavbildningar:

- Värden: Ett anpassat operativsystem som körs på värd virtuella datorer.
- Inbyggd: Ett operativsystem som körs på klienter (till exempel Azure Storage). Det här operativsystemet har inte någon hypervisor-programmet.
- Gäst: Ett gästoperativsystem som körs på virtuella gästdatorer.

Värden och den inbyggda FC-hanterade operativsystem är avsedd för användning i molnet och är inte tillgängliga för allmänheten.

#### <a name="host-and-native-operating-systems"></a>Värden och inbyggda operativsystem

Värden och intern är strikt operativsystemavbildningar som värdar för fabric-agenter och köras på en beräkningsnod (körs som första virtuella datorn på noden) och lagringsnoder. Fördelen med att använda optimerad Källavbildningen på värddatorn och intern är att minska ytan som exponeras av API: er eller oanvända komponenter. Dessa kan innebära en säkerhetsrisk för hög och öka utrymmet för operativsystemet. Fotavtryck operativsystem innehåller endast komponenterna som krävs till Azure.

#### <a name="guest-operating-system"></a>Gästoperativsystem

Azure-interna komponenter som körs på virtuella datorer i system för gästoperativsystem har ingen möjlighet att köra Remote Desktop Protocol. Ändringar av inställningar för baslinje måste gå igenom ändringen och släpp hanteringen.

## <a name="azure-datacenters"></a>Azure-datacenter
Microsoft-Molninfrastruktur och åtgärder (MCIO) team hanterar lokaler för infrastruktur- och datacenter för alla Microsoft online services. MCIO är primärt ansvarig för att hantera fysiskt och miljömässigt kontroller inom datacentren, samt hantera och stöd för yttre perimeter nätverksenheter (till exempel edge-routrar och datacenter-routrar). MCIO ansvarar också för att konfigurera utan minsta servermaskinvaran på rack i datacentret. Kunder har utan direkt interaktion med Azure.

## <a name="service-management-and-service-teams"></a>Service management och service-team
Olika tekniker grupper som kallas serviceteam, hantera stöd för Azure-tjänsten. Varje tjänst är ansvariga för en del av support för Azure. Varje tjänst-team måste göra en tillgängliga 24 x 7-tekniker kan undersöka och åtgärda fel i tjänsten. Service-team har, som standard inte fysisk åtkomst till maskinvara som är verksamma i Azure.

Service-team är:

- Plattform för program
- Azure Active Directory
- Azure Compute
- Azure Net
- Tekniker för molntjänster
- ISSD: säkerhet
- Multifactor Authentication
- SQL Database
- Storage

## <a name="types-of-users"></a>Typer av användare
Anställda (eller underleverantörer) av Microsoft anses vara interna användare. Alla andra användare anses vara externa användare. Alla Azure-interna användare ha sina anställningsstatus kategoriseras på en annan nivå som definierar deras åtkomst till kunddata (åtkomst eller ingen åtkomst). Användarbehörigheter till Azure (auktorisering behörighet efter autentisering sker) beskrivs i följande tabell:

| Roll | Interna eller externa | Känslighetsnivå | Auktoriserade privilegier och funktionerna som utförs | Åtkomsttyp
| --- | --- | --- | --- | --- |
| Azure-datacenter-tekniker | Intern | Ingen åtkomst till kunddata | Hantera den fysiska säkerheten för en lokal plats. Genomför patruller in och ut ur datacentret och övervaka alla startpunkter. Eskort in och ut ur datacenter vissa icke-avmarkerad personal som tillhandahåller allmänna tjänster (till exempel matsal eller rensning) eller IT-arbetet inom datacentret. Genomför rutinmässig övervakning och underhåll av maskinvaruenheterna i nätverket. Utföra incident hanterings- och reparation arbete med hjälp av en rad olika verktyg. Genomför rutinmässig övervakning och underhåll av den fysiska maskinvaran i datacenter. Åtkomst till miljö på begäran från egenskapen ägare. Kapabel att utföra kriminaltekniska undersökningar, logga incidentrapporter och kräver obligatorisk säkerhetskrav för utbildning och principen. Operativa ägarskap och underhåll av kritiska säkerhetsverktyg, till exempel skannrar och loggar in. | Beständig åtkomst till miljön. |
| Azure incident prioritering (snabba svar tekniker) | Intern | Åtkomst till kunddata | Hantera kommunikationen mellan MCIO, support och tekniska team. Sortera plattform incidenter, problem med distribution och tjänstbegäranden. | Just-in-time-åtkomst till miljön, med begränsad permanent åtkomst till icke-kundsystem. |
| Azure-distribution-tekniker | Intern | Åtkomst till kunddata | Distribuera och uppgradera Plattformskomponenter, programvara och schemalagda konfigurationsändringar till stöd för Azure. | Just-in-time-åtkomst till miljön, med begränsad permanent åtkomst till icke-kundsystem. |
| Stöd för Azure-kund avbrott (klient) | Intern | Åtkomst till kunddata | Felsök och diagnostisera plattform avbrott och fel för enskilda beräkningsnoder klienter och Azure-konton. Analysera fel. Öka kritiska korrigeringar till plattform eller kund och Driv tekniska förbättringar över support. | Just-in-time-åtkomst till miljön, med begränsad permanent åtkomst till icke-kundsystem. |
| Live-webbplatsen Azure-tekniker (övervakning tekniker) och incident | Intern | Åtkomst till kunddata | Diagnostisera och förebygga plattform hälsa genom att använda diagnostiska verktyg. Driv korrigeringar av volymdrivrutiner, reparera objekt till följd av avbrott och hjälpa åtgärder för återställning av avbrott. | Just-in-time-åtkomst till miljön, med begränsad permanent åtkomst till icke-kundsystem. |
|Azure-kunder | Extern | Gäller inte | Saknas | Gäller inte |

Azure använder unika identifierare för att autentisera organisationsanvändare och kunder (eller processer som fungerar för organisationens användare). Detta gäller för alla tillgångar och enheter som ingår i Azure-miljön.

### <a name="azure-internal-authentication"></a>Azure intern autentisering

Kommunikation mellan Azure interna komponenter är skyddade med TLS-kryptering. I de flesta fall X.509-certifikat som är självsignerat. Certifikat med anslutningar som kan nås från utanför Azure-nätverket är ett undantag som avser FCs certifikat. FCs har certifikat som utfärdats av en Microsoft Certificate för den certifikatutfärdare (CA) som backas upp av en betrodd rotcertifikatutfärdare. På så sätt kan FC offentliga nycklar som ska distribueras. Dessutom använder Microsofts utvecklingsverktyg FC offentliga nycklar. När utvecklare skickar ny programavbildningar, krypterade bilderna med en offentlig nyckel för FC för att kunna skydda embedded hemligheter.

### <a name="azure-hardware-device-authentication"></a>Azure-maskinvaran enhetsautentisering

FC upprätthåller en uppsättning autentiseringsuppgifter (nycklar och/eller lösenord) som används för att autentisera sig till olika maskinvaruenheter under dess kontroll. Microsoft använder ett system för att förhindra åtkomst till dessa autentiseringsuppgifter. Mer specifikt är transport, persistence och användning av dessa autentiseringsuppgifter utformad för att förhindra att Azure-utvecklare och administratörer säkerhetskopiering tjänster och personal åtkomst till känsliga, konfidentiell eller privat information.

Microsoft använder kryptering som baseras på den FC master identitet offentlig nyckel. Detta inträffar vid FC-installation och FC omkonfiguration gånger för att överföra de autentiseringsuppgifter som används för att komma åt nätverk maskinvaruenheter. När FC måste autentiseringsuppgifterna, hämtar FC och dekrypterar dem.

### <a name="network-devices"></a>Nätverksenheter

Azure-nätverk teamet konfigurerar network service-konton om du vill aktivera en Azure-klienten att autentisera till nätverksenheter (routrar, växlar och belastningsutjämnare).

## <a name="secure-service-administration"></a>Skydda tjänstadministration
Azure-åtgärder personal måste använda säkra administrativa arbetsstationer (saw). Kunder kan ha liknande kontroller med hjälp av arbetsstationer för privilegierad åtkomst. Med saw använda administrativ personal ett individuellt tilldelat administrativt konto som är separat från användarens konto. SAW bygger på konto tillämpas denna praxis genom att tillhandahålla en säker arbetsstation för sådana känsliga kontona.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
- [Dataskydd för Azure-kund](azure-protection-of-customer-data.md)
