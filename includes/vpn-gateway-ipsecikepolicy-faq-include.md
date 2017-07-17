### Stöds anpassade IPsec/IKE-principer på alla Azure VPN Gateway-SKU: er?
<a id="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus" class="xliff"></a>
Anpassade IPsec/IKE-principer stöds på Azure **VpnGw1, VpnGw2, VpnGw3, Standard** och **HighPerformance** VPN-gatewayer. **Basic** SKU stöds inte.

### Hur många principer kan jag ställa in för en anslutning?
<a id="how-many-policies-can-i-specify-on-a-connection" class="xliff"></a>
Du kan bara ange ***en*** principkombination för en viss anslutning.

### Kan jag ange en partiell princip på en anslutning? (T.ex. endast IKE-algoritmer men inte IPsec)
<a id="can-i-specify-a-partial-policy-on-a-connection-eg-only-ike-algorithms-but-not-ipsec" class="xliff"></a>
Nej, du måste ange alla algoritmer och parametrar för både IKE (huvudläge) och IPsec (snabbläge). Partiell principspecifikationen tillåts inte.

### Vilka är de algoritmer och viktiga fördelar som stöds i den anpassade principen?
<a id="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy" class="xliff"></a>
I tabellen nedan visas de kryptografiska algoritmer som stöds och viktiga fördelar som kan konfigureras av kunden. Du måste välja ett alternativ för varje fält.

| **IPsec/IKEv2**  | **Alternativ**                                                                 |
| ---              | ---                                                                         |
| IKEv2-kryptering | AES256, AES192, AES128, DES3, DES                                           |
| IKEv2 Integrity  | SHA384, SHA256, SHA1, MD5                                                   |
| DH-grupp         | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| IPsec-kryptering | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None    |
| IPsec Integrity  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                          |
| PFS-grupp        | ECP384, ECP256, PFS24, PFS2048, PFS14, PFS2, PFS1, None                     |
| QM SA-livstid*  | Sekunder (heltal; **min. 300**) och Kbyte (heltal; **min. 1024**)                                      |
| Trafikväljare | UsePolicyBasedTrafficSelectors ** ($True/$False; standard $False)                             |
|                  |                                                                             |

* (*) IKEv2 Main Mode SA har en livslängd på högst 28 800 sekunder på Azure VPN-gatewayer
* (**) Läs nästa fråga från vanliga frågor och svar för ”UsePolicyBasedTrafficSelectors”

### Behöver allt matcha mellan Azure VPN gateway-principen och mina lokala konfigurationer för VPN-enheter?
<a id="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations" class="xliff"></a>
Din lokala konfiguration för VPN-enheten måste stämma överens med eller innehålla följande algoritmer och parametrar som du anger på Azure IPsec/IKE-principen:

* IKE-krypteringsalgoritm
* IKE-integritetsalgoritm
* DH-grupp
* IPsec-krypteringsalgoritm
* IPsec-integritetsalgoritm
* PFS-grupp
* Trafikväljare (*)

SA-livstider är lokala specifikationer och behöver inte matcha.

Om du aktiverar **UsePolicyBasedTrafficSelectors**, måste du se till att din VPN-enhet har matchande trafikväljare som har definierats med alla prefixkombinationer i ditt lokala nätverk (lokal nätverksgateway) till eller från de virtuella Azure-nätverksprefixen, i stället för alla-till-alla. Om ditt prefix för det lokala nätverket är 10.1.0.0/16 och 10.2.0.0/16 och ditt prefix för det virtuella nätverket är 192.168.0.0/16 och 172.16.0.0/16, måste du ange följande trafik väljare:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

I [Ansluta till flera lokala principbaserade VPN-enheter](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) finns mer information om hur du använder det här alternativet.

### Ersätter den anpassade principen standardprincipuppsättningar för IPsec/IKE för Azure VPN-gatewayer?
<a id="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways" class="xliff"></a>
Ja, när en anpassad princip har angetts på en anslutning kommer Azure VPN-gatewayen bara använda principen på anslutningen, både som IKE-initierare och IKE-responder.

### Om jag tar bort en anpassad princip för IPsec/IKE, blir anslutningen mindre säker?
<a id="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected" class="xliff"></a>
Nej, anslutningen kommer att skyddas av IPsec/IKE. När du tar bort den anpassade principen från en anslutning återgår Azure VPN-gatewayen till [standardlistan med IPsec/IKE-förslag](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) och startar om IKE-handskakningen med din lokala VPN-enhet.

### Kan det störa VPN-anslutningen att lägga till eller uppdatera en IPsec/IKE-princip?
<a id="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection" class="xliff"></a>
Ja, det kan orsaka ett litet avbrott (några sekunder) när Azure VPN-gatewayen river upp den befintliga anslutningen och startar om IKE-handskakning för att återupprätta IPsec-tunneln med nya krypteringsalgoritmer och parametrar. Kontrollera att din lokala VPN-enhet också konfigureras med matchande algoritmer och viktiga styrkor för att minimera störningar.

### Kan jag använda olika principer för olika anslutningar?
<a id="can-i-use-different-policies-on-different-connections" class="xliff"></a>
Ja. Anpassade principer tillämpats på per-anslutningbasis. Du kan skapa och tillämpa olika IPsec/IKE-principer på olika anslutningar. Du kan också välja att använda anpassade principer för en delmängd av anslutningar. De återstående kommer endast att använda Azure standard IPsec/IKE-principuppsättningar.

### Kan jag använda den anpassade principen på VNet-till-VNet-anslutningen?
<a id="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well" class="xliff"></a>
Ja, du kan använda anpassade principen på både IPSec-anslutningar mellan lokala anslutningar eller VNet-till-VNet-anslutningar.

### Behöver jag ange samma princip på båda resurserna för VNet-till-VNet-anslutningen?
<a id="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources" class="xliff"></a>
Ja. En VNet-till-VNet-tunnel består av två anslutningsresurser i Azure, en för varje riktning. Du måste se till att båda anslutningsresurserna har samma princip, annars går det inte att upprätta VNet-till-VNet-anslutningen.

### Fungerar en anpassad IPsec/IKE-princip på ExpressRoute-anslutningen?
<a id="does-custom-ipsecike-policy-work-on-expressroute-connection" class="xliff"></a>
Nej. IPSec-/ princip fungerar bara på S2S VPN- och VNet-till-VNet-anslutningar via Azure VPN-gatewayer.
