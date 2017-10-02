---
title: "Peerkoppling i virtuella nätverk på Azure | Microsoft Docs"
description: "Lär dig mer om virtuell nätverkspeering i Azure."
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 082cd8a6cf50f76c89fe5995047396c734f83034
ms.contentlocale: sv-se
ms.lasthandoff: 09/25/2017

---
# <a name="virtual-network-peering"></a>Virtuell nätverkspeering

[Azure Virtual Network (VNet)](virtual-networks-overview.md) är ditt eget privata nätverksutrymme i Azure, där du på ett säkert sätt kan ansluta Azure-resurser till varandra.

Med VNET-peering kan du smidigt ansluta virtuella nätverk. När de virtuella nätverken har peer-kopplats visas de som ett nätverk för anslutningsändamål. De virtuella datorerna i de peer-kopplade virtuella nätverken kan kommunicera med varandra direkt.
Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras via Microsoft-stamnätsinfrastrukturen på nästan samma sätt som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk genom endast *privata* IP-adresser.

>[!IMPORTANT]
> Du kan peer-koppla virtuella nätverk i olika Azure-regioner. Den här funktionen är för närvarande en förhandsversion. Du kan [registrera din prenumeration för förhandsversionen](virtual-network-create-peering.md). Peering av virtuella nätverk i samma regioner är allmänt sett tillgängligt.
>

Fördelarna med att använda VNET-peering är:

* Trafik via Virtual Network-peering är helt privat. Den går via Microsoft-stamnätverk, utan inblandning av offentligt internet eller gateways.
* En anslutning med korta svarstider och hög bandbredd mellan resurser i olika virtuella nätverk.
* Möjlighet att använda resurser i ett virtuellt nätverk från ett annat virtuellt nätverk när det är peer-kopplat.
* Med Virtual Network-peering kan du överföra data mellan Azure-prenumerationer, distributionsmodeller och mellan Azure-regioner (förhandsversion).
* Möjligheten att peer-koppla virtuella nätverk som har skapats via Azure Resource Manager eller att peer-koppla ett virtuellt nätverk som har skapats via Resource Manager till ett virtuellt nätverk som har skapats via den klassiska distributionsmodellen. Läs artikeln [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Förstå Azure-distributionsmodellerna) om du vill ta reda på mer om skillnaderna mellan de två Azure-distributionsmodellerna.

## <a name="requirements-constraints"></a>Det finns vissa krav och begränsningar

