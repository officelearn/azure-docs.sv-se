---
title: "Om nätverksfunktionerna i Azure till Azure-haveriberedskap med hjälp av Azure Site Recovery | Microsoft Docs"
description: "Ger en översikt över nätverk för replikering av virtuella Azure-datorer med hjälp av Azure Site Recovery."
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/08/2018
ms.author: sujayt
ms.openlocfilehash: 5ce85761df4e0ad62c22a829f67464a3145fd827
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Om nätverk i Azure till Azure-replikering

>[!NOTE]
> Site Recovery replikering för virtuella Azure-datorer är för närvarande under förhandsgranskning.

Den här artikeln innehåller vägledning för nätverk när du replikerar och återställa virtuella Azure-datorer från en region till en annan, med hjälp av [Azure Site Recovery](site-recovery-overview.md). 

## <a name="before-you-start"></a>Innan du börjar

Lär dig hur Site Recovery tillhandahåller katastrofåterställning för [det här scenariot](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Vanlig nätverksinfrastruktur

Följande diagram visar en typisk Azure miljö för program som körs på virtuella Azure-datorer:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Om du använder Azure ExpressRoute eller en VPN-anslutning från ditt lokala nätverk till Azure, miljön ser ut så här:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Oftast skyddas nätverk brandväggar och nätverkssäkerhetsgrupper (NSG: er). Brandväggar Använd URL eller IP-baserade vitlistning för att kontrollera nätverksanslutningen. NSG: er innehåller regler som använder IP-adressintervall för att kontrollera nätverksanslutningen.

>[!IMPORTANT]
> Med hjälp av en autentiserad proxyserver för att kontrollera nätverksanslutningen stöds inte av Site Recovery och det går inte att aktivera replikering.


## <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för URL: er

Om du använder en URL-baserade brandväggen proxy för att styra utgående anslutning Tillåt webbadresserna Site Recovery:


**URL** | **Detaljer**  
--- | ---
*.blob.core.windows.net | Krävs så att data kan skrivas till cache lagringskonto i regionen källa från den virtuella datorn.
login.microsoftonline.com | Krävs för autentiseringen och auktoriseringen till Site Recovery-webbadresser.
*.hypervrecoverymanager.windowsazure.com | Krävs för att kommunikation för Site Recovery-tjänsten kan ske från den virtuella datorn.
*.servicebus.windows.net | Krävs för att Site Recovery övervakning och diagnostik data kan skrivas från den virtuella datorn.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

Om du använder en IP-baserade brandväggen proxy eller NSG-regler för att styra utgående anslutning, måste dessa IP-adressintervall som ska tillåtas.

- Alla IP-adressintervall som motsvarar källplatsen.
    - Du kan hämta den [IP-adressintervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).
    - Du måste tillåta adresserna så att data kan skrivas till cache storage-konto från den virtuella datorn.
- Alla IP-adressintervall som motsvarar Office 365 [autentisering och identitet IP V4-slutpunkter](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
    - Om ny adress läggs till Office 365-adressintervall i framtiden, måste du skapa nya NSG-regler.
- Site Recovery tjänstslutpunkten IP-adresser. Dessa är tillgängliga i en [XML-filen](https://aka.ms/site-recovery-public-ips), och beror på din målplats.
-  Du kan [hämtar och använder det här skriptet](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702), för att automatiskt skapa regler som krävs på NSG: N. 
- Vi rekommenderar att du skapar nödvändiga NSG-regler på ett test NSG och kontrollera att det inte finns några problem innan du skapar regler på en NSG för produktion.
- Se till att prenumerationen är godkända för att skapa antalet NSG-regler som krävs. Kontakta Azure stöd för att öka gränsen för NSG-regel i din prenumeration.

IP-adressintervall är följande:

>
   **mål** | **Site Recovery IP** |  **Site Recovery övervakning IP**
   --- | --- | ---
   Östasien | 52.175.17.132 | 13.94.47.61
   Sydostasien | 52.187.58.193 | 13.76.179.223
   Indien, centrala | 52.172.187.37 | 104.211.98.185
   Södra Indien | 52.172.46.220 | 104.211.224.190
   Norra centrala USA | 23.96.195.247 | 168.62.249.226
   Norra Europa | 40.69.212.238 | 52.169.18.8
   Västra Europa | 52.166.13.64 | 40.68.93.145
   Östra USA | 13.82.88.226 | 104.45.147.24
   Västra USA | 40.83.179.48 | 104.40.26.199
   Södra centrala USA | 13.84.148.14 | 104.210.146.250
   Centrala USA | 40.69.144.231 | 52.165.34.144
   Östra USA 2 | 52.184.158.163 | 40.79.44.59
   Östra Japan | 52.185.150.140 | 138.91.1.105
   Västra Japan | 52.175.146.69 | 138.91.17.38
   Södra Brasilien | 191.234.185.172 | 23.97.97.36
   Östra Australien | 104.210.113.114 | 191.239.64.144
   Sydöstra Australien | 13.70.159.158 | 191.239.160.45
   Centrala Kanada | 52.228.36.192 | 40.85.226.62
   Östra Kanada | 52.229.125.98 | 40.86.225.142
   Västra centrala USA | 52.161.20.168 | 13.78.149.209
   Västra USA 2 | 52.183.45.166 | 13.66.228.204
   Storbritannien, västra | 51.141.3.203 | 51.141.14.113
   Storbritannien, södra | 51.140.43.158 | 51.140.189.52
   Storbritannien, södra 2 | 13.87.37.4| 13.87.34.139
   Storbritannien, norra | 51.142.209.167 | 13.87.102.68
   Centrala Korea | 52.231.28.253 | 52.231.32.85
   Sydkorea | 52.231.298.185 | 52.231.200.144
   
   
  

## <a name="example-nsg-configuration"></a>NSG exempelkonfiguration

Det här exemplet visar hur du konfigurerar NSG-regler för en virtuell dator för att replikera. 

- Om du använder NSG-regler för att styra utgående anslutning kan du använda ”Tillåt utgående HTTPS” regler för alla de nödvändiga IP-adressintervall.
- Exemplet förutsätter att källplatsen VM är ”östra USA” och målplatsen är ”centrala USA.

### <a name="nsg-rules---east-us"></a>NSG-regler - östra USA

1. Skapa regler som motsvarar [Öst oss IP-adressintervall](https://www.microsoft.com/download/confirmation.aspx?id=41653). Detta krävs så att data kan skrivas till cache storage-konto från den virtuella datorn.
2. Skapa regler för alla IP-adressintervall som motsvarar Office 365 [autentisering och identitet IP V4-slutpunkter](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. Skapa regler som motsvarar målplatsen:

   **Plats** | **Site Recovery-IP-adress** |  **Site Recovery övervakning IP-adress**
    --- | --- | ---
   Centrala USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG - regler i centrala USA 

Reglerna krävs så att replikering kan aktiveras från målregionen som för den källa region postredundans:

* Reglerna som motsvarar [centrala USA IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653). Dessa krävs så att data kan skrivas till cache storage-konto från den virtuella datorn.

* Regler för alla IP-adressintervall som motsvarar Office 365 [autentisering och identitet IP V4-slutpunkter](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Regler som motsvarar källplatsen:
    - Östra USA
    - Site Recovery-IP-adress: 13.82.88.226
    - Site Recovery övervakning av IP-adress: 104.45.147.24


## <a name="expressroutevpn"></a>ExpressRoute/VPN 

Följ riktlinjerna i det här avsnittet om du har en ExpressRoute- eller VPN-anslutning mellan lokala och Azure-plats.

### <a name="forced-tunneling"></a>Tvingad tunneltrafik

Normalt kan du definiera en standardväg (0.0.0.0/0) som tvingar utgående Internet-trafiken flöda via den lokala platsen. Vi rekommenderar inte detta. Lämna inte Azure gränsen replikeringstrafik och Site Recovery service-kommunikation. Lösningen är att lägga till användardefinierade vägar (udr: er) för [dessa IP-adressintervall](#outbound-connectivity-for-azure-site-recovery-ip-ranges) så att replikeringstrafiken inte gå lokalt.

### <a name="connectivity"></a>Anslutning 

Följ dessa riktlinjer för anslutningar mellan platsen och den lokala platsen:
- Om ditt program behöver ansluta till lokala datorer eller om det finns klienter som ansluter till programmet från lokala via VPN/ExpressRoute, se till att du har minst en [plats-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) mellan mål-Azure-region och lokala datacenter.

- Om du förväntar dig en mängd trafik mellan mål-Azure-region och lokala datacenter, bör du skapa en annan [ExpressRoute-anslutning](../expressroute/expressroute-introduction.md) mellan aktuella Azure-region och lokala datacenter.

- Om du vill behålla IP-adresser för virtuella datorer när de växlar över hålla den målregionen plats-till-plats/ExpressRoute-anslutning i frånkopplat tillstånd. Detta är att kontrollera att det finns inga intervall krockar mellan käll-region IP-intervall och mål region IP-adressintervall.

### <a name="expressroute-configuration"></a>ExpressRoute-konfiguration
Följa dessa rekommendationer för ExpressRoute-konfiguration:

- Du måste skapa en ExpressRoute-krets i både käll- och områden. Sedan måste du skapa en anslutning mellan:
  - Det virtuella nätverket för källa och ExpressRoute-kretsen.
  - Det virtuella målnätverket och ExpressRoute-kretsen.

- Som en del av ExpressRoute-standard, kan du skapa kretsar i samma geopolitiska region. Om du vill skapa ExpressRoute-kretsar i olika geopolitiska regioner Azure ExpressRoute Premium krävs, vilket innebär att en inkrementell kostnad. (Om du redan använder ExpressRoute Premium, det finns inget extra kostnad.) Mer information finns i [ExpressRoute platser dokumentet](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) och [ExpressRoute priser](https://azure.microsoft.com/pricing/details/expressroute/).

- Vi rekommenderar att du använder olika IP-adressintervall i käll- och områden. ExpressRoute-kretsen kommer inte att kunna ansluta till två virtuella Azure-nätverk med samma IP-adressintervall samtidigt.

- Du kan skapa virtuella nätverk med samma IP-adressintervall i båda regioner och sedan skapa ExpressRoute-kretsar i båda regioner. Koppla bort kretsen från det virtuella nätverket källa vid en redundansväxling och Anslut krets i det virtuella målnätverket.

 >[!IMPORTANT]
 > Om den primära regionen inte är helt kan koppla från åtgärden misslyckas. Som förhindrar att virtuella målnätverket komma ExpressRoute-anslutning.

## <a name="next-steps"></a>Nästa steg
Börja skydda dina arbetsbelastningar av [replikering av Azure virtuella datorer](site-recovery-azure-to-azure.md).
