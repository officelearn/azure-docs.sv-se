---
title: Felsöka problem med peering i virtuella nätverk
description: Steg för att lösa de flesta peering-problem med virtuella nätverk.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc01a20a077a1c624ed490600db919fe3197556c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901768"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Felsöka problem med peering i virtuella nätverk

Den här fel söknings guiden innehåller steg som hjälper dig att lösa de flesta problem med [peering i virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

![AVBILDNING](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>Scenario 1: Konfigurera virtuell nätverks-peering mellan två virtuella nätverk

Är de virtuella nätverken i samma prenumeration eller i olika prenumerationer?

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>Anslutnings typ 1: De virtuella nätverken finns i samma prenumeration

Om du vill konfigurera peering för virtuella nätverk för de virtuella nätverk som finns i samma prenumeration använder du de metoder som finns i följande artiklar, efter behov:

* Om de virtuella nätverken finns i **samma region**följer du stegen för att [skapa en peering för virtuella nätverk i samma prenumeration](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Om de virtuella nätverken finns i de **olika regionerna**följer du stegen för att ställa in [Global peering för virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).  

> [!Note]
> Anslutningarna fungerar inte över global VNet-peering för följande resurser: 
>
> * Virtuella datorer bakom Basic ILB SKU
> * Redis Cache (använder Basic ILB SKU)
> * Application Gateway (använder Basic ILB SKU)
> * Skalnings uppsättningar (använder Basic ILB SKU)
> * Service Fabric kluster (använder Basic ILB SKU)
> * SQL Always On (använder Basic ILB SKU)
> * App Service miljöer (ASE) (använder Basic ILB SKU)
> * API Management (använder Basic ILB SKU)
> * Azure Active Directory domän tjänst (tillägg) (använder Basic ILB SKU)

Mer information finns i [krav och begränsningar](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) för global peering.

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>Anslutnings typ 2: De virtuella nätverken finns i olika prenumerationer eller AD-klienter

Om du vill konfigurera virtuell nätverks-peering för virtuella nätverk i olika prenumerationer eller Active Directory klienter följer du stegen i [skapa peering i olika prenumerationer för Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Om du vill konfigurera nätverks-peering måste du ha behörighet som **nätverks deltagare** i båda prenumerationerna. Mer information finns i [peering-behörigheter](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering%23permissions).

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Scenario 2: Konfigurera peering för virtuellt nätverk med nav-eker-topologi som använder lokala resurser

![AVBILDNING](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>Anslutnings typ 1: För plats-till-plats-anslutning eller ExpressRoute-anslutning

Följ stegen i: [Konfigurera överföring av VPN-gateway för peering av virtuella nätverk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="connection-type-2-for-point-to-site-connections"></a>Anslutnings typ 2: För punkt-till-plats-anslutningar

1. Följ stegen i: [Konfigurera överföring av VPN-gateway för peering av virtuella nätverk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. När peering av virtuella nätverk har upprättats eller ändrats måste punkt-till-plats-paketet hämtas och installeras igen för att punkt-till-plats-klienter ska kunna hämta de uppdaterade vägarna till det virtuella nätverket eker.

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>Scenario 3: Konfigurera virtuell nätverks-peering med nav-eker-topologi för Azure Virtual Network

![AVBILDNING](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>Anslutnings typ 1: De virtuella nätverken finns i samma region

Du måste konfigurera en virtuell nätverks installation (NVA) i det virtuella hubb nätverket och ha användardefinierade vägar med nästa hopp "virtuell nätverks installation" som används i ekrarnas virtuella nätverk. Mer information finns i [tjänst länkning](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Om du behöver hjälp med att konfigurera en NVA [kontaktar du NVA-leverantören](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Information om hur du felsöker NVA enhets konfiguration och routning finns i [problem med virtuella nätverks installationer i Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>Anslutnings typ 2: De virtuella nätverken finns i olika regioner

Överföring över global VNet-peering stöds nu. Anslutningen fungerar inte över global VNet-peering för följande resurser:

* Virtuella datorer bakom Basic ILB SKU
* Redis Cache (använder Basic ILB SKU)
* Application Gateway (använder Basic ILB SKU)
* Skalnings uppsättningar (använder Basic ILB SKU)
* Service Fabric kluster (använder Basic ILB SKU)
* SQL Always On (använder Basic ILB SKU)
* App Service miljöer (ASE) (använder Basic ILB SKU)
* API Management (använder Basic ILB SKU)
* Azure Active Directory domän tjänst (tillägg) (använder Basic ILB SKU)

Mer information om globala peering-krav och begränsningar finns i det [virtuella nätverkets peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>Scenario 4: Jag har ett anslutnings problem mellan två peer-anslutna virtuella nätverk

Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som har de [roller och behörigheter](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)som krävs. Välj det virtuella nätverket, Välj **peering**och kontrol lera sedan fältet **status** . Vad är statusen?

### <a name="connection-type-1-the-peering-status-shows-connected"></a>Anslutnings typ 1: Peering-status visar "ansluten"

Följ dessa steg för att felsöka problemet:

1. Kontrol lera flödena för nätverks trafik:

   Använd [anslutnings fel sökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) och [IP-flöde verifiera](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) från den virtuella käll datorn till den virtuella mål datorn för att avgöra om det finns en NSG eller UDR som orsakar störningar i trafikflöden.

   Följ dessa steg om du använder en brand vägg eller NVA-installation: 
   1. Dokumentera UDR-parametrarna så att du kan återställa dem när det här steget har slutförts.
   2. Ta bort UDR från det virtuella käll-eller NIC-nätverket som pekar på NVA som nästa hopp. Verifiera anslutningen från den virtuella käll datorn direkt till målet som kringgår NVA. Om det här steget fungerar kan du läsa mer i [NVA-felsökaren](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Ta en nätverks spårning: 
   1. Starta en nätverks spårning på den virtuella mål datorn. För Windows kan du använda **netsh**. För Linux använder du **TCPDump**.
   2. Kör **TcpPing** eller **PsPing** från källan till målets IP-adress.

   * Detta är ett exempel på ett **TcpPing** -kommando:`tcping64.exe -t <destination VM address> 3389`

   3. Stoppa nätverks spårningen på målet när **TcpPing** är klar.
   4. Om paketen kommer från källan finns det inga nätverks problem. Undersök både VM-brandväggen och programmet som lyssnar på den porten för att hitta konfigurations problemet.

   > [!Note]
   > Du kan inte ansluta till följande resurs typer över global peering för virtuella nätverk (virtuella nätverk i olika regioner):
   >
   > * Virtuella datorer bakom Basic ILB SKU
   > * Redis Cache (använder Basic ILB SKU)
   > * Application Gateway (använder Basic ILB SKU)
   > * Skalnings uppsättningar (använder Basic ILB SKU)
   > * Service Fabric kluster (använder Basic ILB SKU)
   > * SQL Always On (använder Basic ILB SKU)
   > * App Service miljöer (ASE) (använder Basic ILB SKU)
   > * API Management (använder Basic ILB SKU)
   > * Azure Active Directory domän tjänst (tillägg) (använder Basic ILB SKU)

Mer information finns i [krav och begränsningar](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) för global peering.

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>Anslutnings typ 2: Peering-statusen visar frånkopplad

Du måste ta bort peer kopplingar från både virtuella nätverk och återskapa dem.

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>Scenario 5: Jag har ett anslutnings problem mellan ett nav-eker-virtuellt nätverk och en lokal resurs

Använder du en NVA-eller VPN-gateway från tredje part?

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Anslutnings typ 1: Mitt nätverk använder en NVA-eller VPN-gateway från tredje part

Information om hur du felsöker anslutnings problem som påverkar en NVA eller VPN-gateway från tredje part finns i följande artiklar:

* [NVA-felsökare](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Tjänst länkning](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>Anslutnings typ 2: Mitt nätverk inte är en NVA-eller VPN-gateway från tredje part

Har både hubben hubb och eker virtuella nätverk en VPN-gateway?

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>Både hubben och ekrarna virtuella nätverk har en VPN-gateway

Det finns inte stöd för att använda en fjärr-Gateway.

På grund av en begränsning för VNet-peering stöds inte **fjärr-Gateway** på det virtuella eker-nätverket om det redan finns en VPN-gateway i eker VNet.

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>Både hubben och ekrarna virtuella nätverk har ingen VPN-gateway

För plats-till-plats-eller ExpressRoute-anslutningar kontrollerar du dessa primära orsaker till anslutnings problem till det virtuella fjärrnätverket från den lokala servern.

* Kontrol lera att kryss rutan **Tillåt vidarebefordrad trafik** är markerad i det virtuella nätverk som har en gateway.
* Kontrol lera att kryss rutan **Använd fjärr-Gateway** är markerad på det virtuella nätverk som inte har någon Gateway.
* Be nätverks administratören kontrol lera dina lokala enheter för att kontrol lera att alla har det fjärranslutna virtuella nätverkets adress utrymme tillagt.

För punkt-till-plats-anslutningar:

* Kontrol lera att kryss rutan **Tillåt vidarebefordrad trafik** är markerad i det virtuella nätverk som har en gateway.
* Kontrol lera att kryss rutan **Använd fjärr-Gateway** är markerad på det virtuella nätverk som inte har någon Gateway.
* Hämta och installera klient paketet punkt-till-plats igen. Virtuella nätverks vägar som nyligen peer-kopplas lägger inte automatiskt till vägar till punkt-till-plats-klienter.

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Scenario 6: Jag har ett problem med nätverks anslutningen mellan ekrar och ekrar mellan ekrar och virtuella nätverk i samma region

Du måste ha en NVA i ett nav nätverk, konfigurera UDR i ekrar som har en NVA angiven som nästa hopp och aktivera **Tillåt vidarebefordrad trafik** i det virtuella hubb nätverket.

Mer information finns i [tjänst länkning](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)och diskutera dessa krav med den [NVA-leverantör](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) som du väljer.

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Scenario 7: Jag har ett problem med nätverks anslutningen mellan ekrar och ekrar mellan ekrar och virtuella nätverk i olika regioner

Överföring över global VNet-peering stöds nu. Anslutningarna fungerar inte över global VNet-peering för följande resurser:

* Virtuella datorer bakom Basic ILB SKU
* Redis Cache (använder Basic ILB SKU)
* Application Gateway (använder Basic ILB SKU)
* Skalnings uppsättningar (använder Basic ILB SKU)
* Service Fabric kluster (använder Basic ILB SKU)
* SQL Always On (använder Basic ILB SKU)
* App Service miljöer (ASE) (använder Basic ILB SKU)
* API Management (använder Basic ILB SKU)
* Azure Active Directory domän tjänst (tillägg) (använder Basic ILB SKU)

Mer information finns i [krav och begränsningar](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) för global peering och [olika VPN-topologier](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Scenario 8: Jag har ett nav-eker problem med nätverks anslutningen mellan en webbapp och det virtuella nätverket i eker

Följ dessa steg för att felsöka problemet:

1. Logga in på Azure Portal. Gå till webbappen, Välj **nätverk**och välj sedan VNet- **integrering**.
2. Kontrol lera om du kan se det virtuella fjärrnätverket. Ange det fjärranslutna virtuella nätverkets adress utrymme manuellt (**Synkronisera nätverk** och **Lägg till vägar**).

Mer information finns i följande artiklar:

* [Integrera din app med en Azure-Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Om punkt-till-plats-VPN-routning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>Scenario 9: Jag får ett fel meddelande när det virtuella nätverkets peering konfigureras

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Fel 1: Den aktuella `<TENANT ID>` klienten har inte åtkomst behörighet till den länkade prenumerationen

Lös problemet genom att följa stegen i [skapa peering – Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="error-2-not-connected"></a>Fel 2: Ej ansluten

Du måste ta bort peer kopplingar från både virtuella nätverk och återskapa dem.

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>Fel 3: Det gick inte att peer-koppla ett Databricks virtuellt nätverk

Lös problemet genom att konfigurera det virtuella nätverkets peering från bladet **Azure Databricks** och ange det virtuella mål nätverket med hjälp av **resurs-ID**. Mer information finns i [peer a Databricks Virtual Network to a Remote Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Nästa steg

* [Felsökning av anslutningsproblem mellan virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)