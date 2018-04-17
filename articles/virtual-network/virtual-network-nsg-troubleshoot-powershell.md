---
title: Felsöka Nätverkssäkerhetsgrupper - PowerShell | Microsoft Docs
description: Lär dig hur du felsöker Nätverkssäkerhetsgrupper i Azure Resource Manager-distributionsmodellen med hjälp av Azure PowerShell.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 6beaeb4b7e5c9e393427d575f1cf8bc48599dbd5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Felsöka Nätverkssäkerhetsgrupper med hjälp av Azure PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Om du konfigurerade Nätverkssäkerhetsgrupper (NSG: er) på den virtuella datorn (VM) och har problem med anslutningen VM, innehåller den här artikeln en översikt över diagnostikfunktionerna för NSG: er för att fortsätta felsökningen.

NSG: er kan du styra vilka typer av trafik som flöde till och från virtuella datorer (VM). NSG: er kan tillämpas på undernät i ett Azure Virtual Network (VNet), nätverksgränssnitt (NIC) eller båda. Effektiva reglerna som gäller för ett nätverkskort är en sammanställning av regler som finns i NSG: er som tillämpas på ett nätverkskort och undernät som den är ansluten till. Regler för dessa NSG: er kan ibland står i konflikt med varandra och påverka nätverksanslutning för en virtuell dator.  

Du kan visa alla effektiva säkerhetsregler från dina NSG: er som tillämpas på den Virtuella datorns nätverkskort. Den här artikeln visar hur du felsöker problem med nätverksanslutningen VM med hjälp av reglerna i Azure Resource Manager-distributionsmodellen. Om du inte är bekant med principerna för VNet och NSG läsa den [för virtuella nätverk](virtual-networks-overview.md) och [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md) översikt artiklar.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Använda effektiva säkerhetsregler för att felsöka VM trafikflöde
Det scenario som följer är ett exempel på ett vanligt anslutningsproblem:

En virtuell dator med namnet *VM1* är en del av ett undernät med namnet *Undernät1* inom ett VNet med namnet *WestUS VNet1*. Ett försök att ansluta till den virtuella datorn med RDP över TCP-port 3389 misslyckas. NSG: er som tillämpas på båda NIC *VM1 NIC1* och undernätet *Undernät1*. Trafik till TCP-port 3389 tillåts i NSG: N som är kopplad till nätverksgränssnittet *VM1 NIC1*, men TCP ping till VM1 är port 3389 misslyckas.

Följande steg kan användas för att fastställa inkommande och utgående anslutningsfel över alla portar när det här exemplet används TCP-port 3389.

## <a name="detailed-troubleshooting-steps"></a>Detaljerad felsökning
Utför följande steg för att felsöka NSG: er för en virtuell dator:

1. Starta en Azure PowerShell-sessionen och logga in på Azure. Om du inte är bekant med Azure PowerShell kan du läsa den [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) artikel. Ditt konto ha tilldelats den *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* åtgärden för nätverksgränssnittet. Information om hur du tilldelar åtgärder till konton finns [skapa anpassade roller för rollbaserad åtkomstkontroll i](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Ange följande kommando för att returnera alla NSG-regler tillämpas på ett nätverkskort med namnet *VM1 NIC1* i resursgruppen *RG1*:
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Om du inte vet namnet på ett nätverkskort, kan du ange följande kommando för att hämta namnen på alla nätverkskort i en resursgrupp: 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    Följande är ett exempel på effektiva regler utdata returneras för den *VM1 NIC1* NIC:
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    Observera följande information i utdata:
   
   * Det finns två **NetworkSecurityGroup** avsnitt: en är associerad med ett undernät (*Undernät1*) och ett är kopplat till ett nätverkskort (*VM1 NIC1*). I det här exemplet har en NSG tillämpats för varje.
   * **Associationen** visar resurs (undernät eller NIC) angivna NSG är associerad med. Om NSG-resurs är flyttas/koppla bort omedelbart innan du kör det här kommandot kan behöva du vänta några sekunder innan ändringen så att den återger kommandots utdata. 
   * De namn som inleds med *defaultSecurityRules*: när en NSG skapas, skapas flera standardregler för säkerhet i den. Standardreglerna kan inte tas bort, men de kan åsidosättas med högre Prioritetsregler.
     Läs den [NSG översikt](virtual-networks-nsg.md#default-rules) artikeln om du vill lära dig mer om NSG standard säkerhetsregler.
   * **ExpandedAddressPrefix** expanderar adressprefix för NSG standardtaggar. Taggar representerar flera adressprefix. Utökningen av taggar kan vara användbart när du felsöker VM anslutning till eller från specifika adressprefix. Till exempel med VNET-peering utökas VIRTUAL_NETWORK taggen visas peerkoppla VNet-prefix i föregående utdata.
     
     > [!NOTE]
     > Kommandot endast visar effektiva reglerna om en NSG är associerad med ett undernät och/eller ett nätverkskort. En virtuell dator kan ha flera nätverkskort med olika NSG: er som används. När du felsöker, kör du kommandot för varje nätverkskort.
     > 
     > 
3. För att underlätta filtrering över större antal NSG-regler kan du ange följande kommandon för att fortsätta felsökningen: 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    Ett filter för RDP-trafik (TCP-port 3389), används till diagramvyn, enligt följande bild:
   
    ![Regellistan](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. Som du ser i diagramvyn finns både tillåta och neka regler för RDP. Utdata från steg 2 visar att den *DenyRDP* regeln är i NSG tillämpad på undernätet. Regler för inkommande trafik bearbetas NSG: er tillämpad på undernätet först. Om en matchning hittas, bearbetas inte NSG tillämpas för nätverksgränssnittet. I det här fallet den *DenyRDP* regeln från undernätet blockerar RDP till den virtuella datorn (**VM1**).
   
   > [!NOTE]
   > En virtuell dator kan ha flera nätverkskort som är kopplade till den. Varje kanske är anslutet till ett annat undernät. Eftersom kommandona i föregående steg körs mot ett nätverkskort, är det viktigt att se till att du anger du får anslutningsfel till nätverkskortet. Om du inte är säker på att köra du alltid kommandon mot varje nätverkskort som är anslutet till den virtuella datorn.
   > 
   > 
5. Till RDP till VM1 ändra den *neka RDP (port 3389)* regel att *Tillåt RDP(3389)* i den **Undernät1 NSG** NSG. Kontrollera att TCP-port 3389 är öppen genom att öppna en RDP-anslutning till den virtuella datorn eller med hjälp av verktyget PsPing. Du kan lära dig mer om PsPing genom att läsa den [PsPing hämtningssidan](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    Du kan eller ta bort regler från en NSG med hjälp av informationen i utdata från kommandot:
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>Överväganden
Tänk på följande när du felsöker problem med nätverksanslutningen:

* Standard NSG-regler ska blockera inkommande åtkomst från internet och tillåter bara VNet inkommande trafik. Regler bör läggas uttryckligen vill tillåta inkommande åtkomst från Internet, som krävs.
* Om det finns inga NSG säkerhetsregler som orsakar en virtuell dator är ansluten till nätverket misslyckas, kan det bero på följande:
  * Brandväggsprogram som körs i den Virtuella datorns operativsystem
  * Vägar som konfigurerats för virtuella installationer eller lokal trafik. Internet-trafik kan omdirigeras till lokalt via Tvingad tunneltrafik. En RDP/SSH-anslutning från Internet till den virtuella datorn kanske inte fungerar med den här inställningen, beroende på hur trafiken hanteras i nätverksmaskinvara lokalt. Läs den [felsökning vägar](virtual-network-routes-troubleshoot-powershell.md) artikel för att lära dig att felsöka väg problem som kan hindra flödet av trafik till och från den virtuella datorn. 
* Om du har peerkoppla Vnet, som standard, expanderar taggen VIRTUAL_NETWORK automatiskt för att inkludera prefix för peerkoppla Vnet. Du kan visa dessa prefix i den **ExpandedAddressPrefix** lista, för att felsöka eventuella problem som är relaterade till VNet-peering anslutning. 
* Effektiva säkerhetsregler visas bara om det finns en NSG som är kopplade till Virtuellt datorns nätverkskort och undernät. 
* Om det finns inga NSG: er som är kopplade till nätverkskortet eller undernät och att du har en offentlig IP-adress som tilldelats till den virtuella datorn, kommer alla portar vara öppna för inkommande och utgående åtkomst. Om den virtuella datorn har en offentlig IP-adress, rekommenderas tillämpa NSG: er till nätverkskort eller undernät.  

