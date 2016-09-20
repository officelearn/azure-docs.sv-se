
<properties
   pageTitle="Peer-koppling i virtuella nätverk i Azure | Microsoft Azure"
   description="Läs mer om VNet-peering (virtuella nätverk) i Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2016"
   ms.author="narayan" />

# VNet-peering

VNet-peering är en mekanism som ansluter två virtuella nätverk i samma region via Azures stamnätverk. När de två virtuella nätverken har peer-kopplats visas de som ett nätverk för alla anslutningsändamål. De hanteras fortfarande som separata resurser, men virtuella datorer i dessa virtuella nätverk kan kommunicera med varandra direkt med hjälp av privata IP-adresser.

Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras via Azure-infrastrukturen på nästan samma sätt som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk. Några av fördelarna med att använda VNet-peering är:

- En anslutning med korta svarstider och hög bandbredd mellan resurser i olika virtuella nätverk.
- Möjlighet att använda resurser, till exempel nätverksinstallationer och VPN-gateways som överföringspunkter i ett peer-kopplat VNet.
- Möjligheten att ansluta ett virtuellt nätverk som använder Azure Resource Manager-modellen till ett virtuellt nätverk som använder den klassiska distributionsmodellen och aktivera fullständiga anslutningar mellan resurser i dessa virtuella nätverk.

Krav och viktiga aspekter relaterade till VNet-peering:

- De två peer-kopplade virtuella nätverken måste finnas i samma Azure-region.
- De peer-kopplade virtuella nätverken får inte ha IP-adressintervall som överlappar varandra.
- VNet-peering sker mellan två virtuella nätverk, och det finns ingen härledd transitiv relation. Om det virtuella nätverket A exempelvis peer-kopplas med det virtuella nätverket B, och om det virtuella nätverket B peer-kopplas med det virtuella nätverket C, så leder inte detta till att det virtuella nätverket A peer-kopplas till det virtuella nätverket C.
- Peering kan upprättas mellan virtuella nätverk i två olika prenumerationer så länge en privilegierad användare av båda prenumerationerna tillåter peer-kopplingen.
- Ett virtuellt nätverk som använder Resource Manager-distributionsmodellen kan peer-kopplas med ett annat virtuellt nätverk som använder den här modellen eller med ett virtuellt nätverk som använder den klassiska distributionsmodellen. Virtuella nätverk som använder den klassiska distributionsmodellen kan dock inte peer-kopplas till varandra.
- Kommunikationen mellan virtuella datorer i peer-kopplade virtuella nätverk har inga ytterligare bandbreddsbegränsningar. Bandbreddsbegränsningen baseras fortfarande på den virtuella datorns storlek.


![Grundläggande VNet-peering](./media/virtual-networks-peering-overview/figure01.png)

## Anslutning
När två virtuella nätverk har peer-kopplats kan en virtuell dator (webb- eller arbetsroll) i det virtuella nätverket ansluta direkt till andra virtuella datorer i det peer-kopplade virtuella nätverket. Dessa två nätverk kan upprätta fullständiga anslutningar på IP-nivå.

Svarstiden i nätverk för kommunikation (tur och retur) mellan två virtuella datorer i peer-kopplade virtuella nätverk är samma som den i ett lokalt virtuellt nätverk. Nätverkets genomflöde baseras på den bandbredd som tillåts för den virtuella datorn i proportion till dess storlek. Det finns inte några ytterligare begränsning vad gäller bandbredden.

Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras direkt via Azures backend-infrastruktur och inte genom en gateway.

Virtuella datorer i ett virtuellt nätverk har åtkomst till slutpunkterna för interna belastningsutjämnare (ILB) i det peer-kopplade virtuella nätverket. Nätverkssäkerhetsgrupper (NSG) kan användas i något av de virtuella nätverken för att blockera åtkomsten till andra virtuella nätverk eller till undernät om det behövs.

När användare konfigurerar peering kan de antingen öppna eller stänga NSG-reglerna mellan de virtuella nätverken. Om användaren väljer att öppna fullständiga anslutningar mellan peer-kopplade virtuella nätverk (vilket är standardalternativet) kan de sedan använda nätverkssäkerhetsgrupper för specifika undernät eller virtuella datorer för att blockera eller neka specifik åtkomst.

Azures interna DNS-namnmatchning för virtuella datorer fungerar inte mellan peer-kopplade virtuella nätverk. Virtuella datorer har interna DNS-namn som bara matchas i det virtuella lokala nätverket. Användare kan dock konfigurera virtuella datorer som körs i peer-kopplade virtuella nätverk som DNS-servrar för ett virtuellt nätverk.