* VNET-peering i samma region är allmänt sett tillgängligt. VNET-peering i olika regioner finns som förhandsversion i västra centrala USA, centrala Kanada och västra USA 2. Du kan [registrera din prenumeration för förhandsversionen](virtual-network-create-peering.md).
    > [!WARNING]
    > VNET-peering som skapats i det här scenariot kanske inte har samma tillgänglighet och pålitlighet som scenarier i en allmänt tillgänglig version. VNET-peering kan ha begränsad kapacitet och kanske inte är tillgänglig i alla Azure-regioner. Du hittar aktuell information om tillgänglighet och status för den här funktionen på [sidan med Azure Virtual Network-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

* Peerkopplade virtuella nätverk får inte ha överlappande IP-adressutrymmen.
* Adressutrymmen kan inte läggas till eller tas bort från ett virtuellt nätverk när ett virtuellt nätverk är peer-kopplat med ett annat virtuellt nätverk.
* Peer-kopplingarna i virtuella nätverk upprättas mellan två virtuella nätverk. Det finns ingen härledd transitiv relation mellan peer-kopplingar. Till exempel om virtualNetworkA är peer-kopplat med virtualNetworkB och virtualNetworkB är peer-kopplat med virtualNetworkC är virtualNetwork A *inte* peerkopplat till virtualNetworkC.
* Peerkoppling kan upprättas mellan virtuella nätverk i två olika prenumerationer under förutsättning att en privilegierad användare (se [speciella tillstånd](create-peering-different-deployment-models-subscriptions.md#permissions)) av båda prenumerationerna tillåter peerkopplingen, och prenumerationerna är kopplade till samma Azure Active Directory-klient. Du kan använda en [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) för att ansluta virtuella nätverk i prenumerationer som är kopplade till olika Active Directory-klienter.
* Virtuella nätverk kan peerkopplas om båda skapas via distributionsmodellen Resurshanteraren eller om det ena virtuella nätverket skapas via distributionsmodellen Resurshanteraren och det andra skapas via den klassiska distributionsmodellen. Virtuella nätverk som har skapats via den klassiska distributionsmodellen kan dock inte peer-kopplas till varandra. Du kan använda en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) för att ansluta virtuella nätverk som har skapats via den klassiska distributionsmodellen.
* Även om kommunikationen mellan virtuella datorer i peer-kopplade virtuella nätverk inte har ytterligare bandbreddsbegränsningar finns det en maximal nätverksbandbredd beroende på storleken på den virtuella datorn som fortfarande gäller. Om du vill veta mer om nätverkets maximala bandbredd för olika virtuella datorstorlekar läser du artiklarna om virtuella datorstorlekar i [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

     ![Grundläggande virtuell nätverkspeering](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>Anslutning

När virtuella nätverk har peer-kopplats kan resurser i de virtuella nätverken ansluta till resurser it det peer-kopplade virtuella nätverket.

Nätverksfördröjningen mellan virtuella datorer i peer-kopplade virtuella nätverk i samma region är densamma som den inom ett enda virtuellt nätverk. Nätverkets genomflöde baseras på den bandbredd som tillåts för den virtuella datorn i proportion till dess storlek. Det finns inte några ytterligare begränsning vad gäller bandbredden inom peerkopplingen.

Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras direkt genom Microsoft-stamnätsinfrastrukturen, inte via en gateway eller det offentliga internet.

Virtuella datorer i ett virtuellt nätverk har åtkomst till interna belastningsutjämnare i det peer-kopplade virtuella nätverket i samma region. Stödet för interna belastningsutjämnare gäller inte för globalt peer-kopplade virtuella nätverk i förhandsversion. Den allmänt tillgängliga versionen av global VNET-peering kommer att ha stöd för interna belastningsutjämnare.

Nätverkssäkerhetsgrupper kan användas i något av de virtuella nätverken för att blockera åtkomsten till andra virtuella nätverk eller till undernät om det behövs.
När du konfigurerar peering i virtuella nätverk, kan du öppna eller stänga av reglerna för nätverkssäkerhetsgrupper mellan virtuella nätverk. Om du väljer att öppna fullständiga anslutningar mellan peer-kopplade virtuella nätverk (vilket är standardalternativet) kan du sedan använda nätverkssäkerhetsgrupper för specifika undernät eller virtuella datorer för att blockera eller neka specifik åtkomst. Mer information om nätverkssäkerhetsgrupper finns i artikeln [Nätverkssäkerhetsöversikt](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Tjänstlänkning

Du kan konfigurera användardefinierade routningstabeller som pekar på virtuella datorer i peer-kopplade virtuella nätverk som ”nästa hopp”-IP-adressen för säker tjänstlänkning. Med säker tjänstlänkning kan du skapa en tjänstlänkning och därigenom dirigera trafik från ett virtuellt nätverk till en virtuell enhet som körs i ett peer-kopplat virtuellt nätverk genom användardefinierade routning.

Du kan även effektivt bygga nav-och-ekermiljöer där hubben kan vara värd för infrastrukturkomponenter, t.ex. en virtuell installation. Alla virtuella ekernätverk kan peer-kopplas till det virtuella navnätverket. Trafiken kan flöda via nätverkets virtuella installationer som körs i det virtuella navnätverket. I korthet gör VNet-peering att ”nästa hopp”-IP-adressen i den användardefinierade routningen kan vara IP-adressen för en virtuell dator i det peer-kopplade virtuella nätverket. Mer information om användardefinierade vägar finns i översiktsartikeln [Användardefinierade vägar](virtual-networks-udr-overview.md). Lär dig hur du skapar en [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)

## <a name="gateways-and-on-premises-connectivity"></a>Gateways och lokala anslutningar

Varje virtuellt nätverk, oavsett om det är peer-kopplat med ett annat virtuellt nätverk eller inte, kan fortfarande ha sin egen gateway och använda den för att ansluta till det lokala nätverket. Du kan också konfigurera [VNet-till-VNet-anslutningar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) genom att använda gateways, även om de virtuella nätverken är peer-kopplade.

När båda alternativen för anslutningar mellan virtuella nätverk har konfigurerats flödar trafiken mellan de virtuella nätverken genom peering-konfigurationen (det vill säga genom Azures stamnätverk).

När virtuella nätverk har peer-kopplats i samma region kan du också konfigurera gatewayen i det peer-kopplade virtuella nätverket som en överföringspunkt till det lokala nätverket. I detta fall kan det virtuella nätverket som använder en fjärr-gateway inte ha sin egen gateway. Ett virtuellt nätverk kan bara ha en gateway. Det kan antingen vara en lokal gateway eller en fjärr-gateway (i det peerkopplade virtuella nätverket), som du ser på följande bild:

![Överföring vid VNET-peering](./media/virtual-networks-peering-overview/figure04.png)

Gatewayöverföring stöds inte i peering-förhållandet mellan virtuella nätverk som har skapats via olika distributionsmodeller eller olika regioner. Båda de virtuella nätverken i peering-relationen måste ha skapats via Resource Manager och måste vara i samma region för att en gatewayöverföring ska fungera. Globalt peer-kopplade virtuella nätverk stöder för närvarande inte gatewayöverföring.

När de virtuella nätverken som delar samma Azure ExpressRoute-anslutning är peer-kopplade går trafiken mellan dem genom peering-relationen (dvs. genom Azures stamnätverk). Du kan fortfarande använda lokala gateways i varje virtuellt nätverk för att ansluta till den lokala kretsen. Du kan även använda en delad gateway och konfigurera överföringen för lokala anslutningar.

## <a name="permissions"></a>Behörigheter

Virtuell nätverkspeering är en privilegierad åtgärd. Det är en separat funktion under namnområdet VirtualNetworks. En användare kan beviljas specifika rättigheter för att tillåta peering. En användare som har skrivskyddad åtkomst till det virtuella nätverket ärver dessa rättigheter automatiskt.

En användare som antingen är en administratör eller en privilegierad användare av peering-funktionen kan initiera en peering-åtgärd i ett annat virtuellt nätverk. Den lägsta behörighetsnivån som krävs är Nätverksdeltagare. Om det finns en matchande begäran om peering på den andra sidan, och om andra krav är uppfyllda, så upprättas peer-kopplingen.

Exempel: Om du har peer-kopplat virtuella nätverk som heter myvirtual networkA och myvirtual networkB måste ditt konto tilldelas följande minimiroll eller -behörighet för varje virtuellt nätverk:

|Virtuellt nätverk|Distributionsmodell|Roll|Behörigheter|
|---|---|---|---|
|myvirtual networkA|Resource Manager|[Nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klassisk|[Klassisk nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Saknas|
|myvirtual networkB|Resource Manager|[Nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klassisk|[Klassisk nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>Övervaka

Vid peer-koppling av två virtuella nätverk som skapas via Resource Manager måste en peer-koppling konfigureras för varje virtuellt nätverk i peer-kopplingen.
Du kan övervaka statusen för peering-anslutningen. Peering-statusen vara någon av följande:

* **Initierad**: När du skapar peer-kopplingen till det andra virtuella nätverket från det första är peering-statusen Initierad.

* **Ansluten**: När du skapar peer-kopplingen från det andra virtuella nätverket till det första är peering-statusen Ansluten. Om du visar peering-statusen för det första virtuella nätverket ändras statusen från Initierad till Ansluten. Peer-kopplingen är inte upprättad förrän peering-statusen för båda virtuella nätverk är Ansluten.

* **Frånkopplad** : Om någon av peering-länkarna tas bort efter att en anslutning har upprättats är peering-statusen Frånkopplad.

## <a name="troubleshoot"></a>Felsöka

Om du vill felsöka trafiken som flödar i peering-anslutningen kan du [kontrollera dina gällande routningar.](virtual-network-routes-troubleshoot-portal.md)

Du kan även felsöka anslutningen till en virtuell dator i ett peer-kopplat virtuellt nätverk med Network Watchers [anslutningskontroll](../network-watcher/network-watcher-connectivity-portal.md). Med anslutningskontrollen ser du direkt routningen från käll-VM:s nätverksgränssnitt till mål VM:s nätverksgränssnitt.

## <a name="limits"></a>Begränsningar

Det finns begränsningar för antalet peering-sessioner som tillåts för ett enda virtuellt nätverk. Standardvärdet för antal peer-kopplingar är 50. Du kan öka antalet peer-kopplingar. Mer information finns i [Nätverksgränser i Azure](../azure-subscription-service-limits.md#networking-limits).

## <a name="pricing"></a>Prissättning

En nominell avgift tas ut för ingående och utgående trafik som använder en VNET-peeringanslutning. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Nästa steg

* Slutför självstudien om peering för virtuella nätverk. Peering för virtuellt nätverk skapas mellan virtuella nätverk som skapats via samma eller olika distributionsmodeller som finns i samma eller olika prenumerationer. Genomför en självstudiekurs för något av följande scenarier:

    |Azure-distributionsmodell  | Prenumeration  |
    |---------|---------|
    |Båda Resource Manager |[Samma](virtual-network-create-peering.md)|
    | |[Olika](create-peering-different-subscriptions.md)|
    |En Resource Manager, en klassisk     |[Samma](create-peering-different-deployment-models.md)|
    | |[Olika](create-peering-different-deployment-models-subscriptions.md)|

* Lär dig hur du skapar en [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
* Lär dig mer om alla [peering-inställningar för virtuella nätverk och hur du ändrar dem](virtual-network-manage-peering.md)

