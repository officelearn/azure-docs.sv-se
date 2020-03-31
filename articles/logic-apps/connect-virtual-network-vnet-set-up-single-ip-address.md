---
title: Konfigurera en offentlig utgående IP-adress för ISE:er
description: Lär dig hur du konfigurerar en enda offentlig utgående IP-adress för integrationstjänstmiljöer (ISE) i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191513"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Konfigurera en enda IP-adress för en eller flera integrationstjänstmiljöer i Azure Logic Apps

När du arbetar med Azure Logic Apps kan du konfigurera en [ *integrationstjänstmiljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) för att vara värd för logikappar som behöver åtkomst till resurser i ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). När du har flera ISE-instanser som behöver åtkomst till andra slutpunkter som har IP-begränsningar, distribuera en [Azure-brandvägg](../firewall/overview.md) eller en [virtuell nätverksinstallation](../virtual-network/virtual-networks-overview.md#filter-network-traffic) till ditt virtuella nätverk och dirigera utgående trafik genom brandväggen eller den virtuella nätverksinstallationen. Du kan sedan låta alla ISE-instanser i det virtuella nätverket använda en enda, offentlig, statisk och förutsägbar IP-adress för att kommunicera med målsystem. På så sätt behöver du inte ställa in ytterligare brandväggsöppningar på dessa målsystem för varje ISE.

Det här avsnittet visar hur du dirigerar utgående trafik via en Azure-brandvägg, men du kan använda liknande begrepp på en virtuell nätverksinstallation, till exempel en brandvägg från tredje part från Azure Marketplace. Även om det här avsnittet fokuserar på inställningar för flera ISE-instanser kan du också använda den här metoden för en enda ISE när ditt scenario kräver att begränsa antalet IP-adresser som behöver åtkomst. Fundera över om de extra kostnaderna för brandväggen eller den virtuella nätverksinstallationen är meningsfulla för ditt scenario. Läs mer om [prissättning av Azure-brandväggar](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Krav

* En Azure-brandvägg som körs i samma virtuella nätverk som din ISE. Om du inte har en brandvägg lägger du först `AzureFirewallSubnet` till ett [undernät](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) som namnges i det virtuella nätverket. Du kan sedan [skapa och distribuera en brandvägg](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) i det virtuella nätverket.

* En [Azure-vägtabell](../virtual-network/manage-route-table.md). Om du inte har någon skapar du först [en rutttabell](../virtual-network/manage-route-table.md#create-a-route-table). Mer information om routning finns i [Routning av virtuell nätverkstrafik](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Ställ in flödestabell

1. I [Azure-portalen](https://portal.azure.com)väljer du vägtabellen, till exempel:

   ![Välj rutttabell med regel för att styra utgående trafik](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Om du vill [lägga till en ny rutt](../virtual-network/manage-route-table.md#create-a-route)väljer du **Rutter** > **Lägg till**på rutttabellmenyn .

   ![Lägg till rutt för att dirigera utgående trafik](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. I **fönstret Lägg till flöde** [ställer du in den nya vägen](../virtual-network/manage-route-table.md#create-a-route) med en regel som anger att all utgående trafik till målsystemet följer detta:

   * Använder den [**virtuella installationen**](../virtual-network/virtual-networks-udr-overview.md#user-defined) som nästa hopptyp.

   * Går till den privata IP-adressen för brandväggsinstansen som nästa hoppadress.

     Om du vill hitta den här IP-adressen väljer du **Översikt**på brandväggsmenyn och letar reda på adressen under **Privat IP-adress,** till exempel:

     ![Hitta privat IP-adress för brandvägg](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Här är ett exempel som visar hur en sådan regel kan se ut:

   ![Ställ in regel för att dirigera utgående trafik](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Vägnamn** | <*unikt-ruttnamn*> | Ett unikt namn för rutten i flödestabellen |
   | **Adressprefix** | <*destination-adress*> | Målsystemets adress dit du vill att trafiken ska gå. Kontrollera att du använder [CIDR-notation (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) för den här adressen. |
   | **Nexthop-typ** | **Virtuell installation** | [Hopptypen](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) som används av utgående trafik |
   | **Nexthop-adress** | <*brandvägg-privat-IP-adress*> | Den privata IP-adressen för din brandvägg |
   |||

## <a name="set-up-network-rule"></a>Konfigurera nätverksregel

1. Leta reda på och välj brandvägg i Azure-portalen. Välj **Regler**under **Inställningar**på brandväggsmenyn . I regelfönstret väljer du **Nätverksregelsamling** > **Lägg till nätverksregelsamling**.

   ![Lägga till samling av nätverksregel i brandväggen](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Lägg till en regel som tillåter trafik till målsystemet i samlingen.

   Anta till exempel att du har en logikapp som körs i en ISE och måste kommunicera med ett SFTP-system. Du skapar en nätverksregelsamling `LogicApp_ISE_SFTP_Outbound`som heter , som `ISE_SFTP_Outbound`innehåller en nätverksregel med namnet . Den här regeln tillåter trafik från IP-adressen för alla undernät där DIN ISE körs i ditt virtuella nätverk till målet SFTP-systemet med hjälp av brandväggens privata IP-adress.

   ![Konfigurera nätverksregel för brandvägg](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Egenskaper för samling av nätverksregel**

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*namn på nätverksregelsamling*> | Namnet på nätverksregelsamlingen |
   | **Prioritet** | <*prioritetsnivå*> | Prioritetsordningen som ska användas för att köra regelsamlingen. Mer information finns i [Vilka azure-brandväggsbegrepp finns?](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts) |
   | **Åtgärd** | **Tillåt** | Åtgärdstypen som ska utföras för den här regeln |
   |||

   **Egenskaper för nätverksregel**

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*nätverksregelnamn*> | Namnet på nätverksregeln |
   | **Protokollet** | <*anslutningsprotokoll*> | De anslutningsprotokoll som ska användas. Om du till exempel använder NSG-regler väljer du både **TCP** och **UDP**, inte bara **TCP**. |
   | **Källadresser** | <*ISE-undernät-adresser*> | IP-adresserna för undernätet där DIN ISE körs och där trafik från logikappen kommer |
   | **Måladresser** | <*destination-IP-adress*> | IP-adressen för ditt målsystem där du vill att trafiken ska gå |
   | **Målportar** | <*destination-portar*> | Alla portar som målsystemet använder för inkommande kommunikation |
   |||

   Mer information om nätverksregler finns i följande artiklar:

   * [Konfigurera en nätverksregel](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Regelbearbetningslogik för Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Vanliga frågor och svar om Azure-brandväggen](../firewall/firewall-faq.md)
   * [Azure PowerShell: Ny-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: nätverksregel för az-nätverksbrandvägg](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)