## Tjänstlänkning
Användarna kan konfigurera användardefinierade routningstabeller som pekar på virtuella datorer i peer-kopplade virtuella nätverk som ”nästa hopp”-IP-adressen, som du ser i diagrammet senare i den här artikeln. På så sätt kan användarna skapa en tjänstlänkning och därigenom dirigera trafik från ett virtuellt nätverk till en virtuell enhet som körs i ett peer-kopplat virtuellt nätverk genom användardefinierade routningstabeller.

Användarna kan även effektivt bygga nav-och-ekermiljöer där hubben kan vara värd för infrastrukturkomponenter, t.ex. en virtuell nätverksenhet. Alla dessa virtuella ekernätverk kan sedan peer-kopplas med den, liksom en delmängd av trafiken, till enheter som körs i hubbens virtuella nätverk. I korthet gör VNet-peering att ”nästa hopp”-IP-adressen i den användardefinierade routningstabellen kan vara IP-adressen för en virtuell dator i det peer-kopplade virtuella nätverket.

## Gateways och lokala anslutningar
Varje virtuellt nätverk, oavsett om det är peer-kopplat med ett annat virtuellt nätverk eller inte, kan fortfarande ha sin egen gateway och använda den för att ansluta till den lokala infrastrukturen. Användarna kan också konfigurera [VNet-till-VNet-anslutningar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) genom att använda gateways, även om de virtuella nätverken är peer-kopplade.

När båda alternativen för anslutningar mellan virtuella nätverk har konfigurerats flödar trafiken mellan de virtuella nätverken genom peering-konfigurationen (det vill säga genom Azures stamnätverk).

När virtuella nätverk har peer-kopplats kan användarna också konfigurera gatewayen i det peer-kopplade virtuella nätverket som en överföringspunkt till den lokala infrastrukturen. I detta fall kan det virtuella nätverket som använder en fjärr-gateway inte ha sin egen gateway. Ett virtuellt nätverk kan bara ha en gateway. Det kan antingen vara en lokal gateway eller en fjärr-gateway (i det peer-kopplade virtuella nätverket), som du ser i följande bild.

Gateway-överföring stöds inte i peering-relationen mellan virtuella nätverk som använder Resource Manager-modellen och de som använder den klassiska distributionsmodellen. Båda de virtuella nätverken i peering-relationen måste använda Resource Manager-distributionsmodellen för att en gateway-överföring ska fungera.

När de virtuella nätverken som delar samma Azure ExpressRoute-anslutning är peer-kopplade går trafiken mellan dem genom peering-relationen (dvs. genom Azures stamnätverk). Användarna kan fortfarande använda lokala gateways i varje virtuellt nätverk för att ansluta till den lokala kretsen. De kan också använda en delad gateway och konfigurera överföringen för lokala anslutningar.

![Överföring med VNet-peering](./media/virtual-networks-peering-overview/figure02.png)

## Etablering
VNet-peering är en privilegierad åtgärd. Det är en separat funktion under namnområdet VirtualNetworks. En användare kan beviljas specifika rättigheter för att tillåta peering. En användare som har skrivskyddad åtkomst till det virtuella nätverket ärver dessa rättigheter automatiskt.

En användare som antingen är en administratör eller en privilegierad användare av peering-funktionen kan initiera en peering-åtgärd i ett annat VNet. Om det finns en matchande begäran om peering på den andra sidan, och om andra krav är uppfyllda, så upprättas peer-kopplingen.

Läs artiklarna i avsnittet ”Nästa steg” om du vill veta mer om hur du etablerar VNet-peering mellan två virtuella nätverk.

## Begränsningar
Det finns begränsningar för antalet peering-sessioner som tillåts för ett enda virtuellt nätverk. Mer information finns i [Nätverksgränser i Azure](../azure-subscription-service-limits.md#networking-limits).

## Priser
VNet-peering är kostnadsfritt under granskningsperioden. När funktionen lanseras utgår en nominell kostnad för in- och utgående trafik som använder peering. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/virtual-network).


## Nästa steg
- [Konfigurera peering mellan virtuella nätverk](virtual-networks-create-vnetpeering-arm-portal.md).
- Läs mer om [NSG:er](virtual-networks-nsg.md).
- Läs mer om [användardefinierade vägar och IP-vidarebefordring](virtual-networks-udr-overview.md).



<!--HONumber=sep16_HO1-->


