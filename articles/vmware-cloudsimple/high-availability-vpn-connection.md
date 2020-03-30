---
title: Azure VMware Solution by CloudSimple – Konfigurera hög tillgänglighet från lokalt till CloudSimple VPN-gateway
description: Beskriver hur du konfigurerar en anslutning med hög tillgänglighet från din lokala miljö till en CloudSimple VPN-gateway aktiverad för hög tillgänglighet
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025273"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Konfigurera en anslutning med hög tillgänglighet från lokalt till CloudSimple VPN-gateway

Nätverksadministratörer kan konfigurera en VPN-anslutning med hög tillgänglighet från sin lokala miljö till en CloudSimple VPN-gateway.

Den här guiden innehåller steg för att konfigurera en lokal brandvägg för en VPN-anslutning för IPsec-till-plats-VPN med hög tillgänglighet. De detaljerade stegen är specifika för typen av lokal brandvägg. Som exempel presenterar den här guiden steg för två typer av brandväggar: Cisco ASA och Palo Alto Networks.

## <a name="before-you-begin"></a>Innan du börjar

Slutför följande uppgifter innan du konfigurerar den lokala brandväggen.

1. Kontrollera att din organisation har [etablerat](create-nodes.md) de nödvändiga noderna och skapade minst ett CloudSimple Private Cloud.
2. [Konfigurera en VPN-gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) från plats till plats mellan det lokala nätverket och ditt CloudSimple Private Cloud.

Se [översikt över VPN-gateways](cloudsimple-vpn-gateways.md) för fas 1- och fas 2-förslag som stöds.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Konfigurera lokal Cisco ASA-brandvägg

Instruktionerna i det här avsnittet gäller Cisco ASA version 8.4 och senare. I konfigurationsexempelet distribueras Cisco Adaptive Security Appliance Software Version 9.10 och konfigureras i IKEv1-läge.

För att VPN-platsen ska fungera måste du tillåta UDP 500/4500 och ESP (IP-protokoll 50) från CloudSimples primära och sekundära offentliga IP (peer IP) på det externa gränssnittet för den lokala Cisco ASA VPN-gatewayen.

### <a name="1-configure-phase-1-ikev1"></a>1. Konfigurera fas 1 (IKEv1)

Om du vill aktivera fas 1 (IKEv1) på det externa gränssnittet anger du följande CLI-kommando i Cisco ASA-brandväggen.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. Skapa en IKEv1-policy

Skapa en IKEv1-princip som definierar de algoritmer och metoder som ska användas för hashning, autentisering, Diffie-Hellman-grupp, livstid och kryptering.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Skapa en tunnelgrupp

