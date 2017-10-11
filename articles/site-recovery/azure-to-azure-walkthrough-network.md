---
title: "Planera nätverk för VMware Azure replikeringen med Site Recovery | Microsoft Docs"
description: "Den här artikeln beskrivs planering av krävs vid replikering av virtuella datorer i Azure med Azure Site Recovery"
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
ms.date: 08/01/2017
ms.author: sujayt
ms.openlocfilehash: 7b37e853f6b97ba313111f9201f877846a28fae9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="step-3-plan-networking-for-azure-vm-replication"></a>Steg 3: Planera nätverk för Virtuella Azure-replikering

När du har bekräftat att den [kraven för distribution av](azure-to-azure-walkthrough-prerequisites.md), den här artikeln för att förstå nätverk överväganden när replikering och återställa virtuella Azure-datorer (VM) från en Azure-region till en annan, med hjälp av Azure Site Recovery-tjänsten. 

- När du är klar artikeln bör du ha en förståelse för hur du ställer in utgående åtkomst för Azure virtuella datorer du vill replikera och hur du ansluter från den lokala platsen till dessa virtuella datorer.
- Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
> Azure VM-replikeringen med Site Recovery är för närvarande under förhandsgranskning.



## <a name="network-overview"></a>Översikt över nätverk

Vanligtvis ditt virtuella Azure-datorer finns i ett Azure virtual network/undernät och det finns en anslutning från den lokala platsen till Azure med hjälp av Azure ExpressRoute eller en VPN-anslutning.

Nätverk skyddas vanligtvis använder brandväggar och/eller nätverkssäkerhetsgrupper (NSG: er). Brandväggar kan använda URL-baserade på IP-baserade listor, att nätverksanslutningen för kontrollen. NSG: er använda IP-intervallet-baserade regler. 


Site Recovery fungerar som förväntat, måste du göra några ändringar i utgående nätverksanslutningen, från virtuella datorer som du vill replikera. Site Recovery stöder inte användning av en autentiseringsproxy till kontrollen nätverksanslutning. Om du har en autentiseringsproxy går inte att aktivera replikering. 


Följande diagram visar en normal miljö för ett program som körs på virtuella Azure-datorer.

![kund-miljö](./media/azure-to-azure-walkthrough-network/source-environment.png)

**Azure VM-miljön**

Du kan också ha en anslutning till Azure från din lokala plats med hjälp av Azure ExpressRoute eller en VPN-anslutning. 

![kund-miljö](./media/azure-to-azure-walkthrough-network/source-environment-expressroute.png)

**Lokal anslutning till Azure**



## <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för URL: er

Om du använder en URL-baserade brandväggen proxy för att styra utgående anslutning, kontrollerar du att dessa URL: er som används av Site Recovery

**URL: EN** | **Detaljer**  
--- | ---
*.blob.core.windows.net | Gör att data skrivs från den virtuella datorn till cache lagringskonto i regionen källa.
login.microsoftonline.com | Tillhandahåller autentiseringen och auktoriseringen till Site Recovery-webbadresser.
*.hypervrecoverymanager.windowsazure.com | Tillåter kommunikation med Site Recovery-tjänsten från den virtuella datorn.
*. servicebus.windows.net | Krävs för att Site Recovery övervakning och diagnostik data kan skrivas från den virtuella datorn.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

- Du kan automatiskt skapa regler som krävs på NSG: N genom att hämta och kör [skriptet](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).
- Vi rekommenderar att du skapar nödvändiga NSG-regler på ett test NSG och kontrollera att det inte finns några problem innan du skapar regler på en NSG för produktion.
- Se till att prenumerationen är godkända för att skapa antalet NSG-regler som krävs. Kontakta supporten om du vill öka antalet NSG regeln i din prenumeration.
Om du använder någon IP-baserade brandväggen proxy eller NSG-regler för att styra utgående anslutning måste följande IP-intervall vara godkända, beroende på käll- och målplatserna för virtuella datorer:

    - Alla IP-adressintervall som motsvarar källplatsen. Hämta den [intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).) Vitlistning krävs, så att data kan skrivas till cache storage-konto från den virtuella datorn.
    - Alla IP-adressintervall som motsvarar Office 365 [autentisering och identitet IP V4-slutpunkter](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity). Om nya IP-adresser läggs till Office 365 IP-adressintervall, måste du skapa nya NSG-regler.
