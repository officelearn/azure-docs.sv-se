---
title: Säkerhet för CloudSimple-tjänster
description: Beskriver de delade ansvars modellerna för säkerhet för CloudSimple Services
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff1bd3c6e1f3cf98e92e10eecf972681ed6c7819
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816188"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple säkerhets översikt

Den här artikeln innehåller en översikt över hur säkerhet implementeras på Azure VMware-lösningen av CloudSimple-tjänst, infrastruktur och data Center. Du lär dig mer om data skydd och säkerhet, nätverks säkerhet och hur sårbarheter och korrigeringar hanteras.

## <a name="shared-responsibility"></a>Delat ansvar

Azure VMware-lösningen från CloudSimple använder en delad ansvars modell för säkerhet. Betrodd säkerhet i molnet uppnås genom de delade ansvars områdena hos kunder och Microsoft som en tjänst leverantör. Den här matrisen med ansvar ger högre säkerhet och eliminerar enskilda felpunkter.

## <a name="azure-infrastructure"></a>Azure-infrastruktur 

Säkerhets överväganden för Azure-infrastruktur omfattar data Center och utrustnings platsen.

### <a name="datacenter-security"></a>Data Center säkerhet 

Microsoft har en hel avdelning för att utforma, skapa och driva fysiska anläggningar som stöder Azure. Det här teamet investeras i att underhålla den avancerade fysiska säkerheten. Mer information om fysisk säkerhet finns i [Azure-anläggningar, lokal och fysisk säkerhet] (https://docs.microsoft.com/azure/security/fundamentals/physical-security.

### <a name="equipment-location"></a>Utrustnings plats

Maskin varu utrustningen utan operativ system som kör dina privata moln finns på Azure Data Center-platser. Bio metrisk-baserad tvåfaktorautentisering krävs för att få till gång till de burar där utrustningen finns.

## <a name="dedicated-hardware"></a>Dedikerad maskin vara

Som en del av CloudSimple-tjänsten får alla CloudSimple-kunder dedikerade Bare Metal-värdar med lokala anslutna diskar som är fysiskt isolerade från andra klient maskin vara. En ESXi-hypervisor med virtuellt San körs på varje nod. Noderna hanteras via kunddedikerade VMware, vCenter och NSX. Att inte dela maskin vara mellan klienter ger ett ytterligare lager av isolering och säkerhets skydd.

## <a name="data-security"></a>Datasäkerhet

Kunderna behåller sin data kontroll och ägande rätt. Kundernas data vård är kund informationens ansvar.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Data skydd för data i vila och data i rörelse i interna nätverk

För data i vila i den privata moln miljön kan du använda virtuellt San-kryptering. Virtuellt San-kryptering fungerar med VMware-certifierade externa nyckel hanterings servrar (KMS) i ditt eget virtuella nätverk eller lokalt. Du styr data krypterings nycklarna själv. För data i rörelse i det privata molnet stöder vSphere kryptering av data via kabeln för all VMkernel-trafik, som innehåller vMotion-trafik.

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>Data skydd för data som krävs för att flytta via offentliga nätverk

Om du vill skydda data som flyttas via offentliga nätverk kan du skapa IPsec-och SSL VPN-tunnlar för dina privata moln. Vanliga krypterings metoder stöds, inklusive 128-byte och 256-byte AES. Data under överföring, som omfattar autentisering, administrativ åtkomst och kund information, krypteras med standardmekanismer för kryptering, till exempel SSH, TLS 1,2 och Secure RDP. Kommunikation som transporterar känslig information använder standard krypterings metoder.

### <a name="secure-disposal"></a>Säker kasse ring 

Om din CloudSimple-tjänst går ut eller avslutas, är du ansvarig för att ta bort eller ta bort dina data. CloudSimple samverkar med att ta bort eller returnera alla kund uppgifter som anges i kund avtalet, med undantag för omfattningen CloudSimple krävs enligt gällande lag för att behålla vissa eller alla personliga uppgifter. Om det behövs för att spara personliga data, arkiverar CloudSimple data och implementerar rimliga åtgärder för att förhindra kunddata från all ytterligare bearbetning.

### <a name="data-location"></a>Dataplats

När du konfigurerar dina privata moln kan du välja den Azure-region där de distribueras. Data för virtuella VMware-datorer flyttas inte från det fysiska data centret om du inte utför datamigrering eller säkerhets kopiering på annan plats. Du kan också vara värd för arbets belastningar och lagra data i flera Azure-regioner om det behövs.

De kund uppgifter som är bosatta i privata molnets konvergerade noder går inte över platser utan den uttryckliga åtgärden hos klient organisationens administratör. Det är ditt ansvar att implementera dina arbets belastningar på ett mycket tillgängligt sätt.

### <a name="data-backups"></a>Data säkerhets kopieringar
CloudSimple säkerhets kopie ras eller arkiveras inte kund information. CloudSimple utför regelbunden säkerhets kopiering av vCenter-och NSX-data för att tillhandahålla hög tillgänglighet för hanterings servrar. Före säkerhets kopieringen krypteras alla data på vCenter-källan med hjälp av VMware-API: er. Krypterade data transporteras och lagras i en Azure-blob. Krypterings nycklar för säkerhets kopior lagras i ett mycket säkert CloudSimple hanterat valv som körs i det virtuella CloudSimple-nätverket i Azure.

## <a name="network-security"></a>Nätverkssäkerhet

CloudSimple-lösningen använder lager av nätverks säkerhet.

### <a name="azure-edge-security"></a>Azure Edge-säkerhet

CloudSimple-tjänsterna skapas ovanpå den grundläggande nätverks säkerhet som tillhandahålls av Azure. Azure använder skydds bara metoder för att upptäcka och reagera på nätverksbaserade attacker som är kopplade till avvikande inkommande eller utgående trafik mönster och DDoS-attacker (distributed denial-of-Service). Den här säkerhets kontrollen gäller för privata moln miljöer och kontroll Plans program vara som har utvecklats av CloudSimple.

### <a name="segmentation"></a>Segmentering

CloudSimple-tjänsten har logiskt separata Layer 2-nätverk som begränsar åtkomsten till dina egna privata nätverk i din privata moln miljö. Du kan skydda dina privata moln nätverk ytterligare genom att använda en brand vägg. I CloudSimple-portalen definierar du regler för öst-väst-och norra-syd-nätverksanslutning för all nätverks trafik, som omfattar trafik i olika privata moln, trafik mellan privata moln, allmän trafik till Internet och nätverks trafik till lokalt via IPsec VPN eller Azure ExpressRoute-anslutning.

## <a name="vulnerability-and-patch-management"></a>Sårbarhet och korrigerings hantering 

CloudSimple ansvarar för regelbunden säkerhets korrigering av hanterad VMware-programvara, till exempel ESXi, vCenter och NSX.

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

Kunder kan autentisera sig på sitt Azure-konto (i Azure Active Directory) genom att använda multifaktorautentisering eller SSO som standard. Från Azure Portal kan du starta CloudSimple-portalen utan att ange autentiseringsuppgifter igen.

CloudSimple stöder valfri konfiguration av en identitets källa för det privata molnet vCenter. Du kan använda en [lokal identitets källa](https://docs.azure.cloudsimple.com/set-vcenter-identity), en ny identitets källa för det privata molnet eller [Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad).

Som standard får kunderna de privilegier som krävs för dagliga åtgärder för vCenter i det privata molnet. Den här behörighets nivån inkluderar inte administrativ åtkomst till vCenter. Om administrativ åtkomst tillfälligt krävs kan du [eskalera dina privilegier](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) under en begränsad period medan du slutför de administrativa uppgifterna.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar en CloudSimple-tjänst på Azure](quickstart-create-cloudsimple-service.md).
* Lär dig hur du [skapar ett privat moln](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Lär dig hur du [konfigurerar en privat moln miljö](quickstart-create-private-cloud.md).
