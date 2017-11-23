---
title: "Om VPN-enheter för Azure-anslutningar på olika platser | Microsoft Docs"
description: "Den här artikeln beskriver VPN-enheter och IPSec-parametrar för S2S VPN Gateway-anslutningar på olika platser. Länkar finns till konfigurationsanvisningar och exempel."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: yushwang;cherylmc
ms.openlocfilehash: 9cd626e324dab3428a7b69556e3307dd0b92958b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>Om VPN-enheter och IPSec-/IKE-parametrar för anslutningar för VPN-gateway från plats till plats

En VPN-enhet krävs för att konfigurera en VPN-anslutning för plats-till-plats (S2S) på olika platser med hjälp av en VPN-gateway. Plats-till-plats-anslutningar kan användas för att skapa en hybridlösning, eller när du vill skapa säkra anslutningar mellan ditt lokala nätverk och ditt virtuella nätverk. Den här artikeln innehåller en lista över verifierade VPN-enheter och en lista över IPsec-/IKE-parametrar för VPN-gatewayer.

> [!IMPORTANT]
> Om du har problem med anslutningen mellan dina lokala VPN-enheter och VPN-gatewayer läser du avsnittet [Kända enhetskompatibilitetsproblem](#known).
>

### <a name="items-to-note-when-viewing-the-tables"></a>Observera följande när du läser tabellerna:

* Terminologin har ändrats för Azure VPN-gateways. Endast namnen har ändrats. Funktionaliteten har inte ändrats.
  * Statisk routning = Principbaserad
  * Dynamisk routning = Routningsbaserad
* Specifikationerna för en VPN-gateway med hög kapacitet och en routningsbaserad VPN-gateway är samma, om inget annat anges. Till exempel är verifierade VPN-enheter som är kompatibla med routningsbaserade VPN-gatewayer också kompatibla med VPN-gatewayen med hög kapacitet.

## <a name="devicetable"></a>Validerade VPN-enheter och guider för enhetskonfiguration

> [!NOTE]
> När du konfigurerar en plats-till-plats-anslutning krävs en offentlig IPv4-adress för VPN-enheten.
>

Vi har verifierat en uppsättning VPN-standardenheter tillsammans med våra enhetsleverantörer. Alla enheter i enhetsfamiljerna i följande lista bör fungera med VPN-gatewayer. Mer information om VPN-typerna (PolicyBased eller RouteBased) för den VPN Gateway-lösning som du vill konfigurera finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md#vpntype).

Hjälp med att konfigurera VPN-enheten finns i de länkar som motsvarar lämplig enhetsfamilj. Länkarna till konfigurationsanvisningarna tillhandahålls i mån av möjlighet. Kontakta enhetens tillverkare för att se vilka VPN-enheter som stöds.

|**Leverantör**          |**Enhetsfamilj**     |**Minsta operativsystemversion** |**Instruktioner för principbaserad konfiguration** |**Instruktioner för routningsbaserad konfiguration** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Inte kompatibel  |[Konfigurationsguide](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |AR-serie VPN-routrar |2.9.2                  |Kommer snart     |Inte kompatibel  |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall F-serien |Principbaserad: 5.4.3<br>Routningsbaserad: 6.2.0 |[Konfigurationsguide](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Konfigurationsguide](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall X-serien |Barracuda Firewall 6.5 |[Konfigurationsguide](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Inte kompatibel |
| Brocade            |Vyatta 5400 vRouter   |Virtuell router 6.6R3 GA|[Konfigurationsguide](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Inte kompatibel |
| Check Point |Security Gateway |R77.30 |[Konfigurationsguide](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Konfigurationsguide](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4 och senare versioner (IKEv2*) |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |[Konfigurationsguide*](vpn-gateway-3rdparty-device-config-cisco-asa.md) |
| Cisco |ASR |Principbaserad: IOS 15.1<br>Routningsbaserad: IOS 15.2 |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |Principbaserad: IOS 15.0<br>Routningsbaserad*: IOS 15.1 |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Konfigurationsexempel**](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 och senare |[Konfigurationsguide](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Inte kompatibel |
| F5 |BIG-IP-serien |12.0 |[Konfigurationsguide](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Konfigurationsguide](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.4.2 |  |[Konfigurationsguide](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |
| Internet Initiative Japan (IIJ) |SEIL-serien |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Konfigurationsguide](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Inte kompatibel |
| Juniper |SRX |Principbaserad: JunOS 10.2<br>Routningsbaserad: JunOSS 11.4 |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |J-serien |Principbaserad: JunOS 10.4r9<br>Routningsbaserad: JunOSS 11.4 |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Konfigurationsexempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Routning och fjärråtkomst |Windows Server 2012 |Inte kompatibel |[Konfigurationsexempel](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Mission Control Security Gateway |Saknas |[Konfigurationsguide](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |Inte kompatibel |
| Palo Alto Networks |Alla enheter som kör PAN-OS |PAN-OS<br>Principbaserad: 6.1.5 eller senare<br>Routningsbaserad: 7.1.4 |[Konfigurationsguide](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Konfigurationsguide](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| ShareTech | Nästa datagenerations UTM (Nu-serien) | 9.0.1.3 | Inte kompatibel | [Konfigurationsguide](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |TZ-serie, NSA-serie<br>SuperMassive-serie<br>NSA-serie i E-klassen |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Stöds inte|[Konfigurationsguide](https://www.sonicwall.com/en-us/support/knowledge-base/170505320011694) |
| WatchGuard |Alla |Fireware XTM<br> Principbaserad: v11.11.x<br>Routningsbaserad: v11.12.x |[Konfigurationsguide](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Konfigurationsguide](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

> [!NOTE]
>
> (*) Cisco ASA version 8.4 och senare har stöd för IKEv2 och kan ansluta till Azure VPN Gateway med hjälp av anpassade IPsec-/IKE-principer med alternativet ”UsePolicyBasedTrafficSelectors”. Mer information finns i den här [instruktionsartikeln](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> (**) Routrar i ISR 7200-serien stöder endast principbaserade VPN-gatewayer.

## <a name="additionaldevices"></a>Icke-verifierade VPN-enheter

Om du inte hittar din enhet i tabellen med verifierade VPN-enheter kan enheten ändå fungera med en plats-till-plats-anslutning. Kontakta enhetstillverkaren för ytterligare information om support och konfiguration.

## <a name="editing"></a>Redigera enhetens konfigurationsexempel

När du har hämtat den angivna VPN-enhetens konfigurationsexempel, måste du byta ut vissa värden så att de motsvarar inställningarna för din miljö.

### <a name="to-edit-a-sample"></a>Så här redigerar du ett exempel:

1. Öppna exemplet med Anteckningar.
2. Sök och ersätt alla <*text*>-strängar med de värden som gäller för din miljö. Var noga med att inkludera < och >. När ett namn anges måste det vara unikt. Om ett kommando inte fungerar kan du läsa mer i din enhetstillverkares dokumentation.

| **Exempeltext** | **Ändra till** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Ditt valda namn för det här objektet. Exempel: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |Ditt valda namn för det här objektet. Exempel: myAzureNetwork |
| &lt;RP_AccessList&gt; |Ditt valda namn för det här objektet. Exempel: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |Ditt valda namn för det här objektet. Exempel: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |Ditt valda namn för det här objektet. Exempel: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Ange intervallet. Exempel: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Ange nätmasken. Exempel: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Ange det lokala intervallet. Exempel: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Ange den lokala nätmasken. Exempel: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Den här informationen är specifik för ditt virtuella nätverk och finns i hanteringsportalen som **IP-adress för gateway**. |
| &lt;SP_PresharedKey&gt; |Den här informationen är specifik för ditt virtuella nätverk och finns i hanteringsportalen som Hantera nyckel. |

## <a name="ipsec"></a>IPsec-/IKE-parametrar

> [!IMPORTANT]
> 1. Tabellerna nedan innehåller de kombinationer av algoritmer och parametrar som används av Azure VPN-gatewayer med standardkonfigurationen. För routningsbaserade VPN-gatewayer som skapats med Azure Resource Management-distributionsmodellen kan du ange en anpassad princip för varje enskild anslutning. Detaljerade anvisningar finns i avsnittet [Configure IPsec/IKE policy](vpn-gateway-ipsecikepolicy-rm-powershell.md) (Konfigurera IPsec-/IKE-princip).
>
> 2. Du måste dessutom foga ihop TCP **MSS** vid **1350**. Om dina VPN-enheter inte stöder MSS-ihopfogning kan du i stället ange **MTU** i tunnelgränssnittet till **1400** byte.
>

I följande tabeller:

* SA = Security Association
* IKE fas 1 kallas även "Huvudläge"
* IKE fas 2 kallas även "Snabbläge"

### <a name="ike-phase-1-main-mode-parameters"></a>Parametrar för IKE fas 1 (huvudläge)

| **Egenskap**          |**Principbaserad**    | **Routningsbaserad**    |
| ---                   | ---               | ---               |
| IKE-version           |IKEv1              |IKEv2              |
| Diffie-Hellman Group  |Grupp 2 (1 024 bitar) |Grupp 2 (1 024 bitar) |
| Autentiseringsmetod |I förväg delad nyckel     |I förväg delad nyckel     |
| Krypterings- och hash-algoritmer |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| SA-livstid           |28 800 sekunder     |28 800 sekunder     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametrar för IKE fas 2 (snabbläge)

| **Egenskap**                  |**Principbaserad**| **Routningsbaserad**                              |
| ---                           | ---           | ---                                         |
| IKE-version                   |IKEv1          |IKEv2                                        |
| Krypterings- och hash-algoritmer |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[RouteBased QM SA-erbjudanden](#RouteBasedOffers) |
| SA-livstid (tid)            |3 600 sekunder  |27 000 sekunder                                |
| SA-livstid (byte)           |102 400 000 kB | -                                           |
| PFS (Perfect Forward Secrecy) |Nej             |[RouteBased QM SA-erbjudanden](#RouteBasedOffers) |
| Utebliven peer-identifiering (DPD)     |Stöds inte  |Stöds                                    |


### <a name ="RouteBasedOffers"></a>Erbjudanden för RouteBased VPN IPsec-säkerhetsassociation (IKE-snabbläge SA)

Följande tabell visar erbjudanden för IPsec SA (IKE-snabbläge). Erbjudandena visas i prioritetsordning efter när erbjudandet visats eller godkänts.

#### <a name="azure-gateway-as-initiator"></a>Azure Gateway som initierare

|-  |**Kryptering**|**Autentisering**|**PFS-grupp**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Ingen         |
| 2 |AES256        |SHA1              |Ingen         |
| 3 |3DES          |SHA1              |Ingen         |
| 4 |AES256        |SHA256            |Ingen         |
| 5 |AES128        |SHA1              |Ingen         |
| 6 |3DES          |SHA256            |Ingen         |

#### <a name="azure-gateway-as-responder"></a>Azure Gateway som svarare

|-  |**Kryptering**|**Autentisering**|**PFS-grupp**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Ingen         |
| 2 |AES256        |SHA1              |Ingen         |
| 3 |3DES          |SHA1              |Ingen         |
| 4 |AES256        |SHA256            |Ingen         |
| 5 |AES128        |SHA1              |Ingen         |
| 6 |3DES          |SHA256            |Ingen         |
| 7 |DES           |SHA1              |Ingen         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |Ingen         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Du kan ange IPsec ESP NULL-kryptering med routningsbaserade VPN-gatewayer och VPN-gatewayer som har hög kapacitet. Null-baserad kryptering ger inget skydd av data under överföringen och bör endast användas när maximalt dataflöde och lägsta svarstid krävs. Klienter kan välja att använda detta i scenarier med VNet-till-VNet-kommunikation eller när kryptering används någon annanstans i lösningen.
* Vid Internetanslutning på flera platser bör du använda standardinställningarna för Azure VPN Gateway med kryptering och de hash-algoritmer som anges i tabellen ovan, för att garantera säkerheten för din kritiska kommunikation.

## <a name="known"></a>Kända enhetskompatibilitetsproblem

> [!IMPORTANT]
> Det här är kända kompatibilitetsproblem mellan VPN-enheter från tredje part och Azure VPN-gateways. Azure-teamet arbetar aktivt med leverantörerna för att åtgärda de problem som beskrivs här. Den här sidan uppdateras med den senaste informationen när problemen har åtgärdats. Kom tillbaka regelbundet.
>
>

### <a name="feb-16-2017"></a>16 februari 2017

**Palo Alto Networks-enheter med tidigare versioner än 7.1.4** för Azure-vägbaserad VPN: Om du använder VPN-enheter från Palo Alto Networks med en PAN-OS-version äldre än 7.1.4 och har problem att ansluta till Azure-vägbaserade VPN-gateways ska du göra så här:

1. Kontrollera Palo Alto Networks-enhetens version av den inbyggda programvaran (firmware). Om din version av PAN-OS är äldre än 7.1.4 uppgraderar du till 7.1.4.
2. På Palo Alto Networks-enheten ändrar du livslängden för Phase 2 SA (eller Quick Mode SA) till 28 800 sekunder (8 timmar) vid anslutning till Azure VPN-gatewayen.
3. Om du fortfarande har anslutningsproblem skapar du en supportbegäran i Azure Portal.