-     Site Recovery-tjänsten endpoint IP-adresser ([tillgängliga i en XML-fil](https://aka.ms/site-recovery-public-ips)), som beror på din målplats: 

   **Målplats** | **Site Recovery-tjänsten IP-adresser** |  **Site Recovery övervakning IP**
   --- | --- | ---
   Östasien | 52.175.17.132</br>40.83.121.61 | 13.94.47.61
   Sydostasien | 52.187.58.193</br>52.187.169.104 | 13.76.179.223
   Indien, centrala | 52.172.187.37</br>52.172.157.193 | 104.211.98.185
   Södra Indien | 52.172.46.220</br>52.172.13.124 | 104.211.224.190
   Norra centrala USA | 23.96.195.247</br>23.96.217.22 | 168.62.249.226
   Norra Europa | 40.69.212.238</br>13.74.36.46 | 52.169.18.8
   Västra Europa | 52.166.13.64</br>52.166.6.245 | 40.68.93.145
   Östra USA | 13.82.88.226</br>40.71.38.173 | 104.45.147.24
   Västra USA | 40.83.179.48</br>13.91.45.163 | 104.40.26.199
   Södra centrala USA | 13.84.148.14</br>13.84.172.239 | 104.210.146.250
   Centrala USA | 40.69.144.231</br>40.69.167.116 | 52.165.34.144
   Östra USA 2 | 52.184.158.163</br>52.225.216.31 | 40.79.44.59
   Östra Japan | 52.185.150.140</br>13.78.87.185 | 138.91.1.105
   Västra Japan | 52.175.146.69</br>52.175.145.200 | 138.91.17.38
   Södra Brasilien | 191.234.185.172</br>104.41.62.15 | 23.97.97.36
   Östra Australien | 104.210.113.114</br>40.126.226.199 | 191.239.64.144
   Sydöstra Australien | 13.70.159.158</br>13.73.114.68 | 191.239.160.45
   Centrala Kanada | 52.228.36.192</br>52.228.39.52 | 40.85.226.62
   Östra Kanada | 52.229.125.98</br>52.229.126.170 | 40.86.225.142
   Västra centrala USA | 52.161.20.168</br>13.78.230.131 | 13.78.149.209
   Västra USA 2 | 52.183.45.166</br>52.175.207.234 | 13.66.228.204
   Storbritannien, västra | 51.141.3.203</br>51.140.226.176 | 51.141.14.113
   Storbritannien, södra | 51.140.43.158</br>51.140.29.146 | 51.140.189.52

## <a name="example-nsg-configuration"></a>NSG exempelkonfiguration

Det här avsnittet visar hur du konfigurerar NSG-regler så att replikeringar fungerar för en virtuell dator. Om du använder NSG-regler för att styra utgående anslutning kan du använda ”Tillåt utgående HTTPS” regler för alla IP-adressintervall som krävs.

I det här exemplet är VM-källplats ”östra USA”. Målplatsen för replikering är centrala USA.


### <a name="example"></a>Exempel

#### <a name="east-us"></a>Östra USA

1. Skapa regler som motsvarar [östra USA IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653). Detta krävs så att data kan skrivas till cache storage-konto från den virtuella datorn.
2. Skapa regler för alla IP-adressintervall som motsvarar Office 365 [autentisering och identitet IP V4-slutpunkter](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. Skapa regler som motsvarar målplatsen:

   **Plats** | **Site Recovery-tjänsten IP-adresser** |  **Site Recovery övervakning IP**
    --- | --- | ---
   Centrala USA | 40.69.144.231</br>40.69.167.116 | 52.165.34.144

#### <a name="central-us"></a>Centrala USA

Reglerna krävs så att replikering från målregionen som för käll-regionen efter växling vid fel.

1. Skapa regler som motsvarar [centrala USA IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).
2. Skapa regler för alla IP-adressintervall som motsvarar Office 365 [autentisering och identitet IP V4-slutpunkter](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. Skapa regler som motsvarar källplatsen:

   **Plats** | **Site Recovery-tjänsten IP-adresser** |  **Site Recovery övervakning IP**
    --- | --- | ---
   Östra USA | 13.82.88.226</br>40.71.38.173 | 104.45.147.24


## <a name="existing-on-premises-connection"></a>Befintlig lokal anslutning

Om du har en ExpressRoute- eller VPN-anslutning mellan lokal plats och källplatsen i Azure, Följ dessa riktlinjer:

**Konfiguration** | **Detaljer**
--- | ---
**Tvingad tunneltrafik** | Vanligtvis tvingar en standardväg (0.0.0.0/0) utgående internet-trafiken flöda via den lokala platsen. Vi rekommenderar inte. Replikeringstrafik och Site Recovery kommunikation ska hålla sig inom Azure.<br/><br/> Lägga till användardefinierade vägar (udr: er) som en lösning för [dessa IP-adressintervall](#outbound-connectivity-for-azure-site-recovery-ip-ranges), så att trafiken inte gå lokalt.
**Anslutning** | Om appar som behöver ansluta till lokala datorer eller lokala klienter ansluter till appen över lokala via VPN/ExpressRoute, kontrollera att du har minst en [plats-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), mellan aktuella Azure-region och den lokala platsen.<br/><br/> Om trafikvolymer är högt mellan aktuella Azure-region och den lokala platsen, kan du skapa en ny [ExpressRoute-anslutning](../expressroute/expressroute-introduction.md), mellan målregionen och lokalt.<br/><br/> Om du vill behålla IP-adresser för virtuella datorer efter redundans hålla den målregionen plats-till-plats/ExpressRoute-anslutning i frånkopplat tillstånd. Detta säkerställer att det finns inga intervall konflikt mellan käll- och IP-adressintervall.
**ExpressRoute** | Skapa en ExpressRoute-krets i käll- och områden.<br/><br/> Skapa en anslutning mellan källnätverket och ExpressRoute-kretsen och mellan målnätverket och kretsen.<br/><br/> Vi rekommenderar att du använder olika IP-adressintervall i käll- och områden. Kretsen kan inte ansluta till mer än en Azure-nätverk med samma IP-intervall på samma gång.<br/><br/> Du kan skapa virtuella nätverk med samma IP-adressintervall i båda regioner och sedan skapa ExpressRoute-kretsar i båda regioner. Koppla bort kretsen från det virtuella nätverket källa för växling vid fel, och Anslut krets i det virtuella målnätverket.<br/><br/> Om den primära regionen inte är helt kan koppla från åtgärden misslyckas. I det här fallet kommer det virtuella nätverket målet inte ExpressRoute-anslutning.
**ExpressRoute Premium** | Du kan skapa kretsar i samma geopolitiska region.<br/><br/> För att skapa kretsar i olika geopolitiska regioner, behöver du Azure ExpressRoute Premium.<br/><br/> Kostnaden är inkrementell med Premium. Om du redan använder den, är det utan extra kostnad.<br/><br/> Lär dig mer om [platser](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) och [priser](https://azure.microsoft.com/pricing/details/expressroute/).



## <a name="next-steps"></a>Nästa steg

Gå till [steg 4: skapa ett valv](azure-to-azure-walkthrough-vault.md)

