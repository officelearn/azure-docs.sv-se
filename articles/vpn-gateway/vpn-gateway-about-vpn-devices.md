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
ms.date: 03/03/2017
ms.author: yushwang;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 13ef48ebe79571c7139e46f9510a5f8d2f504cb7
ms.lasthandoff: 03/15/2017


---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>Om VPN-enheter och IPSec-/IKE-parametrar för anslutningar för VPN-gateway från plats till plats

En VPN-enhet krävs för att konfigurera en VPN-anslutning för plats-till-plats (S2S) på olika platser med hjälp av en VPN-gateway. Plats-till-plats-anslutningar kan användas för att skapa en hybridlösning, eller när du vill skapa en säker anslutning mellan ditt lokala nätverk och ditt virtuella nätverk. I den här artikeln beskrivs kompatibla VPN-enheter och konfigurationsparametrar. Det här dokumentet innehåller listan över IPsec-/IKE-parametrar för Azure VPN-gateways och en lista över validerade VPN-enheter som ansluter till Azure VPN-gateways.


> [!IMPORTANT]
> Om du har problem med anslutningen mellan dina lokala VPN-enheter och Azure VPN-gateways läser du avsnittet [Kända enhetskompatibilitetsproblem](#known). 


###<a name="items-to-note-when-viewing-the-tables"></a>Observera följande när du läser tabellerna:

* Terminologin har ändrats för Azure VPN-gateways. Du kommer troligen stöta på båda termerna. Funktionen har inte ändrats, bara namnen.
  * Statisk routning = Principbaserad
  * Dynamisk routning = Routningsbaserad
* Specifikationerna för en VPN-gateway med hög kapacitet och en routningsbaserad VPN-gateway är samma, om inget annat anges. Till exempel är verifierade VPN-enheter som är kompatibla med routningsbaserade VPN-gatewayer också kompatibla med Azure VPN-gatewayen med hög kapacitet.

> [!NOTE]
> När du konfigurerar en plats-till-plats-anslutning krävs en offentlig IPv4-adress för VPN-enheten.                                                                                                                                                                               


## <a name="devicetable"></a>Verifierade VPN-enheter
Vi har verifierat en uppsättning VPN-standardenheter tillsammans med våra enhetsleverantörer. Alla enheter i enhetsfamiljerna som finns i följande lista ska fungera med Azures VPN-gatewayer. I [Om VPN-gatewayer](vpn-gateway-about-vpngateways.md) kan du kontrollera vilken typ av gateway som du måste skapa för den lösning som du vill konfigurera.

Hjälp med att konfigurera VPN-enheten finns i de länkar som motsvarar lämplig enhetsfamilj. Kontakta enhetens tillverkare för att se vilka VPN-enheter som stöds.

| **Leverantör** | **Enhetsfamilj** | **Minsta operativsystemversion** | **Principbaserad** | **Routningsbaserad** |
| --- | --- | --- | --- | --- |
| Allied Telesis |AR-serie VPN-routrar |2.9.2 |Kommer snart |Inte kompatibel |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall F-serien |Principbaserad: 5.4.3<br>Routningsbaserad: 6.2.0 |[Konfigurationsanvisningar](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Konfigurationsanvisningar](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall X-serien |Barracuda Firewall 6.5 |[Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Inte kompatibel |
| Brocade |Vyatta 5400 vRouter |Virtuell router 6.6R3 GA |[Konfigurationsanvisningar](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Inte kompatibel |
| Check Point |Security Gateway |R77.30 |[Konfigurationsanvisningar](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Konfigurationsanvisningar](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco |ASA |8.3 |[Cisco-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |Inte kompatibel |
| Cisco |ASR |Principbaserad: IOS 15.1<br>Routningsbaserad: IOS 15.2 |[Cisco-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Cisco-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |Principbaserad: IOS 15.0<br>Routningsbaserad*: IOS 15.1 |[Cisco-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Cisco-exempel*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 och senare |[Integreringsanvisningar](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Inte kompatibel |
| Dell SonicWALL |TZ-serie, NSA-serie<br>SuperMassive-serie<br>NSA-serie i E-klassen |SonicOS 5.8.x<br>[SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850)<br>[SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) |[Konfigurationsguide för SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Konfigurationsguide för SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |[Konfigurationsguide för SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Konfigurationsguide för SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 |BIG-IP-serien |12.0 |[Konfigurationsanvisningar](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Konfigurationsanvisningar](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.4.2 |[Konfigurationsanvisningar](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |[Konfigurationsanvisningar](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |
| Internet Initiative Japan (IIJ) |SEIL-serien |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Konfigurationsanvisningar](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Inte kompatibel |
| Juniper |SRX |Principbaserad: JunOS 10.2<br>Routningsbaserad: JunOSS 11.4 |[Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |J-serien |Principbaserad: JunOS 10.4r9<br>Routningsbaserad: JunOSS 11.4 |[Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Routning och fjärråtkomst |Windows Server 2012 |Inte kompatibel |[Microsoft-exempel](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Mission Control Security Gateway |Saknas |[Installationsguide](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |[Installationsguide](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan |Openswan |2.6.32 |(Kommer snart) |Inte kompatibel |
| Palo Alto Networks |Alla enheter som kör PAN-OS |PAN-OS<br>Principbaserad: 6.1.5 eller senare<br>Routningsbaserad: 7.1.4 |[Konfigurationsanvisningar](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Konfigurationsanvisningar](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| WatchGuard |Alla |Fireware XTM<br> Principbaserad: v11.11.x<br>Routningsbaserad: v11.12.x |[Konfigurationsanvisningar](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Konfigurationsanvisningar](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

(*) ISR 7200 serie routrar stöder endast principbaserade VPN.

## <a name="additionaldevices"></a>Icke-verifierade VPN-enheter
Om du inte hittar din enhet i tabellen med verifierade VPN-enheter kan enheten ändå fungera med en plats-till-plats-anslutning. Kontakta enhetstillverkaren för ytterligare information om support och konfiguration.

## <a name="editing"></a>Redigera enhetens konfigurationsexempel
När du har hämtat den angivna VPN-enhetens konfigurationsexempel, måste du byta ut vissa värden så att de motsvarar inställningarna för din miljö.

###<a name="to-edit-a-sample"></a>Så här redigerar du ett exempel:

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

## <a name="IPSec"></a>IPsec-/IKE-parametrar
> [!NOTE]
> Även om de värden som anges i följande tabell stöds av Azure VPN Gateway går det för närvarande inte att ange eller välja en specifik kombination av algoritmer eller parametrar från Azure VPN-gatewayen. Du måste ange eventuella begränsningar från den lokala VPN-enheten.
> 
> Du måste dessutom foga ihop **MSS** vid **1350**.

I tabellerna nedan:

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
| SA-livstid           |28&800; sekunder     |10&800; sekunder     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametrar för IKE fas 2 (snabbläge)
| **Egenskap**                  |**Principbaserad**| **Routningsbaserad**                              |
| ---                           | ---           | ---                                         |
| IKE-version                   |IKEv1          |IKEv2                                        |
| Krypterings- och hash-algoritmer |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[RouteBased QM SA-erbjudanden](#RouteBasedOffers) |
| SA-livstid (tid)            |3&600; sekunder  |3&600; sekunder                                |
| SA-livstid (byte)           |102&400;&000; kB | -                                           |
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

* Du kan ange IPsec ESP NULL-kryptering med VPN-gatewayer som är routningsbaserade och har hög kapacitet. Null-baserad kryptering ger inget skydd av data under överföringen och bör endast användas när maximalt dataflöde och lägsta svarstid krävs.  Klienter kan välja att använda detta i scenarier med VNet-till-VNet-kommunikation eller när kryptering används någon annanstans i lösningen.
* Vid Internetanslutning på flera platser bör du använda standardinställningarna för Azure VPN Gateway med kryptering och de hash-algoritmer som anges i tabellen ovan, för att garantera säkerheten för din kritiska kommunikation.

## <a name="known"></a>Kända enhetskompatibilitetsproblem

> [!IMPORTANT]
> Det här är kända kompatibilitetsproblem mellan VPN-enheter från tredje part och Azure VPN-gateways. Azure-teamet arbetar aktivt med leverantörerna för att åtgärda de problem som beskrivs här. Den här sidan uppdateras med den senaste informationen när problemen har åtgärdats. Kom tillbaka regelbundet.

###<a name="feb-16-2017"></a>16 februari 2017

**Palo Alto Networks-enheter med tidigare versioner än 7.1.4** för Azure-vägbaserad VPN: Om du använder VPN-enheter från Palo Alto Networks med en PAN-OS-version äldre än 7.1.4 och har problem att ansluta till Azure-vägbaserade VPN-gateways ska du göra så här:

1. Kontrollera Palo Alto Networks-enhetens version av den inbyggda programvaran (firmware). Om din version av PAN-OS är äldre än 7.1.4 uppgraderar du till 7.1.4.
2. På Palo Alto Networks-enheten ändrar du livslängden för Phase 2 SA (eller Quick Mode SA) till 28 800 sekunder (8 timmar) vid anslutning till Azure VPN-gatewayen.
3. Om du fortfarande har anslutningsproblem skapar du en supportbegäran på Azure Portal.

