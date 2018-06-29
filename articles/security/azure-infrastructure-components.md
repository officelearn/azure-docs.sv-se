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
ms.openlocfilehash: 8db1dce5fcc56c229d1fdd746bafbd2fae2c9bad
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102708"
---
# <a name="azure-information-system-components-and-boundaries"></a>Azure information systemkomponenter och gränser
Den här artikeln innehåller en allmän beskrivning av Microsoft Azure-arkitektur och hantering. Azure systemmiljön består av följande nätverk:

- Microsoft Azure-produktionsnätverket (Azure-nätverk)
- Microsoft Corporate nätverk (Corpnet)

Separata IT-team ansvarar för drift och underhåll av Azure-nätverk och CorpNet-nätverk.

## <a name="azure-architecture"></a>Azure-arkitektur
Microsoft Azure är en molntjänster plattform och infrastruktur för att skapa, distribuera, och hantera program och tjänster via ett nätverk med Microsoft-hanterade datacenter. Baserat på antalet resurser som anges av kunder, skapar Azure virtuella datorer baserat på resursen behov. Dessa virtuella datorer som kör på en Microsoft Azure Hypervisor som är avsett för använda i molnet och är inte tillgängligt för allmänheten.

På varje nod i Azure fysisk server finns en Hypervisor som körs direkt via maskinvaran. Hypervisor delar en nod i ett variabeln antal gäst virtuella maskiner (VMs). Varje nod har också en särskild ”rot” VM, som körs på värd-OS. Windows-brandväggen är aktiverad på varje virtuell dator. De enda portarna öppen och adresserbara, är internt eller externt, portar som uttryckligen definieras i tjänstedefinitionsfilen som konfigurerats av kunden. All trafik och åtkomst till disk och nätverk är genom av Hypervisor och rot-OS.

I lagret värden kör virtuella Azure-datorer en anpassad och förstärkt version av den senaste Windows Server. Microsoft Azure använder en avskalade version av Windows Server som innehåller bara komponenter som behövs för att värden virtuella datorer. Detta görs både för att förbättra prestanda och för att minska risken för angrepp. Datorn gränser tillämpas av Hypervisor som inte är beroende av operativsystemets säkerhet.

**Azure hanteringen av Infrastrukturkontrollanter (FCs)**: I Azure, virtuella datorer som körs på fysiska servrar (blad/noder) är grupperade i ”kluster” på 1000. De virtuella datorerna hanteras oberoende av en plattform som skalats ut och redundanta programvarukomponent som kallas FC.

Varje FC hanterar livscykeln för program som körs i klustret, och bestämmelser och övervakar hälsan för maskinvara under dess kontroll. Den kör både autonom åtgärder, till exempel reincarnating VM-instanser på felfri servrar när den bestämmer att en server misslyckades. FC genomför också hantering av program-åtgärder som till exempel distribuera, uppdatera och skala ut program.

Datacenter är uppdelat i kluster. Isolera fel på FC-nivå och förhindra att vissa typer av fel påverkar servrar utanför klustret där de förekommer. FCs som hanterar ett visst Azure kluster grupperas i ett FC-kluster.

**Inventering av maskinvara**: en förteckning över Azure maskinvara och nätverksenheter förbereddes under bootstrap konfigurationsprocessen och dokumenteras i konfigurationsfilen datacenter.xml. Ny maskinvara och nätverkskomponenter anger Azure produktionsmiljön måste följa bootstrap konfigurationsprocessen. FC ansvarar för att hantera hela lagret som anges i konfigurationsfilen datacenter.xml.

**FC-hanterade OS**: OS-teamet tillhandahåller operativsystemsavbildningar i form av virtuella hårddiskar (VHD) som har distribuerats på alla värd och Gäst-VM i Azure produktionsmiljön. OS-teamet skapar dessa ”Base bilder” via en automatisk offline build-process. Base-avbildning är en version av operativsystemet som kerneln och andra kärnkomponenter har ändrats och optimerats för att stödja Azure-miljön.

Det finns tre typer av Fabric-hanterade operativsystemsavbildningar:

- Värdoperativsystem – värdens operativsystem är ett anpassat operativsystem som körs på värd virtuella datorer
- Intern OS – intern OS som körs på klienter (till exempel Azure-lagring) som inte har någon hypervisor-programmet
- Gäst-OS-Gästoperativsystem som körs på gäst-VM

