---
title: Azure VMware-lösning från CloudSimple – Säkerhet för CloudSimple-tjänster
description: Beskriver modeller för delat ansvar för säkerhet för CloudSimple-tjänster
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a33e20ec540a05885eb13a3828d28ffc9923fff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025001"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple säkerhetsöversikt

Den här artikeln innehåller en översikt över hur säkerhet implementeras på Azure VMware-lösningen av CloudSimple-tjänsten, infrastrukturen och datacenter. Du lär dig mer om dataskydd och säkerhet, nätverkssäkerhet och hur sårbarheter och korrigeringar hanteras.

## <a name="shared-responsibility"></a>Delat ansvar

Azure VMware Solution by CloudSimple använder en modell med delat ansvar för säkerhet. Betrodd säkerhet i molnet uppnås genom delade ansvarsområden för kunder och Microsoft som tjänsteleverantör. Denna matris av ansvar ger högre säkerhet och eliminerar enskilda felpunkter.

## <a name="azure-infrastructure"></a>Azure-infrastruktur

Säkerhetsöverväganden för Azure-infrastruktur omfattar datacenter och utrustningsplats.

### <a name="datacenter-security"></a>Datacenter säkerhet

Microsoft har en hel avdelning som ägnar sig åt att utforma, bygga och driva de fysiska anläggningar som stöder Azure. Det här teamet är investerat i att upprätthålla den senaste fysiska säkerheten. Mer information om fysisk säkerhet finns i [Azure-anläggningar, lokaler och fysisk säkerhet](../security/azure-physical-security.md).

### <a name="equipment-location"></a>Placering av utrustning

Maskinvaruutrustningen för bare metal som kör dina privata moln finns på Azure datacenterplatser.  Burarna där utrustningen finns kräver biometrisk tvåfaktorsautentisering för att få åtkomst.

## <a name="dedicated-hardware"></a>Dedikerad maskinvara

Som en del av CloudSimple-tjänsten får alla CloudSimple-kunder dedikerade bare metal-värdar med lokala anslutna diskar som är fysiskt isolerade från annan klientmaskinvara. En ESXi hypervisor med vSAN körs på varje nod. Noderna hanteras via kundens dedikerade VMware vCenter och NSX. Att inte dela maskinvara mellan klienter ger ett extra lager av isolering och säkerhetsskydd.

## <a name="data-security"></a>Datasäkerhet

Kunderna behåller kontrollen och äganderätten till sina data. Datahantering av kunddata är kundens ansvar.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Dataskydd för data i vila och data i rörelse inom interna nätverk

För data i vila i den privata molnmiljön kan du använda vSAN-kryptering. vSAN-kryptering fungerar med VMware-certifierade externa nyckelhanteringsservrar (KMS) i ditt eget virtuella nätverk eller lokalt.  Du styr datakrypteringsnycklarna själv. För data i rörelse i det privata molnet stöder vSphere kryptering av data över kabeln för all vmkernel-trafik (inklusive vMotion-trafik).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Dataskydd för data som krävs för att gå via offentliga nätverk

För att skydda data som rör sig genom offentliga nätverk kan du skapa IPsec- och SSL VPN-tunnlar för dina privata moln. Vanliga krypteringsmetoder stöds, inklusive 128 byte och 256 byte AES. Data under överföring (inklusive autentisering, administrativ åtkomst och kunddata) krypteras med standardkrypteringsmekanismer (SSH, TLS 1.2 och Secure RDP). Kommunikation som transporterar känslig information använder standardkrypteringsmekanismerna.

### <a name="secure-disposal"></a>Säkert bortskaffande

Om din CloudSimple-tjänst går ut eller avslutas är du ansvarig för att ta bort eller ta bort dina data. CloudSimple samarbetar med dig för att ta bort eller returnera alla kunddata enligt kundavtalet, förutom i den utsträckning CloudSimple enligt tillämplig lag krävs för att behålla vissa eller alla personuppgifter. Om det är nödvändigt för att behålla personuppgifter arkiverar CloudSimple data och vidtar rimliga åtgärder för att förhindra att kunddata bearbetas vidare.