Skapa en tunnelgrupp under IPsec-attributen. Konfigurera peer-IP-adressen och den fördelade nyckeln för tunnel, som du anger när du [konfigurerar vpn-gatewayen plats till plats](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Konfigurera fas 2 (IPsec)

Om du vill konfigurera fas 2 (IPsec) skapar du en åtkomstkontrollista (ACL) som definierar den trafik som ska krypteras och tunnel. I följande exempel kommer trafiken av intresse från tunneln som kommer från det lokala lokala undernätet (10.16.1.0/24) till fjärrundernätet Privat moln (192.168.0.0/24). Åtkomstkontrollistan kan innehålla flera poster om det finns flera undernät mellan platserna.

I Cisco ASA-version 8.4 och senare kan objekt eller objektgrupper skapas som fungerar som behållare för nätverk, undernät, värd-IP-adresser eller flera objekt. Skapa ett objekt för det lokala och ett objekt för fjärrundernäten och använd dem för krypto-åtkomstkontrollistorna och NAT-satserna.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Definiera ett lokalt lokalt undernät som ett objekt

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Definiera CloudSimple-fjärrundernätet som ett objekt

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Konfigurera en åtkomstlista för trafik av intresse

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Konfigurera transformeringsuppsättningen

Konfigurera transformeringsuppsättningen (TS), ```ikev1```som måste involvera nyckelordet . De krypterings- och hash-attribut som anges i TS måste matcha med de parametrar som anges i [Standardkonfiguration för CloudSimple VPN-gateways](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Konfigurera kryptokartan

Konfigurera kryptokartan, som innehåller dessa komponenter:

* Peer-IP-adress
* Definierad ACL som innehåller trafik av intresse
* Transformera uppsättning

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Tillämpa kryptokartan

Använd kryptokartan på det externa gränssnittet:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Bekräfta tillämpliga NAT-regler

Följande är NAT-regeln som används. Se till att VPN-trafiken inte utsätts för någon annan NAT-regel.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Exempel på IPsec Site-to-Site VPN-etablerad utdata från Cisco ASA

Fas 1-utgång:

![Fas 1-utgång för Cisco ASA-brandvägg](media/ha-vpn-connection-cisco-phase1.png)

Fas 2-utgång:

![Fas 2-utgång för Cisco ASA-brandvägg](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Konfigurera den lokala Palo Alto Networks-brandväggen

Instruktionerna i det här avsnittet gäller Palo Alto Networks version 7.1 och senare. I det här konfigurationsexempelet distribueras Palo Alto Networks VM-seriens programvaruversion 8.1.0 i IKEv1-läge.

För att VPN-platsen ska fungera måste du tillåta UDP 500/4500 och ESP (IP-protokoll 50) från CloudSimples primära och sekundära offentliga IP (peer IP) på det externa gränssnittet för den lokala Palo Alto Networks-gatewayen.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Skapa primära och sekundära tunnelgränssnitt

Logga in på Palo Alto-brandväggen, välj > **Nätverksgränssnittstunnel** > **Tunnel** > **Lägg till**, konfigurera följande fält och klicka på **OK**. **Network**

* Gränssnittsnamn. Det första fältet fylls i automatiskt med nyckelordet "tunnel". I det intilliggande fältet anger du valfritt tal mellan 1 och 9999. Det här gränssnittet används som ett primärt tunnelgränssnitt för att transportera plats-till-plats-trafik mellan det lokala datacentret och det privata molnet.
* Kommentar. Ange kommentarer för enkel identifiering av syftet med tunneln
* Netflow-profil. Låt standardvärdet vara kvar.
* Config. Tilldela gränssnitt till: Virtuell router: Välj **standard**. 
        Säkerhetszon: Välj zonen för betrodd LAN-trafik. I det här exemplet är namnet på zonen för LAN-trafik "Förtroende".
* IPv4. Klicka på **Lägg till** och lägg till oanvända /32 ip-adresser som inte överlappar varandra i din miljö, som kommer att tilldelas det primära tunnelgränssnittet och kommer att användas för att övervaka tunnlarna (förklaras senare).

Eftersom den här konfigurationen är för en VPN med hög tillgänglighet krävs två tunnelgränssnitt: Ett primärt och ett sekundärt. Upprepa föregående steg för att skapa det sekundära tunnelgränssnittet. Välj ett annat tunnel-ID och en annan oanvänd /32 IP-adress.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Ställ in statiska vägar för privata molnundernät som ska nås via VPN-platsen för plats till plats

Vägar är nödvändiga för att de lokala undernäten ska nå CloudSimple privata molnundernät.

 > Välj Virtuella > **nätverksroutrar***som standard* > **Statiska vägar** > Lägg**till,** konfigurera följande fält och klicka på **OK**. **Network**

* Namn. Ange ett namn för enkel identifiering av syftet med rutten.
* Destination. Ange de privata molnnäten Cloud Som ska nås via S2S-tunnelgränssnitt från lokala
* Gränssnitt. Välj det primära tunnelgränssnitt som skapas i steg 1 (avsnitt-2) i listrutan. I det här exemplet är det tunnel.20.
* Nästa hopp. Välj **Ingen**.
* Admin Avstånd. Låt standardvärdet vara kvar.
* Metriska. Ange valfritt värde från 1 till 65535. Nyckeln är att ange lägre mått för rutten som motsvarar primärtunnelgränssnitt jämfört med rutten motsvarande sekundära tunnelgränssnitt vilket gör den tidigare vägen att föredra. Om tunnel.20 har ett mätvärde på 20 i motsats till ett måttvärde på 30 för tunnel.30, är tunnel.20 att föredra.
* Rutttabell. Låt standardvärdet vara kvar.
* BFD-profil. Låt standardvärdet vara kvar.
* Banövervakning. Lämna alternativet omarkerat.

Upprepa föregående steg för att skapa en annan väg för privata molnundernät att använda som en sekundär/säkerhetskopieringsväg via sekundärt tunnelgränssnitt. Den här gången väljer du ett annat tunnel-ID och ett högre mått än för den primära vägen.

### <a name="3-define-the-cryptographic-profile"></a>3. Definiera den kryptografiska profilen

Definiera en kryptografisk profil som anger protokoll och algoritmer för identifiering, autentisering och kryptering som ska användas för att konfigurera VPN-tunnlar i IKEv1 fas 1.

Välj **Nätverk** > **Expandera nätverksprofiler** > **IKE Crypto** > **Add**, konfigurera följande fält och klicka på **OK**.

* Namn. Ange valfritt namn på IKE-kryptoprofilen.
* DH-gruppen. Klicka på **Lägg till** och välj lämplig DH-grupp.
* Kryptering. Klicka på **Lägg till** och välj lämplig krypteringsmetod.
* Autentisering. Klicka på **Lägg till** och välj lämplig autentiseringsmetod.
* Livstid. Låt standardvärdet vara kvar.
* IKEv2-autentisering flera. Låt standardvärdet vara kvar.

### <a name="4-define-ike-gateways"></a>4. Definiera IKE-gateways

Definiera IKE-gateways för att upprätta kommunikation mellan peer-datorer i varje ände av VPN-tunneln.

Välj **Nätverk** > **Expandera nätverksprofiler** > **IKE Gateways** > **Lägg till,** konfigurera följande fält och klicka på **OK**.

Fliken Allmänt:

* Namn. Ange namnet på IKE-gatewayen som ska peered med den primära CloudSimple VPN-peer.
* Version. Välj **endast IKEv1-läge**.
* Adresstyp. Välj **IPv4**.
* Gränssnitt. Välj det offentliga vänte- eller utanförgränssnittet.
* Lokal IP-adress. Låt standardvärdet vara kvar.
* Peer IP-adresstyp. Välj **IP**.
* Peer-adress. Ange den primära CloudSimple VPN-peer-IP-adressen.
* Autentisering. Välj **Fördelad nyckel**.
* Fördelad nyckel / Bekräfta fördelad nyckel. Ange den fördelade nyckeln för att matcha CloudSimple VPN-gatewaynyckeln.
* Lokal identifiering. Ange den offentliga IP-adressen för den lokala Palo Alto-brandväggen.
* Peer-identifiering. Ange den primära CloudSimple VPN-peer-IP-adressen.

Fliken Avancerade alternativ:

* Aktivera passivt läge. Lämna alternativet omarkerat.
* Aktivera NAT Traversal. Lämna avmarkerat om den lokala Palo Alto-brandväggen inte ligger bakom någon NAT-enhet. Annars markerar du kryssrutan.

IKEv1:

* Exchange-läge. Välj **huvud**.
* IKE Crypto-profil. Välj den IKE Crypto-profil som du skapade tidigare. Låt rutan Aktivera fragmentering vara markerad.
* Död peer-upptäckt. Lämna rutan avmarkerad.

Upprepa föregående steg för att skapa den sekundära IKE-gatewayen.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Definiera IPSEC Crypto-profiler

Välj **Nätverk** > **Expandera nätverksprofiler** > **IPSEC Crypto** > **Add**, konfigurera följande fält och klicka på **OK**.

* Namn. Ange ett namn för IPsec-kryptoprofilen.
* IPsec-protokollet. Välj **ESP**.
* Kryptering. Klicka på **Lägg till** och välj lämplig krypteringsmetod.
* Autentisering. Klicka på **Lägg till** och välj lämplig autentiseringsmetod.
* DH-gruppen. Välj **no-pfs**.
* Livslängd. Ställ in som 30 minuter.
* Aktivera. Lämna rutan avmarkerad.

Upprepa föregående steg för att skapa en annan IPsec-kryptoprofil, som kommer att användas för som sekundär CloudSimple VPN-peer. Samma IPSEC Crypto-profil kan också användas för både primära och sekundära IPsec-tunnlar (se följande procedur).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Definiera bildskärmsprofiler för tunnelövervakning

Välj **Nätverksut** > **expandera nätverksprofiler** > **Övervaka** > **Lägg till,** konfigurera följande fält och klicka på **OK**.

* Namn. Ange alla namn på monitorprofilen som ska användas för tunnelövervakning för proaktiv reaktion på felet.
* Åtgärder. Välj **Växla över**.
* Intervall. Ange värdet **3**.
* Threshold (Faktisk kostnad jämfört med tröskelvärde). Ange värdet **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Ställ in primära och sekundära IPsec-tunnlar.

Välj > **Nätverks-IPsec-tunnlar** > **Lägg till**, konfigurera följande fält och klicka på **OK**. **Network**

Fliken Allmänt:

* Namn. Ange ett namn för den primära IPSEC-tunneln som ska peered med primär CloudSimple VPN-peer.
* Tunnelgränssnitt. Välj det primära tunnelgränssnittet.
* Typ. Låt standardvärdet vara kvar.
* Adresstyp. Välj **IPv4**.
* IKE Gateway. Välj den primära IKE-gatewayen.
* IPsec Crypto-profil. Välj den primära IPsec-profilen. Välj **Visa avancerade alternativ**.
* Aktivera återuppspelningsskydd. Låt standardvärdet vara kvar.
* Kopiera TOS-huvudet. Lämna rutan avmarkerad.
* Tunnelmonitor. Markera rutan.
* Destination IP. Ange alla IP-adresser som tillhör undernätet CloudSimple Private Cloud som är tillåtet via anslutning mellan plats och plats. Se till att tunnelgränssnitten (till exempel tunnel.20 - 10.64.5.2/32 och tunnel.30 - 10.64.6.2/32) på Palo Alto tillåts nå CloudSimple Private Cloud IP-adressen via VPN-adressen plats till plats. Se följande konfiguration för proxy-ID: er.
* Profil. Välj bildskärmsprofilen.

Fliken Proxy-ID: Klicka på Lägg**till** **IPv4** > och konfigurera följande:

* Proxy-ID. Ange vilket namn som helst för den intressanta trafiken. Det kan finnas flera proxy-ID:er som finns inuti en IPsec-tunnel.
* Lokala. Ange lokala lokala undernät som får kommunicera med privata molnundernät via VPN-platsen för plats.
* Fjärr. Ange fjärrundernäten Private Cloud som tillåts kommunicera med de lokala undernäten.
* Protokollet. Välj **en**.

Upprepa föregående steg för att skapa en annan IPsec-tunnel som ska användas för den sekundära CloudSimple VPN-peer.Repeat the previous steps to create another IPsec tunnel to use for the secondary CloudSimple VPN peer.

## <a name="references"></a>Referenser

Konfigurera NAT på Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Konfigurationsguide för Cisco ASA 5500-serien</a>

IKEv1- och IKEv2-attribut som stöds på Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Konfigurationsguide för Cisco ASA-serien</a>

Konfigurera IPsec Site-to-Site VPN på Cisco ASA med version 8.4 och senare:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Konfigurera IKEv1 IPsec-plats-till-plats-tunnlar med ASDM eller CLI på ASA</a>

Konfigurera virtuella Cisco Adaptive Security Appliance (ASAv) på Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Snabbstartsguide för Cisco Adaptive Security Virtual Appliance (ASAv)</a>

Konfigurera VPN från plats till plats med proxy-ID:er på Palo Alto:

[Konfigurera VPN från plats till plats](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Ställa in tunnelmonitor:

[Konfigurera tunnelövervakning](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE gateway- eller IPsec-tunnelåtgärder:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Aktivera/inaktivera, uppdatera eller starta om en IKE-gateway eller IPsec-tunnel</a>