Värden och den interna FC-hanterade operativsystem är avsedd för användning i molnet och är inte tillgängligt för allmänheten.

**Värden och interna OS**: Värdoperativsystem och interna OS är förstärkt operativsystemsavbildningar som är värdar för Fabric-agenter (MI) och kör på en beräkningsnod (körs som första virtuella datorn på noden) och lagringsnoder. Fördelarna med att använda optimerad Base avbildningar för värd och interna OS är det minskar yta som exponeras av API: er eller oanvända komponenter som finns hög säkerhetsrisker och öka storleken på Operativsystemet. Dessa operativsystem storleken omfattar bara komponenterna som krävs till Azure. Detta förbättrar prestanda och minskar risken för angrepp.

**Gästoperativsystem**: Azure interna komponenter som körs på virtuella datorer gäst-OS har ingen möjlighet att köra Remote Desktop Protocol (RDP) till skillnad från externa kunder. Ändringar i grundläggande inställningar krävs för att gå igenom ändringen och släpp hanteringen.

## <a name="azure-datacenters"></a>Azure-datacenter
Microsoft-Molninfrastruktur och åtgärder (MCIO) team hanterar Microsofts infrastruktur och datacenter lokaler för alla Microsoft online services. MCIO är främst ansvarar för att hantera fysisk och miljösäkerhet kontroller i datacenter, samt hantera och stöd för yttre perimeter nätverksenheter (routrar i utkanten och Datacenter-routrar). MCIO ansvarar också för att konfigurera utan minsta servermaskinvaran på rack i datacentret. Kunder har ingen direkt interaktion med Azure.

## <a name="service-management--service-teams"></a>Service management & serviceteam
Stöd för Azure-tjänsten hanteras av ett antal tekniska grupper kallas Service-team. Var och en av de Service-team ansvarar för en del av stöd för Azure. Varje Service-Team måste göra en ingenjör tillgängliga 24 x 7 för att undersöka och lösa fel i tjänsten. Tjänsten team har, som standard inte tillgång till maskinvaran i Azure.

Service-team är:

- Plattform för program
- Azure Active Directory
- Azure Compute
- Azure Net
- Tekniker molntjänster
- ISSD: säkerhet
- Multifactor Authentication
- SQL Database
- Storage

## <a name="types-of-users"></a>Typer av användare
Alla Azure interna användare ha sina anställningsstatus kategoriseras på en annan nivå som definierar åtkomsten till kundinformation (åtkomst eller ingen åtkomst). Medarbetare (eller leverantörer) av Microsoft anses vara internanvändare. Externa användare betraktas som alla andra användare. I tabellen nedan beskrivs användarbehörigheter till Azure (auktorisering behörighet efter autentisering sker):

| Roll | Interna eller externa | Känslighetsnivån | Auktoriserade privilegier och funktionerna som utförs | Åtkomsttyp
| --- | --- | --- | --- | --- |
| Azure Datacenter-tekniker | Intern | Ingen åtkomst till kundinformation | Hantera den fysiska säkerheten för lokaler. Genomföra patruller till och från datacentret och övervaka alla startpunkter; Utföra eskort services in och ut från datacenter för vissa icke-avmarkerad-personal som tillhandahåller allmänna tjänster (matsal, rensa) eller IT arbetet inom datacentret; Utför rutinmässiga övervakning och underhåll av nätverksmaskinvara; Utföra incidenthantering och reparations arbete med hjälp av en mängd olika verktyg. Utför rutinmässiga övervakning och underhåll av den fysiska maskinvaran i Datacenter; Åtkomst till miljön på begäran från egenskapen ägare. Kan utföra kriminaltekniska undersökningar, loggning incident rapporten och kräver obligatorisk säkerhetsutbildning & principkrav. Använd ägarskap och underhåll av kritiska säkerhetsverktyg, till exempel skannrar och Logginsamling. | Beständig åtkomst till miljön |
| Microsoft Azure Incident prioritering (snabba svar Engineers) | Intern | Åtkomst till kundinformation | Hantera kommunikation mellan infrastruktur Operations, Support och Azure Engineering team; Prioritering plattform incidenter, distributionsproblem med och tjänstbegäranden. | Just-in-time-åtkomst för miljön - med begränsad beständig åtkomst till icke-kund system |
| Tekniker för distribution av Microsoft Azure | Intern | Åtkomst till kundinformation | Utför distributionen/uppgraderingar av platform-komponenter, programvara och schemalagda konfigurationsändringar stöd för Microsoft Azure. | Just-in-time-åtkomst för miljön - med begränsad beständig åtkomst till icke-kund system |
| Microsoft Azure kundsupport avbrott (klient) | Intern | Åtkomst till kundinformation | Felsöka och diagnostisera plattform avbrott och fel för enskilda beräknings-klienter och Microsoft Azure-konton; Analysera fel och enheten kritiska korrigeringar plattform/kunden enhet tekniska förbättringar över stöd. | Just-in-time-åtkomst för miljön - med begränsad beständig åtkomst till icke-kund system |
| Microsoft Azure Liveplatsen tekniker (övervakning Engineers) & Incident | Intern | Åtkomst till kundinformation | Ansvariga för att diagnostisera och minimera plattform hälsotillstånd med hjälp av diagnostikverktyg; Enheten korrigeringar för volymen drivrutiner, reparation objekt som uppstår till följd av avbrott och hjälpa avbrott återställningen åtgärder. | Just-in-time-åtkomst för miljön - med begränsad beständig åtkomst till icke-kund system |
|Microsoft Azure-kunder | Extern | Gäller inte | Saknas | Gäller inte |

