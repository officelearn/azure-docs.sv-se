---
title: Säkerhet för CloudSimple tjänster
description: Beskriver delat ansvar modeller för säkerheten i CloudSimple-tjänster
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/27/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5f3fe32e03a9a2bb0186854a83917f8918c6647
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358130"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple Säkerhetsöversikt

Den här artikeln innehåller en översikt över hur säkerhet implementeras på CloudSimple service, infrastruktur och datacenter.  Du lär dig mer om dataskydd och säkerhet, nätverkssäkerhet och hur säkerhetsproblem och korrigeringar hanteras.

## <a name="shared-responsibility"></a>Delat ansvar

Azure VMware-lösning genom att CloudSimple använder en modell för delat ansvar för säkerhet. Betrodd säkerhet i molnet uppnås via delade ansvaret för kunder och Microsoft en tjänstleverantör. Den här matrisen ansvar ger högre säkerhet och eliminerar enskilda felpunkter. 

## <a name="azure-infrastructure"></a>Azure-infrastrukturen 

Säkerhetsöverväganden för Azure-infrastrukturen inkludera platsen datacenter och utrustning. 

### <a name="datacenter-security"></a>Datacentersäkerhet 

Microsoft har ett hela divisionen tillägnad utforma, bygga och driva lokaler som stöd för Azure. Det här laget har investerat i underhålla den senaste fysisk säkerhet. Information om fysisk säkerhet finns i [Azure anläggningar, plats och fysisk säkerhet](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Utrustning plats

Den maskinvaruutrustning som bare metal som kör ditt privata moln finns i Azure-datacenter-platser.  Burar där är den utrustningen, kräver biometriska baserat tvåfaktorsautentisering att få åtkomst.

## <a name="dedicated-hardware"></a>Dedikerad maskinvara

Som en del av tjänsten CloudSimple kan få alla CloudSimple kunder dedikerade bare metal värdar med lokalt anslutna diskar som är fysiskt isolerade från andra maskinvara för innehavare. En ESXi-hypervisor med virtuellt SAN-nätverk körs på varje nod. Noderna hanteras via kunden dedikerade VMware vCenter och NSX. Dela inte maskinvara mellan klienter ger ett extra lager av isolering och säkerhetsskydd.

## <a name="data-security"></a>Datasäkerhet

Kunder behålla kontrollen och ägarskap för sina data. Data bevara kunddata ansvarar för kunden.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Dataskydd för vilande data och data i rörelse inom interna nätverk

Du kan använda virtuellt SAN-kryptering för data i vila i privat molnmiljö. virtuellt SAN-kryptering fungerar med VMware certifierade externa nyckelhanteringsservrar (KMS) i dina egna virtuella nätverk eller lokalt.  Du styr datakrypteringsnycklar själv. VSphere stöder kryptering av data över nätverket för alla vmkernel trafik (inklusive vMotion) för data i rörelse inom det privata molnet.

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Dataskydd för data som krävs för att navigera i offentliga nätverk

För att skydda data som går genom offentliga nätverk, kan du skapa IPsec och SSL VPN-tunnlar för dina privata moln. Vanliga krypteringsmetoder stöds, inklusive AES 128 byte och 256 byte. Data under överföring (inklusive autentisering, administrativ åtkomst och kunddata) krypteras med standard krypteringsmekanismer (SSH, TLS 1.2 och skydda RDP). Kommunikation som transporterar känslig information använder standard krypteringsmekanismer.

### <a name="secure-disposal"></a>Säker borttagning 

Om tjänsten CloudSimple upphör att gälla eller är avslutad, ansvarar du för att ta bort dina data eller. CloudSimple kommer att samarbeta med dig för att ta bort eller returnera alla kunddata som angavs i kundavtal, förutom i utsträckning som CloudSimple krävs enligt lag att behålla vissa eller alla personliga data. Om det behövs för att behålla alla personliga data, kommer CloudSimple Arkivera data och implementera rimliga åtgärder för att förhindra att kunddata vidare bearbetning.

### <a name="data-location"></a>Dataplats

När du konfigurerar dina privata moln, kan du välja Azure-regionen där de ska distribueras. Data för virtuell dator i VMware flyttas inte från det fysiska datacentret, såvida inte du utföra migrering av data eller data för säkerhetskopiering. Du kan också vara värd för arbetsbelastningar och lagra data i flera Azure-regioner, om det är lämpligt för dina behov.

Vilka data som finns i privata moln hyperkonvergerade noderna passerar inte platser utan särskild åtgärd av klientadministratören. Det är ditt ansvar att implementera dina arbetsbelastningar på ett sätt som med hög tillgänglighet.

### <a name="data-backups"></a>Säkerhetskopiering av data
CloudSimple inte säkerhetskopiera eller arkivera kunddata. CloudSimple utför regelbunden säkerhetskopiering av vCenter och NSX data för att ge hög tillgänglighet för hanteringsservrar. Före säkerhetskopieringen krypteras alla data i vCenter-källan med hjälp av VMware APIs. Krypterade data transporteras och lagras i Azure-blob. Krypteringsnycklar för säkerhetskopiering lagras i ett mycket säkert hanterade CloudSimple-valv som körs i det virtuella nätverket CloudSimple i Azure.

## <a name="network-security"></a>Nätverkssäkerhet

CloudSimple-lösningen är beroende av lager för nätverkssäkerhet.

### <a name="azure-edge-security"></a>Azure säkerhet

CloudSimple services bygger på den grundläggande nätverkssäkerhet som tillhandahålls av Azure. Azure tillämpar skydd på djupet teknik för identifiering och svar i tid för nätverksbaserade attacker som är associerade med avvikande inkommande eller utgående trafikmönster och distribuerade denial of service DDoS-attacker. Den här säkerhetskontroll gäller för privata molnmiljöer och den programvara för versionskontroll-plan som har utvecklats av CloudSimple.

### <a name="segmentation"></a>Segmentering

Tjänsten CloudSimple har logiskt separata Layer 2-nätverk som begränsar åtkomsten till ditt eget privata nätverk i miljön privat moln. Du kan skydda dina privata moln-nätverk med en brandvägg. CloudSimple Portal kan du definiera TT och NS kontroller nätverkstrafikregler för all nätverkstrafik, inklusive intra trafik för privat moln, privat kommunikation mellan molntrafiken, allmän trafik till Internet, och nätverkstrafik till lokala platser via IPsec VPN eller ExpressRoute-anslutning.

## <a name="vulnerability-and-patch-management"></a>Säkerhetsrisk som uppdateringshantering 

CloudSimple ansvarar för periodiska säkerhetskorrigeringar i hanterade VMware-programvara (ESXi, vCenter och NSX).

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

Kunder kan autentisera på sina Azure-konto (i Azure AD) med hjälp av Multi-Factor authentication eller enkel inloggning som önskade. Du kan starta CloudSimple Portal utan att skriva in autentiseringsuppgifter från Azure-portalen.

CloudSimple stöder valfri konfiguration av en identitetskälla för vCenter-privat moln. Du kan använda en [lokala identitetskälla](https://docs.azure.cloudsimple.com/set-vcenter-identity), en ny identitetskälla för det privata molnet eller [Azure AD](https://docs.azure.cloudsimple.com/azure-ad).

Som standard får kunder de privilegier som krävs för dagliga åtgärder på vCenter inom det privata molnet. Den här behörighetsnivån omfattar inte administrativ åtkomst till vCenter. Om administrativ åtkomst krävs tillfälligt, kan du [eskalera dina behörigheter](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) under en begränsad period medan du utför administrativa uppgifter.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en CloudSimple-tjänst på Azure](quickstart-create-cloudsimple-service.md)
* Lär dig hur du [skapa ett privat moln](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Lär dig hur du [konfigurera en privat molnmiljö](quickstart-create-private-cloud.md)
