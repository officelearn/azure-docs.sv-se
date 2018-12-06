---
title: Integrerade Azure Stack-brandväggen planering för Azure Stack-system | Microsoft Docs
description: Beskriver överväganden för Azure Stack-Brandvägg för Azure Stack Azure-ansluten distribution av flera noder.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 3759a9845d4ad1514fc5f0183c78b5eca2e31464
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960659"
---
# <a name="azure-stack-firewall-integration"></a>Integrering med Azure Stack-brandväggen
Du rekommenderas att du använder en brandväggsenhet för att säkra Azure Stack. Även om brandväggar kan hjälpa dig med distribuerade denial of service DDOS-attacker, intrångsidentifiering och innehållsgranskning, kan de också blir en flaskhals för dataflöde för Azure storage-tjänster som blobbar, tabeller och köer.

Baserat på identitetsmodellen Azure Active Directory (AD Azure) eller Windows Server Active Directory Federation Services (AD FS), kan du behöva publicera AD FS-slutpunkten. Om en frånkopplad distributionsläget används, måste du publicera den AD FS-slutpunkten. Mer information finns i den [datacenter integration identitet artikeln](azure-stack-integrate-identity.md).

Azure Resource Manager (administratör), administratörsportalen och slutpunkter för Key Vault (administratör) kräver inte externa publicering nödvändigtvis. Till exempel en tjänstleverantör, kan du minska risken för angrepp och administrera Azure Stack från endast i ditt nätverk och inte från internet.

För företag, kan det externa nätverket vara befintliga företagets nätverk. I ett sådant scenario, måste du publicera dessa slutpunkter för att köra Azure Stack från företagsnätverket.

### <a name="network-address-translation"></a>Network Address Translation
Network Address Translation (NAT) är den rekommenderade metoden att tillåta en distribution av virtuell dator (DVM) för att få åtkomst till de externa resurserna och internet under distributionen av samt de nödfall Recovery konsolen (ERCS) virtuella datorerna eller privilegierad slutpunkt (program) under registrering och felsökning.

NAT kan också vara ett alternativ till offentliga IP-adresser på det externa nätverket eller offentliga virtuella IP-adresser. Det rekommenderas dock inte eftersom det begränsar klient användarupplevelsen och ökar komplexiteten. De två alternativen är en 1:1-NAT som fortfarande kräver en offentlig IP per användare IP-adresser på poolen eller många: 1 NAT som kräver en NAT-regeln per användare VIP som innehåller kopplingar till alla portar en användare kan använda.

Några av nackdelarna med att använda NAT för offentlig VIP-adress är:
- NAT lägger till användning vid hantering av brandväggsregler eftersom användare styra sina egna slutpunkter och sina egna publiceringsregler i stacken programvarudefinierade nätverk (SDN). Användare måste kontakta Azure Stack-operatör att få sina VIP som har publicerats och för att uppdatera listan över portar.
- Även om användning av NAT gränser användarupplevelsen, ger fullständig behörighet till operatorn över publiceringsförfrågningar.
- För hybridmolnscenarierna med Azure, Överväg att Azure inte har stöd för hur du konfigurerar en VPN-tunnel till en slutpunkt som använder NAT.

### <a name="ssl-decryption"></a>SSL-dekryptering
För närvarande rekommenderar vi att inaktivera SSL-dekryptering på alla Azure Stack-trafik. Om det stöds i framtida uppdateringar, ges vägledning om hur du aktiverar SSL-dekryptering för Azure Stack.

## <a name="edge-firewall-scenario"></a>Edge-brandväggen scenario
I en edge-distribution med Azure Stack direkt bakom edge-routern eller brandväggen. I dessa scenarier stöds för brandväggen för att vara högre än kantlinje (1-Scenario) där det har stöd för både aktiv-aktiv och aktivt-passivt brandväggskonfigurationer eller fungerar som kantlinje enheten (Scenario 2) där det endast har stöd för aktiv-aktiv brandvägg förlita dig på lika med kostnaden Multi sökväg (ECMP) med BGP eller statisk routning för redundans-konfiguration.

Vanligtvis har offentliga dirigerbara IP-adresser angetts för den offentliga VIP-poolen från det externa nätverket vid tidpunkten för distribution. I ett scenario för edge rekommenderas inte att använda offentliga dirigerbara IP-adresser på ett annat nätverk av säkerhetsskäl. Det här scenariot kan en användare att uppleva fullständig lokal kontrollerad molnupplevelse som i ett offentligt moln som Azure.  

![Brandväggen exempel på Azure Stack edge](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Intranät eller perimeternätverket nätverket brandväggen företagsscenariot
I en intranät- eller perimeternätverket företagsdistribution med Azure Stack på en multi-zonindelad brandvägg eller mellan gränsbrandväggen och interna, företagets brandvägg. Dess trafiken distribueras sedan mellan säker, perimeternätverk (eller DMZ) och oskyddade zoner som beskrivs nedan:

- **Säker zon**: det här är det interna nätverket som använder interna eller företagets dirigerbara IP-adresser. Säkert nätverk kan delas, ha utgående Internetåtkomst via NAT i brandväggen och kan vanligtvis nås från var som helst i ditt datacenter via det interna nätverket. Alla Azure Stack-nätverk ska placeras i zonen säker utom det externa nätverket offentliga VIP-poolen.
- **Perimeter zonen**. Perimeternätverket är där externa eller program som webbservrar distribueras vanligen som riktas mot internet. Det är vanligtvis övervakas av en brandvägg för att undvika attacker som DDoS och för intrångsidentifiering (kodar) samtidigt som fortfarande angivna inkommande trafik från internet. Endast en externt nätverk offentliga VIP-pool i Azure-stacken ska placeras i perimeterrnätverkszonen.
- **Oskyddat zon**. Det här är det externa nätverket internet. Den **är inte** rekommenderas att distribuera Azure Stack i zonen oskyddat.

![Exempel på nätverket Azure Stack perimeternätverket](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Läs mer
Läs mer om [portar och protokoll som används av Azure Stack-slutpunkter](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Nästa steg
[Krav för Azure Stack PKI](azure-stack-pki-certs.md)

