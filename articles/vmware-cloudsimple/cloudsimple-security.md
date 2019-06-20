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
ms.openlocfilehash: db093f6aef4f1bdb28a96bf89b3013d359a7b796
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154119"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple Säkerhetsöversikt

Den här artikeln innehåller en översikt över hur säkerhet implementeras på Azure VMware-lösningen av CloudSimple service, infrastruktur och datacenter. Du lär dig mer om dataskydd och säkerhet, nätverkssäkerhet och hur säkerhetsproblem och korrigeringar hanteras.

## <a name="shared-responsibility"></a>Delat ansvar

Azure VMware-lösning genom att CloudSimple använder en modell för delat ansvar för säkerhet. Betrodd säkerhet i molnet uppnås via delade ansvaret för kunder och Microsoft en tjänstleverantör. Den här matrisen ansvar ger högre säkerhet och eliminerar enskilda felpunkter.

## <a name="azure-infrastructure"></a>Azure-infrastrukturen 

Säkerhetsöverväganden för Azure-infrastrukturen inkludera platsen datacenter och utrustning.

### <a name="datacenter-security"></a>Datacentersäkerhet 

Microsoft har ett hela divisionen tillägnad utforma, bygga och driva lokaler som har stöd för Azure. Det här laget har investerat i underhålla den senaste fysisk säkerhet. Mer information om fysisk säkerhet finns i [Azure anläggningar, plats och fysisk säkerhet](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Utrustning plats

Bare metal-maskinvara som kör dina privata moln finns i Azure-datacenter-platser. Biometrisk tvåfaktors-autentisering krävs för att få åtkomst till burar där den utrustningen finns.

## <a name="dedicated-hardware"></a>Dedikerad maskinvara

Som en del av tjänsten CloudSimple kan hämta alla CloudSimple kunder dedikerade bare metal-värdar med lokalt anslutna diskar som är fysiskt isolerade från andra maskinvara för innehavare. En ESXi-hypervisor med virtuellt SAN-nätverk körs på varje nod. Noderna hanteras via dedikerade kunden VMware vCenter och NSX. Dela inte maskinvara mellan klienter ger ett extra lager av isolering och säkerhetsskydd.

## <a name="data-security"></a>Datasäkerhet

Kunder behålla kontrollen och ägarskap för sina data. Data bevara kunddata ansvarar för kunden.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Dataskydd för vilande data och data i rörelse inom interna nätverk

Du kan använda virtuellt SAN-kryptering för data i vila i privat molnmiljö. virtuellt SAN-kryptering fungerar med VMware-certifierade externa nyckelhanteringsservrar (KMS) i dina egna virtuella nätverk eller lokalt. Du styr datakrypteringsnycklar själv. VSphere stöder kryptering av data över nätverket för alla VMkernel trafik, vilket innefattar vMotion trafik för data i rörelse inom det privata molnet.

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>Dataskydd för data som krävs för att navigera i offentliga nätverk

För att skydda data som går genom offentliga nätverk, kan du skapa IPsec och SSL VPN-tunnlar för dina privata moln. Vanliga krypteringsmetoder stöds, inklusive AES 128 byte och 256 byte. Data under överföringen, vilket innefattar autentisering, administrativ åtkomst och kundinformation, krypteras med standard krypteringsmekanismer, till exempel SSH, TLS 1.2 och säker RDP. Kommunikation som transporterar känslig information använder standard krypteringsmekanismer.

### <a name="secure-disposal"></a>Säker borttagning 

Om tjänsten CloudSimple upphör att gälla eller är avslutad, är du ansvarig för att ta bort dina data eller. CloudSimple samarbetar med dig för att ta bort eller returnera alla kunddata som angavs i kundavtal, förutom i utsträckning som CloudSimple krävs enligt lag att behålla vissa eller alla personliga data. Om det behövs för att behålla alla personliga data, CloudSimple arkiverar data och implementerar rimliga åtgärder för att förhindra att kunddata vidare bearbetning.

### <a name="data-location"></a>Dataplats

När du ställer in dina privata moln, väljer du den Azure-region där de har distribuerats. Data för virtuell dator i VMware flyttas inte från det fysiska datacentret, såvida inte du utföra migrering av data eller data för säkerhetskopiering. Du kan också vara värd för arbetsbelastningar och lagra data i flera Azure-regioner, om det är lämpligt för dina behov.

Vilka data som finns i privata moln hyperkonvergerat noder passerar inte platser utan särskild åtgärd av klientadministratören. Det är ditt ansvar att implementera dina arbetsbelastningar på ett sätt som med hög tillgänglighet.

### <a name="data-backups"></a>Säkerhetskopiering av data
CloudSimple inte säkerhetskopiera eller arkivera kunddata. CloudSimple utför regelbunden säkerhetskopiering av vCenter och NSX data för att ge hög tillgänglighet för hanteringsservrar. Före säkerhetskopieringen krypteras alla data i vCenter-källan med hjälp av VMware APIs. Krypterade data transporteras och lagras i en Azure-blob. Krypteringsnycklarna för säkerhetskopior lagras i ett mycket säkert CloudSimple hanterade valv som körs i det virtuella nätverket CloudSimple i Azure.

## <a name="network-security"></a>Nätverkssäkerhet

CloudSimple-lösningen är beroende av lager för nätverkssäkerhet.

### <a name="azure-edge-security"></a>Azure säkerhet

CloudSimple services bygger på den grundläggande nätverkssäkerhet som tillhandahålls av Azure. Azure tillämpar skydd på djupet teknik för identifiering och svar i tid för nätverksbaserade attacker som är associerade med avvikande inkommande eller utgående trafikmönster och distribuerade denial of service DDoS-attacker. Den här säkerhetskontroll gäller för privata molnmiljöer och den programvara för versionskontroll-plan som har utvecklats av CloudSimple.

### <a name="segmentation"></a>Segmentering

Tjänsten CloudSimple har logiskt separata Layer 2-nätverk som begränsar åtkomsten till ditt eget privata nätverk i din privata molnmiljö. Du kan ytterligare skydda dina privata moln-nätverk med hjälp av en brandvägg. I portalen CloudSimple definierar du öst-väst och Nord-sydlig kontroll nätverkstrafikregler för all nätverkstrafik, vilket inkluderar trafik optimeras trafik, trafik mellan privata moln, allmän trafik till Internet och nätverkstrafik till den lokala via IPsec VPN eller Azure ExpressRoute-anslutning.

## <a name="vulnerability-and-patch-management"></a>Hantering av sårbarhet och korrigering 

CloudSimple ansvarar för periodiska säkerhetskorrigeringar i hanterade VMware-programvara, till exempel ESXi och vCenter NSX.

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

Kunder kan autentisera på sina Azure-konto (i Azure Active Directory) med hjälp av multifaktorautentisering eller enkel inloggning som önskade. Du kan starta CloudSimple portal utan att skriva in autentiseringsuppgifter från Azure-portalen.

CloudSimple stöder valfri konfiguration av en identitetskälla för vCenter-privat moln. Du kan använda en [lokala identitetskälla](https://docs.azure.cloudsimple.com/set-vcenter-identity), en ny identitetskälla för det privata molnet eller [Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad).

Som standard får kunder de privilegier som krävs för dagliga åtgärder på vCenter inom det privata molnet. Den här behörighetsnivån omfattar inte administrativ åtkomst till vCenter. Om administrativ åtkomst krävs tillfälligt, kan du [eskalera dina behörigheter](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) under en begränsad period medan du utför administrativa uppgifter.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en CloudSimple-tjänst på Azure](quickstart-create-cloudsimple-service.md).
* Lär dig hur du [skapa ett privat moln](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Lär dig hur du [konfigurera en privat molnmiljö](quickstart-create-private-cloud.md).
