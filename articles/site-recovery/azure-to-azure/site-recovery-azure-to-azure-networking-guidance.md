---
title: "Azure Site Recovery nätverk vägledning för att replikera virtuella datorer från Azure till Azure | Microsoft Docs"
description: "Nätverk vägledning för att replikera virtuella Azure-datorer"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: a2ea66f2ed3815d0375001571e64dad338f16e3e
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="networking-guidance-for-replicating-azure-virtual-machines"></a>Nätverk vägledning för att replikera virtuella Azure-datorer

>[!NOTE]
> Site Recovery replikering för virtuella Azure-datorer är för närvarande under förhandsgranskning.

Den här artikeln beskrivs nätverksfunktioner vägledning för Azure Site Recovery när du replikerar och återställa virtuella Azure-datorer från en region till en annan region. Mer information om krav för Azure Site Recovery finns i [krav](site-recovery-support-matrix-azure-to-azure.md) artikel.

## <a name="site-recovery-architecture"></a>Site Recovery-arkitekturen

Site Recovery tillhandahåller ett enkelt och enkelt sätt att replikera program som körs på virtuella Azure-datorer till en annan Azure-region så att de kan återställas om det finns ett avbrott i den primära regionen. Lär dig mer om [det här scenariot och Site Recovery-arkitekturen](concepts-azure-to-azure-architecture.md).

## <a name="your-network-infrastructure"></a>Nätverkets infrastruktur

Följande diagram visar vanliga Azure-miljön för ett program som körs på virtuella Azure-datorer:

