---
title: Felsöka problem med peering i virtuella nätverk
description: Åtgärder för att lösa de flesta problem med virtuella nätverks peering.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 662619e101b45d1dd8b34ea97e31f214b254124a
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521874"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Felsöka problem med peering i virtuella nätverk

Den här felsökningsguiden innehåller steg som hjälper dig att lösa de flesta problem [med virtuella nätverks peering.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)

![Diagram över virtuell nätverks peering](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Konfigurera virtuell nätverks peering mellan två virtuella nätverk

Finns de virtuella nätverken i samma prenumeration eller i olika prenumerationer?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>De virtuella nätverken har samma prenumeration

Om du vill konfigurera virtuell nätverks peering för de virtuella nätverk som finns i samma prenumeration använder du metoderna i följande artiklar:

* Om de virtuella nätverken finns i *samma region*läser du [Skapa en peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Om de virtuella nätverken finns i de *olika regionerna*läser du [Virtuell nätverks peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 

> [!Note]
> Anslutningen fungerar inte över global virtuell nätverks peering för följande resurser: 
>
> * Virtuella datorer (VMs) bakom Grundläggande intern belastningsutjämnare (ILB) SKU
> * Redis-cache (använder Basic ILB SKU)
> * Programgateway (använder Basic ILB SKU)
> * Skaluppsättningar för virtuella datorer (använder Basic ILB SKU)
> * Azure Service Fabric-kluster (använder Basic ILB SKU)
> * SQL Server always on (använder basic ILB SKU)
> * Azure App Service Environment for PowerApps (använder Basic ILB SKU)
> * Azure API Management (använder Basic ILB SKU)
> * Azure Active Directory Domain Services (Azure AD DS) (använder Basic ILB SKU)

Mer information finns i [kraven och begränsningarna för](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) global peering.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>De virtuella nätverken finns i olika prenumerationer eller Active Directory-klienter

Information om hur du konfigurerar virtuell nätverks peering för virtuella nätverk i olika prenumerationer eller Active Directory-klienter finns [i Skapa peering i olika prenumerationer för Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Om du vill konfigurera nätverks peering måste du ha behörighet **för nätverksdeltagare** i båda prenumerationerna. Mer information finns i [Peering-behörigheter](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Konfigurera virtuell nätverks peering med hub-spoke-topologi som använder lokala resurser

![Diagram över virtuell nätverks peering med lokal eker](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>För en plats-till-plats-anslutning eller en ExpressRoute-anslutning

Följ stegen i: [Konfigurera VPN-gatewaytransitera för virtuell nätverks peering](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="for-point-to-site-connections"></a>För point-to-site-anslutningar

1. Följ stegen i: [Konfigurera VPN-gatewaytransitera för virtuell nätverks peering](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. När virtuell nätverks peering har upprättats eller ändrats hämtar och installerar du om point-to-site-paketet så att point-to-site-klienterna får de uppdaterade vägarna till det virtuella ekrarna.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Konfigurera virtuell nätverks peering med virtuellt nätverk för hub-spoke-topologi

![Diagram över virtuell nätverks peering med ett virtuellt nätverk eker](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>De virtuella nätverken finns i samma region


1. Konfigurera en virtuell nätverksinstallation (NVA) i det virtuella navnätverket.
1. I eker virtuella nätverk, har användardefinierade vägar med nästa hopp typ "nätverk virtuell installation" tillämpas.

Mer information finns i [Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Om du behöver hjälp med att konfigurera en NVA [kontaktar du NVA-leverantören](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Mer information om felsökning av NVA-enhetskonfiguration och routning finns [i Problem med virtuella nätverksinstallationer i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="the-virtual-networks-are-in-different-regions"></a>De virtuella nätverken finns i olika regioner

Transit över global virtuell nätverks peering stöds nu. Anslutningen fungerar inte över global virtuell nätverks peering för följande resurser:

* Virtuella datorer bakom Basic ILB SKU
* Redis-cache (använder Basic ILB SKU)
* Programgateway (använder Basic ILB SKU)
* Skalningsuppsättningar (använder Basic ILB SKU)
* Service Fabric-kluster (använder Basic ILB SKU)
* SQL Server always on (använder basic ILB SKU)
* App Service Environment (använder Basic ILB SKU)
* API-hantering (använder Grundläggande ILB SKU)
* Azure AD DS (använder Basic ILB SKU)

Mer information om globala peering-krav och begränsningar finns i [Virtual Network peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Felsöka ett anslutningsproblem mellan två peer-in-virtuella nätverk

Logga in på [Azure-portalen](https://portal.azure.com/) med ett konto som har nödvändiga [roller och behörigheter](virtual-network-manage-peering.md#permissions). Markera det virtuella nätverket, välj **Peering**och kontrollera sedan **fältet Status.** Vad är status?

### <a name="the-peering-status-is-connected"></a>Peering-statusen är "Ansluten"

Så här felsöker du problemet:

1. Kontrollera nätverkstrafikflödena:

   Använd [Connection Felsök och](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) [IP-flöde verifiera](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) från källan VM till målet VM för att avgöra om det finns en NSG eller UDR som orsakar störningar i trafikflöden.

   Om du använder en brandvägg eller NVA: 
   1. Dokumentera UDR-parametrarna så att du kan återställa dem när det här steget är klart.
   2. Ta bort UDR från käll-VM-undernätet eller nätverkskortet som pekar på NVA som nästa hopp. Verifiera anslutningen från källdatorn direkt till målet som kringgår NVA. Om det här steget inte fungerar läser [du felsökaren för NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Ta en nätverksspårning: 
   1. Starta en nätverksspårning på måldatorn. För Windows kan du använda **Netsh**. För Linux använder du **TCPDump**.
   2. Kör **TcpPing** eller PsPing från källan till mål-IP.Run TcpPing or **PsPing** from the source to the destination IP.

      Det här är ett exempel på ett **TcpPing-kommando:**`tcping64.exe -t <destination VM address> 3389`

   3. När **TcpPing** är klar stoppar du nätverksspårningen på målet.
   4. Om paket kommer från källan finns det inget nätverksproblem. Undersök både VM-brandväggen och programlyssningen på den porten för att hitta konfigurationsproblemet.

   > [!Note]
   > Du kan inte ansluta till följande resurstyper via global virtuell nätverks peering (virtuella nätverk i olika regioner):
   >
   > * Virtuella datorer bakom Basic ILB SKU
   > * Redis-cache (använder Basic ILB SKU)
   > * Programgateway (använder Basic ILB SKU)
   > * Skalningsuppsättningar (använder Basic ILB SKU)
   > * Service Fabric-kluster (använder Basic ILB SKU)
   > * SQL Server always on (använder basic ILB SKU)
   > * App Service Environment (använder Basic ILB SKU)
   > * API-hantering (använder Grundläggande ILB SKU)
   > * Azure AD DS (använder Basic ILB SKU)

Mer information finns i [kraven och begränsningarna för](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) global peering.

### <a name="the-peering-status-is-disconnected"></a>Peering-statusen är "Frånkopplad"

Lös problemet genom att ta bort peering-peering från båda virtuella nätverken och sedan återskapa dem.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Felsöka ett anslutningsproblem mellan ett virtuellt nätverk med navektal och en lokal resurs

Använder nätverket en NVA- eller VPN-gateway från tredje part?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Mitt nätverk använder en NVA- eller VPN-gateway från tredje part

Om du vill felsöka anslutningsproblem som påverkar en NVA- eller VPN-gateway från tredje part läser du följande artiklar:

* [FELSÖKARE FÖR NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Tjänstlänkning](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Mitt nätverk använder inte en NVA- eller VPN-gateway från tredje part

Har hubb virtuella nätverk och ekrarna virtuella nätverk har en VPN-gateway?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Både hubbvirt nätverk och ekrar virtuella nätverk har en VPN-gateway

Det går inte att använda en fjärrgateway.

Om det virtuella ekrarna redan har en VPN-gateway stöds inte alternativet **Använd fjärrgateway** i det virtuella nätverket. Detta beror på en begränsning av peering-nätverk.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Både det virtuella navnätverket och det virtuella nätverket spoke har ingen VPN-gateway

För plats-till-plats- eller Azure ExpressRoute-anslutningar kontrollerar du följande primära orsaker till anslutningsproblem till fjärrvirtiska nätverket från lokala:

* Kontrollera att kryssrutan **Tillåt vidarebefordrad trafik** är markerad i det virtuella nätverket som har en gateway.
* Kontrollera att kryssrutan **Använd fjärrgateway** är markerad i det virtuella nätverket som inte har någon gateway.
* Be nätverksadministratören kontrollera dina lokala enheter för att kontrollera att alla har lagt till adressutrymmet för fjärrresurser.

För point-to-site-anslutningar:

* Kontrollera att kryssrutan **Tillåt vidarebefordrad trafik** är markerad i det virtuella nätverket som har en gateway.
* Kontrollera att kryssrutan **Använd fjärrgateway** är markerad i det virtuella nätverket som inte har någon gateway.
* Hämta och installera om klientpaketet för punkt till plats. Virtuella nätverksvägar som nyligen peer-peer lägger inte automatiskt till vägar till point-to-site-klienter.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Felsöka ett nätverksanslutningsproblem mellan eker virtuella nätverk i samma region

Ett navnätverk måste innehålla en NVA. Konfigurera UDR:er i ekrar som har en NVA som nästa hopp och aktivera **Tillåt vidarebefordrad trafik** i det virtuella navnätverket.

Mer information finns i [Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)och diskutera dessa krav med den [NVA-leverantör](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) du väljer.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Felsöka ett nätverksanslutningsproblem mellan eker virtuella nätverk i olika regioner

Transit över global virtuell nätverks peering stöds nu. Anslutningen fungerar inte över global virtuell nätverks peering för följande resurser:

* Virtuella datorer bakom Basic ILB SKU
* Redis-cache (använder Basic ILB SKU)
* Programgateway (använder Basic ILB SKU)
* Skalningsuppsättningar (använder Basic ILB SKU)
* Service Fabric-kluster (använder Basic ILB SKU)
* SQL Server always on (använder basic ILB SKU)
* App Service Environment (använder Basic ILB SKU)
* API-hantering (använder Grundläggande ILB SKU)
* Azure AD DS (använder Basic ILB SKU)

Mer information finns i [kraven och begränsningarna för](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) global peering och olika VPN [Topologies](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Felsöka ett nätverksanslutningsproblem mellan en webbapp och det virtuella nätverket för e-valuta

Så här felsöker du problemet:

1. Logga in på Azure Portal. 
1. I webbappen väljer du **nätverk**och väljer sedan **VNet-integrering**.
1. Kontrollera om du kan se fjärrvirtualnätet. Ange manuellt adressutrymmet för fjärrnätverk (**Synkronisera nätverk** och Lägg **till rutter**).

Mer information finns i följande artiklar:

* [Integrera din app med ett virtuellt Azure-nätverk](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Om VPN-routning från punkt till plats](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Felsöka ett felmeddelande om konfiguration av virtuell nätverks peering 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Nuvarande `<TENANT ID>` klient har inte behörighet att komma åt länkad prenumeration

Information om problemet finns i [Skapa peering - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="not-connected"></a>Inte ansluten

LÃ¶s problemet genom att ta bort peering-pÃ¥ båda virtuella nätverken och återskapa dem sedan.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Det gick inte att peer a Databricks virtuella nätverk

LÃ¶s problemet genom att konfigurera den virtuella nätverks peering under **Azure Databricks**och ange sedan det virtuella målnätverket med hjälp av **Resource ID**. Mer information finns i [Peer a Databricks virtuella nätverk till ett virtuellt fjärrnätverk](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>Det virtuella fjärrnätverket saknar en gateway

Det här problemet uppstår när du peer virtuella `Use Remote Gateways`nätverk från olika klienter och senare vill konfigurera . En begränsning av Azure-portalen är att den inte kan validera närvaron av en virtuell nätverksgateway i en annan klients virtuella nätverk.

Det finns två sätt att lösa problemet:

 * Ta bort peerings `Use Remote Gateways` och aktivera alternativet när du skapar en ny peering.
 * Använd PowerShell eller CLI i stället för `Use Remote Gateways`Azure-portalen för att aktivera .

## <a name="next-steps"></a>Nästa steg

* [Felsökning av anslutningsproblem mellan virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
