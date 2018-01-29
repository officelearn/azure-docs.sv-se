---
title: "Azure Stack-brandväggen planera för Azure-stacken integrerat system | Microsoft Docs"
description: "Beskriver Azure Stack brandväggen överväganden för flera noder Azure Stack Azure-anslutna distributioner."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 737df48a24d8e077f898d00526c7138db69dbb93
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="azure-stack-firewall-integration"></a>Integrering med Azure Stack-brandväggen
Det rekommenderas att du använder en enhet för att säkra Azure-stacken. Även om brandväggar kan hjälpa dig med t.ex distribuerad denial of service (DDOS) attacker, intrångsidentifiering och innehållsgranskning, blir de också en genomströmning flaskhals för Azure storage-tjänster som blobbar, tabeller och köer.

Baserat på identitetsmodellen Azure Active Directory (AD Azure) eller Windows Server Active Directory Federation Services (AD FS) kan behöva du publicera AD FS-slutpunkten. Om en frånkopplad distributionsläget används, måste du publicera AD FS-slutpunkten. Mer information finns i [datacenter integration identitet artikel](azure-stack-integrate-identity.md).

Azure Resource Manager (administratör), administratörsportalen och Key Vault (administratör) slutpunkter kräver inte nödvändigtvis externa publicering. Till exempel som en tjänsteleverantör kanske du vill begränsa risken för angrepp och administrera endast Azure-paketet i nätverket och inte från internet.

För företag, kan det externa nätverket vara befintliga företagsnätverket. I ett sådant scenario, måste du publicera dessa slutpunkter för att fungera Azure Stack från företagsnätverket.

### <a name="network-address-translation"></a>Network Address Translation
NAT (Network Address Translation) är den rekommenderade metoden för att tillåta distribution av virtuell dator (DVM) för att komma åt externa resurser och internet under distributionen av samt de virtuella datorerna nödsituation Recovery konsolen (ERCS) eller Privilegierade slutpunkt (program) under registrering och felsökning.

NAT kan också vara ett alternativ till offentliga IP-adresser på externt nätverk eller offentliga virtuella IP-adresser. Det rekommenderas dock inte eftersom det begränsar användarupplevelsen klient och ökar komplexitet. De två alternativen är en 1:1-NAT som fortfarande kräver en offentlig IP per användare IP-adress på pool eller NAT-enhet som kräver en NAT-regel per användare VIP som innehåller kopplingar till alla portar som en användare kan använda flera: 1.

Några av downsides för att använda NAT för offentliga VIP är:
- NAT lägger till användning vid hantering av brandväggsregler eftersom användarna styra sina egna slutpunkter och sina egna regler för serverpublicering i stacken programvarudefinierade nätverksfunktioner (SDN). Användarna måste kontakta Azure-stacken operatorn att få sina VIP: er som har publicerats och uppdatera portlistan över.
- Vid användning av NAT begränsar användarupplevelsen, ger fullständig behörighet till operatorn över publiceringsförfrågningar.
- För hybridscenarion moln med Azure, Överväg att Azure inte stöder att konfigurera en VPN-tunnel till en slutpunkt som använder NAT.

### <a name="ssl-decryption"></a>SSL-dekryptering
För närvarande vår rekommendation dekrypterades SSL är att inaktivera den på alla Azure Stack-trafik, i framtiden vi ger riktlinjer för hur du aktiverar SSL-dekryptering för Azure-stacken.

## <a name="edge-firewall-scenario"></a>Edge brandväggen scenario
I en kant-distribution distribueras Azure Stack direkt bakom gränsroutern eller brandväggen. I följande scenarier stöds för brandväggen för att vara över kantlinjen eller fungerar som kantlinje enheten om den stöder lika kostnaden Multi Path ECMP () med BGP eller statisk routning.

Normalt har offentligt dirigerbara IP-adresser angetts för den offentliga VIP-poolen från det externa nätverket vid tidpunkten för distribution. I en kant-scenariot rekommenderas det inte att använda offentliga dirigerbara IP-adresser på ett annat nätverk av säkerhetsskäl. Det här scenariot kan en användare har fullständig själva kontrollerade molnet upplevelse som i ett offentligt moln som Azure.  

![Azure Stack edge brandväggen exempel](.\media\azure-stack-firewall\edge-firewall-scenario.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Företagets intranät eller perimeternätverk brandväggen scenariot
I ett intranät eller perimeternätverk företagsdistribution bör distribueras Azure Stack på en multi-zonindelad brandvägg eller mellan gränsbrandväggen och interna, företagets nätverksbrandvägg. Sedan dess trafiken distribueras mellan säker, perimeternätverk (eller DMZ) och osäkra zoner som beskrivs nedan:

- **Säker zon**: Detta är det interna nätverket som använder interna eller företagets dirigerbara IP-adresser. Säkra nätverk kan delas upp, har utgående Internetåtkomst via NAT i brandväggen och är vanligtvis nås från var som helst i ditt datacenter via det interna nätverket. Alla Azure-stacken nätverk ska placeras i zonen säker utom det externa nätverket offentliga VIP-pool.
- **Perimeternätverk zonen**. Perimeternätverket är där externa eller program som webbservrar distribueras vanligtvis mot internet. Det är vanligtvis övervakas av en brandvägg för att undvika attacker som DDoS och för intrångsidentifiering (hackningsförsök) samtidigt som angivna inkommande trafik från internet. Endast en externt nätverk offentliga VIP-pool i Azure-stacken ska placeras i zonen DMZ.
- **Osäkra zonen**. Detta är det externa nätverket internet. Den **är inte** rekommenderas att distribuera Azure-stacken i zonen oskyddat.

![Azure Stack perimeter-nätverk, exempel](.\media\azure-stack-firewall\perimeter-network-scenario.png)


## <a name="next-steps"></a>Nästa steg
Lär dig mer om [portar och protokoll som används av Azure Stack-slutpunkter](azure-stack-integrate-endpoints.md)