### <a name="data-location"></a>Dataplats

När du konfigurerar dina privata moln väljer du den Azure-region där de ska distribueras. Virtuella datordata för VMware flyttas inte från det fysiska datacentret om du inte utför datamigrering eller säkerhetskopiering av data utanför plats. Du kan också vara värd för arbetsbelastningar och lagra data inom flera Azure-regioner om det är lämpligt för dina behov.

Kunddata som finns i Hyperkonvergerade noder i privat moln går inte igenom platser utan den explicita åtgärden för klientadministratören. Det är ditt ansvar att implementera dina arbetsbelastningar på ett högtillgänge.

### <a name="data-backups"></a>Säkerhetskopior av data

CloudSimple säkerhetskopierar eller arkiverar inte kunddata. CloudSimple utför periodisk säkerhetskopiering av vCenter- och NSX-data för att ge hög tillgänglighet för hanteringsservrar. Före säkerhetskopiering krypteras alla data på vCenter-källan med hjälp av VMware API:er. De krypterade data transporteras och lagras i Azure blob. Krypteringsnycklar för säkerhetskopior lagras i ett mycket säkert CloudSimple-hanterat valv som körs i det virtuella CloudSimple-nätverket i Azure.

## <a name="network-security"></a>Nätverkssäkerhet

CloudSimple-lösningen är beroende av lager av nätverkssäkerhet.

### <a name="azure-edge-security"></a>Azure-kantsäkerhet

CloudSimple-tjänsterna bygger ovanpå den grundläggande nätverkssäkerhet som tillhandahålls av Azure. Azure tillämpar djupgående försvarstekniker för identifiering och snabb respons på nätverksbaserade attacker som är associerade med avvikande ingående eller utgående trafikmönster och ddos-attacker (Distributed Denial-of-Service). Den här säkerhetskontrollen gäller privata molnmiljöer och styrplanprogramvaran som utvecklats av CloudSimple.

### <a name="segmentation"></a>Segmentering

CloudSimple-tjänsten har logiskt separata Layer 2-nätverk som begränsar åtkomsten till dina egna privata nätverk i din privata molnmiljö. Du kan ytterligare skydda dina privata molnnätverk med hjälp av en brandvägg. Med CloudSimple-portalen kan du definiera regler för EW- och NS-nätverkstrafikkontroller för all nätverkstrafik, inklusive intra Private Cloud-trafik, inter-Private Cloud-trafik, allmän trafik till Internet och nätverkstrafik till lokalt via IPsec VPN eller ExpressRoute-anslutning.

## <a name="vulnerability-and-patch-management"></a>Sårbarhet och patchhantering

CloudSimple ansvarar för periodisk säkerhetskorrigering av hanterad VMware-programvara (ESXi, vCenter och NSX).

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

Kunder kan autentisera till sitt Azure-konto (i Azure AD) med multifaktorautentisering eller SSO som föredras. Från Azure-portalen kan du starta CloudSimple-portalen utan att ange autentiseringsuppgifter igen.

CloudSimple stöder valfri konfiguration av en identitetskälla för Private Cloud vCenter. Du kan använda en [lokal identitetskälla](set-vcenter-identity.md), en ny identitetskälla för det privata molnet eller [Azure AD](azure-ad.md).

Som standard får kunderna de privilegier som är nödvändiga för den dagliga driften av vCenter i det privata molnet. Den här behörighetsnivån innehåller inte administrativ åtkomst till vCenter. Om administratörsåtkomst krävs tillfälligt kan du [eskalera dina privilegier](escalate-private-cloud-privileges.md) under en begränsad period medan du slutför de administrativa uppgifterna.
