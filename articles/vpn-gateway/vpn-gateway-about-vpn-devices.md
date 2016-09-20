<properties 
   pageTitle="Om VPN-enheter för VPN Gateway-anslutningar från plats till plats i virtuella Azure-nätverk | Microsoft Azure"
   description="Läs mer om VPN-enheter och IPSec-parametrar för S2S VPN Gateway-anslutningar från plats till plats. Plats-till-plats-anslutningar kan användas i hybridkonfigurationer. Den här artikeln innehåller länkar till konfigurationsanvisningar och exempel för VPN Gateway-enheter."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# Om VPN-enheter för VPN Gateway-anslutningar från plats till plats

En VPN-enhet krävs för att konfigurera en plats-till-plats-anslutning (S2S) i VPN. Plats-till-plats-anslutningar kan användas för att skapa en hybridlösning, eller när du vill skapa en säker anslutning mellan ditt lokala nätverk och ditt virtuella nätverk. I den här artikeln beskrivs kompatibla VPN-enheter och konfigurationsparametrar. Observera att när du konfigurerar en plats-till-plats-anslutning, krävs en offentlig IPv4-adress för VPN-enheten.                                                                                                                                                                                

Om enheten inte finns med i tabellen med [verifierade VPN-enheter](#devicetable) kan du gå till avsnittet [Icke-verifierade VPN-enheter](#additionaldevices) i den här artikeln. Det är möjligt att enheten fungerar med Azure ändå. Kontakta enhetstillverkaren för att se vilket stöd som finns för VPN-enheter.

**Observera följande när du läser tabellerna:**

- Terminologin har ändrats för statisk och dynamisk routning. Du kommer troligen stöta på båda termerna. Funktionen har inte ändrats, bara namnen.
    - Statisk routning = Principbaserad
    - Dynamisk routning = Routningsbaserad 
- Specifikationerna för en VPN-gateway med hög kapacitet och en routningsbaserad VPN-gateway är samma, om inget annat anges. Till exempel är verifierade VPN-enheter som är kompatibla med routningsbaserade VPN-gatewayer också kompatibla med Azure VPN-gatewayen med hög kapacitet. 


## <a name="devicetable"></a>Verifierade VPN-enheter 

Vi har verifierat en uppsättning VPN-standardenheter tillsammans med våra enhetsleverantörer. Alla enheter i enhetsfamiljerna som finns i följande lista ska fungera med Azures VPN-gatewayer. I [Om VPN-gatewayer](vpn-gateway-about-vpngateways.md) kan du kontrollera vilken typ av gateway som du måste skapa för den lösning som du vill konfigurera. 

Hjälp med att konfigurera VPN-enheten finns i de länkar som motsvarar lämplig enhetsfamilj. 



| **Leverantör**                      | **Enhetsfamilj**                                        | **Minsta operativsystemversion**                             | **Principbaserad**                                                                                                                                                                                                             | **Routningsbaserad**                                                                                                                                                                    |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis                  | AR-serie VPN-routrar                                    | 2.9.2                                              | Kommer snart                                                                                                                                                                                                                                          | Inte kompatibel                                                                                                                                                                                               |
| Barracuda Networks, Inc.        | Barracuda NextGen Firewall F-serien             | Principbaserad: 5.4.3, routningsbaserad: 6.2.0  | [Konfigurationsanvisningar](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Konfigurationsanvisningar](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW)                                                                                                                                                                                              |
| Barracuda Networks, Inc.        |  Barracuda NextGen Firewall X-serien                 | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Inte kompatibel                                                                                                                                                                                               |
| Brocade                         | Vyatta 5400 vRouter                                      | Virtuell router 6.6R3 GA                            | [Konfigurationsanvisningar](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)                                       | Inte kompatibel                                                                                                                                                                                               |
| Check Point                     | Security Gateway                                         | R75.40, R75.40VS                                     | [Konfigurationsanvisningar](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)                                         | [Konfigurationsanvisningar](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco                           | ASA                                                      | 8.3                                                | [Cisco-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)                                                                                                                                                                        | Inte kompatibel                                                                                                                                                                                               |
| Cisco                           | ASR                                                      | IOS 15.1 (principbaserad), IOS 15.2 (routningsbaserad)                | [Cisco-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                                                        | [Cisco-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                 |
| Cisco                           | ISR                                                      | IOS 15.0 (principbaserad), IOS 15.1 (routningsbaserad*)               | [Cisco-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                                                        | [Cisco-exempel*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                |
| Citrix                          | NetScaler MPX, SDX, VPX      |10.1 och senare                                           | [Integreringsanvisningar](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html)                                                                                                                                                                            | Inte kompatibel                                                                                                                                                                                               |
| Dell SonicWALL                  | TZ-serien, NSA-serien, SuperMassive-serien, E-klass NSA-serien | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646 )          | [Instruktioner – SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instruktioner – SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                   | [Instruktioner – SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instruktioner – SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                                                                      |
| F5                              | BIG-IP-serien                                            | Saknas                                                | [Konfigurationsanvisningar](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)                                                                                                                                                                          | Inte kompatibel                                                                                                                                                                                               |
| Fortinet                        | FortiGate                                                | FortiOS 5.2.7                                      | [Konfigurationsanvisningar](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                                                          | [Konfigurationsanvisningar](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                  |
| Internet Initiative Japan (IIJ) | SEIL-serien                                              | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20            | [Konfigurationsanvisningar](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)                                                                                                                                                                   | Inte kompatibel                                                                                                                                                                                               |
| Juniper                         | SRX                                                      | JunOS 10.2 (principbaserad), JunOS 11.4 (routningsbaserad)            | [Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                                                                      | [Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                              |
| Juniper                         | J-serien                                                 | JunOS 10.4r9 (principbaserad), JunOS 11,4 (routningsbaserad)          | [Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                                                                 | [Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                         |
| Juniper                         | ISG                                                      | ScreenOS 6.3 (princip- och routningsbaserad)                  | [Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                                                                      | [Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                              |
| Juniper                         | SSG                                                      | ScreenOS 6.2 (princip- och routningsbaserad)                  | [Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                                                                      | [Juniper-exempel](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                              |
| Microsoft                       | Routning och fjärråtkomst                        | Windows Server 2012                                | Inte kompatibel                                                                                                                                                                                                                                       | [Microsoft-exempel](http://go.microsoft.com/fwlink/p/?LinkId=717761)                                                                                           |
| Open Systems AG | Mission Control Security Gateway | Saknas | [Installationsguide](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Installationsguide](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan                        | Openswan                                                 | 2.6.32                                             | (Kommer snart)                                                                                                                                                                                                                                        | Inte kompatibel                                                                                                                                                                                               |
| Palo Alto Networks              | Alla enheter som kör PAN-OS     | PAN-OS 6.1.5 eller senare (principbaserad), PAN-OS 7.0.5 eller senare (routningsbaserad)       | [Konfigurationsanvisningar]( https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065)                                                                                                                                                                                          | [Konfigurationsanvisningar](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340)                                                                                                                                                                                    |
| Watchguard                      | Alla                                                      | Fireware XTM v11.x                                 | [Konfigurationsanvisningar](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)                                                                                                                                                                          | Inte kompatibel                                                                                                                                                                                               |

(*) ISR 7200 serie routrar stöder endast principbaserade VPN.

## <a name="additionaldevices"></a>Icke-verifierade VPN-enheter

Om du inte hittar din enhet i tabellen med verifierade VPN-enheter (ovan), kan den ändå fungera med en plats-till-plats-anslutning. Kontrollera att VPN-enheten uppfyller de minimikrav som beskrivs i avsnittet Gateway-krav i artikeln [Om VPN Gateway](vpn-gateway-about-vpngateways.md#gateway-requirements). Enheter som uppfyller minimikraven bör också fungera bra med VPN-gatewayer. Kontakta enhetstillverkaren för ytterligare information om support och konfiguration.


## Redigera enhetens konfigurationsexempel

När du har hämtat den angivna VPN-enhetens konfigurationsexempel, måste du byta ut vissa värden så att de motsvarar inställningarna för din miljö. 

**Så här redigerar du ett exempel:**

1. Öppna exemplet med Anteckningar. 
1. Sök och ersätt alla <*text*>-strängar med de värden som gäller för din miljö. Var noga med att inkludera < och >. När ett namn anges måste det vara unikt. Om ett kommando inte fungerar kan du läsa mer i din enhetstillverkares dokumentation.

| **Exempeltext**                | **Ändra till**                                                                                                        |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP_OnPremisesNetwork&gt;           | Ditt valda namn för det här objektet. Exempel: myOnPremisesNetwork                                                       |
| &lt;RP_AzureNetwork&gt;                | Ditt valda namn för det här objektet. Exempel: myAzureNetwork                                                            |
| &lt;RP_AccessList&gt;                  | Ditt valda namn för det här objektet. Exempel: myAzureAccessList                                                         |
| &lt;RP_IPSecTransformSet&gt;           | Ditt valda namn för det här objektet. Exempel: myIPSecTransformSet                                                       |
| &lt;RP_IPSecCryptoMap&gt;              | Ditt valda namn för det här objektet. Exempel: myIPSecCryptoMap                                                          |
| &lt;SP_AzureNetworkIpRange&gt;         | Ange intervallet. Exempel: 192.168.0.0                                                                                  |
| &lt;SP_AzureNetworkSubnetMask&gt;      | Ange nätmasken. Exempel: 255.255.0.0                                                                            |
| &lt;SP_OnPremisesNetworkIpRange&gt;    | Ange det lokala intervallet. Exempel: 10.2.1.0                                                                         |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; | Ange den lokala nätmasken. Exempel: 255.255.255.0                                                              |
| &lt;SP_AzureGatewayIpAddress&gt;       | Den här informationen är specifik för ditt virtuella nätverk och finns i hanteringsportalen som **IP-adress för gateway**. |
| &lt;SP_PresharedKey&gt;                | Den här informationen är specifik för ditt virtuella nätverk och finns i hanteringsportalen som Hantera nyckel.          |



## IPsec-parametrar

>[AZURE.NOTE] Även om de värden som anges i följande tabell stöds av Azure VPN Gateway går det för närvarande inte att ange eller välja en specifik kombination från Azure VPN Gateway. Du måste ange eventuella begränsningar från den lokala VPN-enheten. Du måste dessutom foga ihop MSS vid 1350.

### Installation av IKE, fas 1

| **Egenskap**                                       | **Principbaserad** | **Routningsbaserad och standard, eller VPN-gateway med hög kapacitet** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE-version                                        | IKEv1                          | IKEv2                                                            |
| Diffie-Hellman Group                               | Grupp 2 (1 024 bitar)             | Grupp 2 (1 024 bitar)                                               |
| Autentiseringsmetod                              | I förväg delad nyckel                 | I förväg delad nyckel                                                   |
| Krypteringsalgoritmer                              | AES256 AES128 3DES             | AES256 3DES                                                      |
| Hash-algoritm                                  | SHA1(SHA128)                   | SHA1(SHA128), SHA2(SHA256)                                                     |
| Fas 1, Security Association (SA), livslängd (tid) | 28 800 sekunder                 | 10 800 sekunder                                                   |


### Installation av IKE, fas 2

| **Egenskap**                                                             | **Principbaserad**                 | **Routningsbaserad och standard, eller VPN-gateway med hög kapacitet**   |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE-version                                                              | IKEv1                                          | IKEv2                                                              |
| Hash-algoritm                                                        | SHA1(SHA128)                                   | SHA1(SHA128)                                                       |
| Fas 2, Security Association (SA), livslängd (tid)                        | 3 600 sekunder                                  | 3 600 sekunder                                                                  |
| Fas 2, Security Association (SA), livslängd (dataflöde)                  | 102 400 000 kB                                 | -                                                                  |
| IPSec-kryptering för SA och autentisering (i prioritetsordning) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. Saknas | Se *Routningsbaserad gateway-IPSec, Security Association (SA), erbjudanden* (nedan) |
| PFS (Perfect Forward Secrecy)                                            | Nej                                             | Ja (DH Group1, 2, 5, 14, 24)                                                    |
| Utebliven peer-identifiering                                                      | Stöds inte                                  | Stöds                                                          |

### Routningsbaserad gateway-IPSec, Security Association (SA), erbjudanden

Följande tabellen visar IPsec, SA-kryptering och autentiseringserbjudanden. Erbjudandena visas i prioritetsordning efter när erbjudandet visats eller godkänts.

| **IPsec, SA-kryptering och autentiseringserbjudanden** | **Azure Gateway som initierare**                               | **Azure Gateway som svarare**                                   |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1                                                 | ESP AES_256 SHA                                              | ESP AES_128 SHA                                              |
| 2                                                 | ESP AES_128 SHA                                              | ESP 3_DES MD5                                                |
| 3                                                 | ESP 3_DES MD5                                                | ESP 3_DES SHA                                                |
| 4                                                 | ESP 3_DES SHA                                                | AH SHA1 med ESP AES_128 med null HMAC                      |
| 5                                                 | AH SHA1 med ESP AES_256 med null HMAC                      | AH SHA1 med ESP 3_DES med null HMAC                        |
| 6                                                 | AH SHA1 med ESP AES_128 med null HMAC                      | AH MD5 med ESP 3_DES med null HMAC, ingen föreslagen livslängd |
| 7                                                 | AH SHA1 med ESP 3_DES med null HMAC                        | AH SHA1 med ESP 3_DES SHA1, ingen livslängd                    |
| 8                                                 | AH MD5 med ESP 3_DES med null HMAC, ingen föreslagen livslängd | AH MD5 med ESP 3_DES MD5, ingen livslängd                     |
| 9                                                 | AH SHA1 med ESP 3_DES SHA1, ingen livslängd                    | ESP DES MD5                                                  |
| 10                                                | AH MD5 med ESP 3_DES MD5, ingen livslängd                     | ESP DES SHA1, ingen livslängd                                   |
| 11                                                | ESP DES MD5                                                  | AH SHA1 med ESP DES, null HMAC, ingen föreslagen livslängd        |
| 12                                                | ESP DES SHA1, ingen livslängd                                   | AH MD5 med ESP DES, null HMAC, ingen föreslagen livslängd        |
| 13                                                | AH SHA1 med ESP DES, null HMAC, ingen föreslagen livslängd        | AH SHA1 med ESP DES SHA1, ingen livslängd                      |
| 14                                                | AH MD5 med ESP DES, null HMAC, ingen föreslagen livslängd        | AH MD5 med ESP DES MD5, ingen livslängd                       |
| 15                                                | AH SHA1 med ESP DES SHA1, ingen livslängd                      | ESP SHA, ingen livslängd                                        |
| 16                                                | AH MD5 med ESP DES MD5, ingen livslängd                       | ESP MD5, ingen livslängd                                        |
| 17                                                | -                                                            | AH SHA, ingen livslängd                                         |
| 18                                                | -                                                            | AH MD5, ingen livslängd                                         |


- Du kan ange IPsec ESP NULL-kryptering med VPN-gatewayer som är routningsbaserade och har hög kapacitet. Null-baserad kryptering ger inget skydd av data under överföringen och bör endast användas när maximalt dataflöde och lägsta svarstid krävs.  Klienter kan välja att använda detta i scenarier med VNet-till-VNet-kommunikation eller när kryptering används någon annanstans i lösningen.

- Vid Internetanslutning på flera platser bör du använda standardinställningarna för Azure VPN Gateway med kryptering och de hash-algoritmer som anges i tabellen ovan, för att garantera säkerheten för din kritiska kommunikation.








<!--HONumber=sep16_HO1-->