Azure använder unika identifierare för att autentisera organisationsanvändare och kunder (eller processer som agerar på uppdrag av organisationens användare) för alla tillgångar/enheter som ingår i Azure-miljön.

**Intern autentisering för Microsoft Azure**: kommunikation mellan Azure interna komponenter är skyddade med TLS-kryptering. I de flesta fall är X.509-certifikat självsignerat. Undantag görs för certifikat med anslutningar som kan nås från utanför Azure-nätverket och FCs. FCs har certifikat utfärdade av en Microsoft Certificate för den certifikatutfärdare (CA) som backas upp av en betrodd rotcertifikatutfärdare. Detta gör att FC offentliga nycklar som ska återställas. Dessutom som FC offentliga nycklar används av Microsoft-utvecklingsverktyg så att när utvecklare skickar nya programavbildningar, de är krypterade med en offentlig nyckel för FC för att skydda alla inbäddade hemligheter.

**Microsoft Azure maskinvara Enhetsautentisering**: I FC finns en uppsättning autentiseringsuppgifter (nycklar och/eller lösenord) som används för att autentisera sig hos olika maskinvaruenheter under dess kontroll. System som används för transport, beständighet och användning av dessa autentiseringsuppgifter är utformat för att förhindra Azure utvecklare och administratörer säkerhetskopiering services/personal åtkomst till känsliga, konfidentiell eller privat information.

Kryptering som baseras på den FC master identitet offentliga nyckel som används på FC-installationen och FC omkonfiguration gånger för att överföra de autentiseringsuppgifter som används för att komma åt nätverk maskinvaruenheter. Autentiseringsuppgifter hämtas och dekrypteras av FC när det behövs.

**Nätverksenheter**: tjänstkonton för nätverket konfigureras med Azure-nätverk-teamet för att aktivera en Microsoft Azure-klient att autentisera till nätverksenheter (routrar, växlar och belastningsutjämnare).

## <a name="secure-service-administration"></a>Skydda tjänsten administration
Microsoft Azure operations personal krävs för att använda säkra administrativa arbetsstationer (sågar; kunder kan implementera liknande kontroller med hjälp av privilegierad åtkomst arbetsstationer eller PAWs). Metoden SETT är en utökning av väletablerade rekommenderas att använda separata admin och användarkonton för administrativa personal. Den här övningen använder ett enskilt tilldelade administrativa konto som är separat från användarens konto. SETT bygger på att kontot uppdelning praxis genom att tillhandahålla en säker arbetsstation för de känsliga kontona.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure verksamhet, lokal och fysisk säkerhet](azure-physical-security.md)
- [Tillgängligheten för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktion och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Integriteten hos Azure-infrastrukturen](azure-infrastructure-integrity.md)
- [Skydd av kundinformation i Azure](azure-protection-of-customer-data.md)
