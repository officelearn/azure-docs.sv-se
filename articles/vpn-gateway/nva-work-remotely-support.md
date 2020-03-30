---
title: 'Fjärrarbete: Nva-överväganden (Network Virtual Appliance) för fjärrarbete | Azure VPN-gateway'
description: Den här artikeln hjälper dig att förstå de saker som du bör ta hänsyn till att arbeta med virtuella nätverksinstallationer (NVAs) i Azure under COVID-19-pandemin.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337097"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Arbeta på distans: NVA-överväganden (Network Virtual Appliance) för fjärrarbete

>[!NOTE]
>I den här artikeln beskrivs hur du kan utnyttja virtuella nätverksinstallationer, Azure, Microsoft-nätverk och Azure-partnerekosystemet för att arbeta på distans och minska nätverksproblem som du står inför på grund av COVID-19-krisen.
>

Vissa Azure-kunder använder virtuella enheter från tredje part (NVAs) från Azure Marketplace för att tillhandahålla kritiska tjänster som Point-to-site VPN för sina anställda som arbetar hemifrån under COVID-19-epidemin. I den här artikeln beskrivs några riktlinjer på hög nivå som du bör ta hänsyn till när du använder NVA:er i Azure för att tillhandahålla fjärråtkomstlösningar.

## <a name="nva-performance-considerations"></a>Nva prestanda överväganden

Alla större NVA-leverantörer på Azure Marketplace bör ha rekommendationer om den virtuella datorns storlek och antal instanser som ska användas när de distribuerar sina lösningar.  Även om nästan alla NVA-leverantörer låter dig välja vilken storlek som helst som är tillgänglig för dig i en viss region, är det mycket viktigt att du följer leverantörernas rekommendationer för Azure VM-instansstorlekar, eftersom dessa rekommendationer är de vm-storlekar som leverantören har utfört prestanda testa med i Azure.  

### <a name="consider-the-following"></a>Tänk på följande

- **Kapacitet och antal samtidiga användare** - Det här numret är särskilt viktigt för VPN-användare från point-to-site eftersom varje ansluten användare skapar en krypterad (IPSec eller SSL VPN) tunnel.  
- **Aggregerat dataflöde** - Vilken är den sammanlagda bandbredden du behöver för att tillgodose antalet användare som du behöver för att ge fjärråtkomst.
- **Den vm-storlek du behöver** - Du bör alltid använda VM-storlekar som rekommenderas av NVA-leverantören.  För punkt-till-plats VPN, om du kommer att ha en hel del samtidiga användaranslutningar, bör du använda större VM-storlekar som [Dv2 och DSv2-serien](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Dv2 och Dsv2-serien") virtuella datorer. Dessa virtuella datorer tenderar att ha fler virtuella processorer och kan hantera fler samtidiga VPN-sessioner.  Förutom att ha fler virtuella kärnor har större VM-storlekar i Azure mer mängdbreddskapacitet än mindre vm-storlekar.
    > **Viktigt:** Varje leverantör använder resurser på olika sätt.  Om det inte är klart vilka instansstorlekar du ska använda för att hantera din uppskattade användarbelastning bör du kontakta programvaruleverantören direkt och be dem om en rekommendation.
- **Antal instanser** - Om du förväntar dig att ha ett stort antal användare och anslutningar finns det gränser för vad skala upp dina NVA-instansstorlekar kan uppnå.  Överväg att distribuera flera VM-instanser.
- **IPSec VPN vs SSL VPN** - I allmänhet IPSec VPN implementationer prestera bättre än SSL VPN implementationer.  
- **Licensiering** - Se till att de programvarulicenser du har köpt för NVA-lösningen kommer att täcka den plötsliga tillväxt du kan uppleva under COVID-19-epidemin.  Många NVA-licensprogram begränsar antalet anslutningar eller bandbredd som lösningen klarar av.
- **Accelererat nätverk** - Överväg en NVA-lösning som har stöd för accelererat nätverkande.  Accelererad nätverk möjliggör enkel rot-I/O-virtualisering (SR-IOV) till en virtuell dator, vilket avsevärt förbättrar dess nätverksprestanda. Den här banan med hög prestanda kringgår värden från datasökvägen, vilket minskar svarstiden, jitter- och CPU-användningen för användning med de mest krävande nätverksarbetsbelastningarna på vm-typer som stöds. Accelererade nätverk stöds på de flesta allmänna ändamål och beräkningsoptimerade instansstorlekar med två eller flera virtuella processorer.

## <a name="monitoring-resources"></a>Övervakning av resurser

Varje NVA-lösning har sina egna verktyg och resurser för att övervaka prestanda för deras NVA.  Läs dokumentationen för leverantörerna för att se till att du förstår prestandabegränsningarna och kan identifiera när din NVA är nära eller når kapacitet.  Utöver detta kan du titta på Azure Monitor Network Insights och se grundläggande prestandainformation om dina virtuella nätverksinstallationer, till exempel:

- CPU-användning
- Nätverk – inkommande
- Nätverk – utgående
- Inkommande flöden
- Utgående flöden

## <a name="next-steps"></a>Efterföljande moment

De flesta större NVA-partners har publicerat vägledning kring skalning för plötslig, oväntad tillväxt under COVID-19. Här är några användbara länkar till partnerresurser.

[Barracuda Aktivera arbete hemifrån samtidigt som du säkrar dina data under COVID-19](https://www.barracuda.com/covid-19/work-from-home "Aktivera Arbete hemifrån samtidigt som du skyddar dina data under COVID-19")

[Cisco AnyConnect implementering och prestanda/skalningsreferens för COVID-19-förberedelse](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect implementering och prestanda/skalningsreferens för COVID-19-förberedelse")

[Citrix COVID-19 Svarsstödcenter](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 Svarsstödcenter")

[F5 Vägledning för att ta itu med den dramatiska ökningen av distansarbetare](https://www.f5.com/business-continuity "F5 Vägledning för att ta itu med den dramatiska ökningen av distansarbetare")

[Fortinet COVID-19 Uppdateringar för kunder och partners](https://www.fortinet.com/covid-19.html "COVID-19 Uppdateringar för kunder och partners")

[Palo Alto Nätverk COVID-19 Svarscenter](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Nätverk COVID-19 Svarscenter")