![kund-miljö](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Om du använder Azure ExpressRoute eller en VPN-anslutning från ett lokalt nätverk till Azure miljön ser ut så här:

![kund-miljö](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalt skydda kunder sina nätverk med brandväggar och/eller nätverkssäkerhetsgrupper (NSG: er). Brandväggarna kan använda antingen URL- eller IP-baserade vitlistning för att kontrollera nätverksanslutningen. NSG: er tillåter regler för att använda IP-intervall för att kontrollera nätverksanslutningen.

>[!IMPORTANT]
> Om du använder en autentiserad proxyserver för att kontrollera nätverksanslutningen, stöds inte och går inte att aktivera replikering för Site Recovery.

I följande avsnitt beskrivs utgående anslutning Nätverksändringar som krävs från Azure virtuella datorer för Site Recovery replikering ska fungera.

## <a name="outbound-connectivity-for-azure-site-recovery-urls"></a>Utgående anslutning för Azure Site Recovery URL: er

Om du använder alla URL-baserade brandväggen proxy för att styra utgående anslutning, att kontrollera listan över godkända följande obligatoriska Azure Site Recovery-webbadresser:


**URL: EN** | **Syfte**  
--- | ---
*.blob.core.windows.net | Krävs så att data kan skrivas till cache lagringskonto i regionen källa från den virtuella datorn.
login.microsoftonline.com | Krävs för autentiseringen och auktoriseringen till Site Recovery-webbadresser.
*.hypervrecoverymanager.windowsazure.com | Krävs för att kommunikation för Site Recovery-tjänsten kan ske från den virtuella datorn.
*. servicebus.windows.net | Krävs för att Site Recovery övervakning och diagnostik data kan skrivas från den virtuella datorn.

## <a name="outbound-connectivity-for-azure-site-recovery-ip-ranges"></a>Utgående anslutning för Azure Site Recovery IP-adressintervall

>[!NOTE]
> För att automatiskt skapa nödvändiga NSG-regler på nätverkssäkerhetsgruppen, kan du [hämtar och använder det här skriptet](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

>[!IMPORTANT]
> * Vi rekommenderar att du skapar nödvändiga NSG-regler på en test nätverkssäkerhetsgrupp och kontrollera att det inte finns några problem innan du skapar regler på en nätverkssäkerhetsgrupp för produktion.
> * Se till att prenumerationen är godkända för att skapa antalet NSG-regler som krävs. Kontakta supporten om du vill öka antalet NSG regeln i din prenumeration.

Om du använder någon IP-baserade brandväggen proxy eller NSG-regler för att styra utgående anslutning måste följande IP-intervall vara godkända, beroende på käll- och målplatserna för virtuella datorer:

- Alla IP-adressintervall som motsvarar källplatsen. (Du kan hämta den [IP-adressintervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).) Vitlistning krävs så att data kan skrivas till cache storage-konto från den virtuella datorn.

- Alla IP-adressintervall som motsvarar Office 365 [autentisering och identitet IP V4-slutpunkter](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

    >[!NOTE]
    > Om nya IP-adresser läggs till Office 365 IP-adressintervall i framtiden, måste du skapa nya NSG-regler.

- Site Recovery tjänstslutpunkten IP-adresser ([tillgängliga i en XML-fil](https://aka.ms/site-recovery-public-ips)), som beror på din målplats:

   **Målplats** | **Site Recovery-tjänsten IP-adresser** |  **Site Recovery övervakning IP**
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

## <a name="sample-nsg-configuration"></a>Exempel på NSG konfiguration
Det här avsnittet beskrivs hur du konfigurerar NSG-regler så att Site Recovery replikering kan fungera på en virtuell dator. Om du använder NSG-regler för att styra utgående anslutning, kan du använda ”Tillåt utgående HTTPS” regler för alla IP-adressintervall som krävs.

>[!Note]
> För att automatiskt skapa nödvändiga NSG-regler på nätverkssäkerhetsgruppen, kan du [hämtar och använder det här skriptet](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

Om den Virtuella datorns plats har ”Öst oss” och din replikering målplatsen är ”centrala oss”, följer du stegen i följande två avsnitt.

>[!IMPORTANT]
> * Vi rekommenderar att du skapar nödvändiga NSG-regler på en test nätverkssäkerhetsgrupp och kontrollera att det inte finns några problem innan du skapar regler på en nätverkssäkerhetsgrupp för produktion.
> * Se till att prenumerationen är godkända för att skapa antalet NSG-regler som krävs. Kontakta supporten om du vill öka antalet NSG regeln i din prenumeration.

### <a name="nsg-rules-on-the-east-us-network-security-group"></a>NSG-regler på östra USA nätverkssäkerhetsgruppen

* Skapa regler som motsvarar [östra USA IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653). Detta krävs så att data kan skrivas till cache storage-konto från den virtuella datorn.

* Skapa regler för alla IP-adressintervall som motsvarar Office 365 [autentisering och identitet IP V4-slutpunkter](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Skapa regler som motsvarar målplatsen:

   **Plats** | **Site Recovery-tjänsten IP-adresser** |  **Site Recovery övervakning IP**
    --- | --- | ---
   Centrala USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules-on-the-central-us-network-security-group"></a>NSG-regler på centrala USA nätverkssäkerhetsgruppen

Reglerna krävs så att replikering kan aktiveras från målregionen som för den källa region postredundans:

* Reglerna som motsvarar [centrala USA IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653). Dessa krävs så att data kan skrivas till cache storage-konto från den virtuella datorn.

* Regler för alla IP-adressintervall som motsvarar Office 365 [autentisering och identitet IP V4-slutpunkter](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Regler som motsvarar källplatsen:

   **Plats** | **Site Recovery-tjänsten IP-adresser** |  **Site Recovery övervakning IP**
    --- | --- | ---
   Östra USA | 13.82.88.226 | 104.45.147.24


## <a name="guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration"></a>Riktlinjer för befintliga Azure till lokala ExpressRoute/VPN-konfiguration

Följ riktlinjerna i det här avsnittet om du har en ExpressRoute- eller VPN-anslutning mellan lokala och källplats i Azure.

### <a name="forced-tunneling-configuration"></a>Tvingad tunneltrafik konfiguration

En vanlig konfiguration av customer är att definiera en standardväg (0.0.0.0/0) som tvingar utgående Internet-trafiken flöda via den lokala platsen. Vi rekommenderar inte detta. Lämna inte Azure gränsen replikeringstrafik och Site Recovery service-kommunikation. Lösningen är att lägga till användardefinierade vägar (udr: er) för [dessa IP-adressintervall](#outbound-connectivity-for-azure-site-recovery-ip-ranges) så att replikeringstrafiken inte gå lokalt.

### <a name="connectivity-between-the-target-and-on-premises-location"></a>Anslutningen mellan mål- och lokala platsen

Följ dessa riktlinjer för anslutningar mellan platsen och den lokala platsen:
- Om ditt program behöver ansluta till lokala datorer eller om det finns klienter som ansluter till programmet från lokala via VPN/ExpressRoute, se till att du har minst en [plats-till-plats-anslutning](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) mellan mål-Azure-region och lokala datacenter.

- Om du förväntar dig en mängd trafik mellan mål-Azure-region och lokala datacenter, bör du skapa en annan [ExpressRoute-anslutning](../../expressroute/expressroute-introduction.md) mellan aktuella Azure-region och lokala datacenter.

- Om du vill behålla IP-adresser för virtuella datorer när de växlar över hålla den målregionen plats-till-plats/ExpressRoute-anslutning i frånkopplat tillstånd. Detta är att kontrollera att det finns inga intervall krockar mellan käll-region IP-intervall och mål region IP-adressintervall.

### <a name="best-practices-for-expressroute-configuration"></a>Metodtips för ExpressRoute-konfiguration
Följa dessa rekommendationer för ExpressRoute-konfiguration:

- Du måste skapa en ExpressRoute-krets i både käll- och områden. Sedan måste du skapa en anslutning mellan:
  - Det virtuella nätverket för källa och ExpressRoute-kretsen.
  - Det virtuella målnätverket och ExpressRoute-kretsen.

- Som en del av ExpressRoute-standard, kan du skapa kretsar i samma geopolitiska region. Om du vill skapa ExpressRoute-kretsar i olika geopolitiska regioner Azure ExpressRoute Premium krävs, vilket innebär att en inkrementell kostnad. (Om du redan använder ExpressRoute Premium, det finns inget extra kostnad.) Mer information finns i [ExpressRoute platser dokumentet](../../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) och [ExpressRoute priser](https://azure.microsoft.com/pricing/details/expressroute/).

- Vi rekommenderar att du använder olika IP-adressintervall i käll- och områden. ExpressRoute-kretsen kommer inte att kunna ansluta till två virtuella Azure-nätverk med samma IP-adressintervall samtidigt.

- Du kan skapa virtuella nätverk med samma IP-adressintervall i båda regioner och sedan skapa ExpressRoute-kretsar i båda regioner. Koppla bort kretsen från det virtuella nätverket källa vid en redundansväxling och Anslut krets i det virtuella målnätverket.

 >[!IMPORTANT]
 > Om den primära regionen inte är helt kan koppla från åtgärden misslyckas. Som förhindrar att virtuella målnätverket komma ExpressRoute-anslutning.

## <a name="next-steps"></a>Nästa steg
Börja skydda dina arbetsbelastningar av [replikering av Azure virtuella datorer](azure-to-azure-quickstart.md).
