---
title: Diagnostisera ett problem med nätverkstrafikfilter för virtuella datorer | Microsoft-dokument
description: Lär dig hur du diagnostiserar ett problem med nätverkstrafikfilter för virtuella datorer genom att visa de effektiva säkerhetsreglerna för en virtuell dator.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: 6939ea2497a9f12321e1a6dfb9bf9fbb353bc7db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240767"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnostisera ett problem med nätverkstrafikfilter för virtuell dator

I den här artikeln får du lära dig hur du diagnostiserar ett problem med nätverkstrafikfilter genom att visa säkerhetsreglerna för nätverkssäkerhetsgruppen (NSG) som gäller för en virtuell dator (VM).

Med NSG:er kan du styra vilka typer av trafik som flödar in och ut ur en virtuell dator. Du kan associera en NSG till ett undernät i ett virtuellt Azure-nätverk, ett nätverksgränssnitt som är kopplat till en virtuell dator eller båda. De effektiva säkerhetsregler som tillämpas på ett nätverksgränssnitt är en aggregering av de regler som finns i NSG som är associerade till ett nätverksgränssnitt och undernätet som nätverksgränssnittet finns i. Regler i olika NSG-grupper kan ibland stå i konflikt med varandra och påverka en virtuell dators nätverksanslutning. Du kan visa alla effektiva säkerhetsregler från NSG:er som tillämpas på den virtuella datorns nätverksgränssnitt. Om du inte är bekant med begreppen virtuellt nätverk, nätverk eller NSG läser du [Översikt över virtuella nätverk,](virtual-networks-overview.md) [Nätverksgränssnitt](virtual-network-network-interface.md)och [Nätverkssäkerhetsgrupper](security-overview.md)översikt .

## <a name="scenario"></a>Scenario

Du försöker ansluta till en virtuell dator via port 80 från Internet, men anslutningen misslyckas. För att avgöra varför du inte kan komma åt port 80 från Internet kan du visa de gällande säkerhetsreglerna för ett nätverksgränssnitt med [Azure-portalen,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)eller [Azure CLI](#diagnose-using-azure-cli).

De steg som följer förutsätter att du har en befintlig virtuell dator för att visa de gällande säkerhetsreglerna för. Om du inte har en befintlig virtuell dator distribuerar du först en [Linux-](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-dator för att slutföra uppgifterna i den här artikeln med. Exemplen i den här artikeln är för en virtuell dator som heter *myVM* med ett nätverksgränssnitt som heter *myVMVMNic*. Den virtuella datorn och nätverksgränssnittet finns i en resursgrupp med namnet *myResourceGroup*och finns i regionen *östra USA.* Ändra värdena i stegen, beroende på vad som är lämpligt, för den virtuella datorn som du diagnostiserar problemet för.

## <a name="diagnose-using-azure-portal"></a>Diagnostisera med Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Azure-konto som har [de nödvändiga behörigheterna](virtual-network-network-interface.md#permissions).
2. Högst upp i Azure-portalen anger du namnet på den virtuella datorn i sökrutan. När namnet på den virtuella datorn visas i sökresultaten markerar du det.
3. Under **INSTÄLLNINGAR**väljer du **Nätverk**, som visas i följande bild:

   ![Visa säkerhetsregler](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   De regler som visas i föregående bild är för ett nätverksgränssnitt med namnet **myVMVMNic**. Du ser att det finns **inkommande portregler** för nätverksgränssnittet från två olika nätverkssäkerhetsgrupper:
   
   - **mySubnetNSG**: Associerad till undernätet som nätverksgränssnittet är i.
   - **myVMNSG:** Associerad till nätverksgränssnittet i den virtuella datorn som heter **myVMVMNic**.

   Regeln som heter **DenyAllInBound** är det som förhindrar inkommande kommunikation till den virtuella datorn via port 80, från internet, enligt beskrivningen i [scenariot](#scenario). Regeln listar *0.0.0.0/0* **för SOURCE**, som innehåller internet. Ingen annan regel med högre prioritet (lägre antal) tillåter port 80 inkommande. Information om hur du tillåter port 80 som är inkommande till den virtuella datorn från internet finns i [Lösa ett problem](#resolve-a-problem). Mer information om säkerhetsregler och hur Azure tillämpar dem finns i [Nätverkssäkerhetsgrupper](security-overview.md).

   Längst ned i bilden visas även **UTGÅENDE PORTREGLER**. Under det är de utgående portreglerna för nätverksgränssnittet. Även om bilden bara visar fyra inkommande regler för varje NSG, kan dina NSGs ha många fler än fyra regler. I bilden visas **VirtualNetwork** under **SOURCE och** **DESTINATION** och **AzureLoadBalancer** under **SOURCE**. **VirtualNetwork** och **AzureLoadBalancer** är [tjänsttaggar](security-overview.md#service-tags). Tjänsttaggar representerar en grupp IP-adressprefix för att minimera komplexiteten för att skapa säkerhetsregeln.

4. Kontrollera att den virtuella datorn är i körläge och välj sedan **Effektiva säkerhetsregler**, som visas i föregående bild, för att se de effektiva säkerhetsreglerna som visas i följande bild:

   ![Visa effektiva säkerhetsregler](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Reglerna som anges är desamma som du såg i steg 3, men det finns olika flikar för NSG som är associerade till nätverksgränssnittet och undernätet. Som du kan se på bilden visas bara de första 50 reglerna. Om du vill hämta en CSV-fil som innehåller alla regler väljer du **Hämta**.

   Om du vill se vilka prefix som varje tjänsttagg representerar markerar du en regel, till exempel regeln **AllowAzureLoadBalancerInbound**. Följande bild visar prefixen för **AzureLoadBalancer-tjänsttaggen:**

   ![Visa effektiva säkerhetsregler](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Även om **AzureLoadBalancer-tjänsttaggen** bara representerar ett prefix representerar andra tjänsttaggar flera prefix.

5. De tidigare stegen visade säkerhetsreglerna för ett nätverksgränssnitt som heter **myVMVMNic**, men du har också sett ett nätverksgränssnitt som heter **myVMVMNic2** i några av de tidigare bilderna. Den virtuella datorn i det här exemplet har två nätverksgränssnitt kopplade till den. De effektiva säkerhetsreglerna kan vara olika för varje nätverksgränssnitt.

   Om du vill se reglerna för **nätverksgränssnittet myVMVMNic2** markerar du det. Som visas i bilden som följer har nätverksgränssnittet samma regler som är associerade med sitt undernät som **myVMVMNic-nätverksgränssnittet,** eftersom båda nätverksgränssnitten finns i samma undernät. När du associerar en NSG till ett undernät tillämpas dess regler på alla nätverksgränssnitt i undernätet.

   ![Visa säkerhetsregler](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Till skillnad från **myVMVMNic-nätverksgränssnittet** har **nätverksgränssnittet myVMVMNic2** inte en nätverkssäkerhetsgrupp som är associerad med det. Varje nätverksgränssnitt och undernät kan ha noll eller ett NSG associerat till det. NSG som är associerat till varje nätverksgränssnitt eller undernät kan vara samma eller olika. Du kan associera samma nätverkssäkerhetsgrupp till så många nätverksgränssnitt och undernät som du väljer.

Även om effektiva säkerhetsregler har visats via den virtuella datorn kan du också visa effektiva säkerhetsregler via en individ:
- **Nätverksgränssnitt:** Lär dig hur du [visar ett nätverksgränssnitt](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG:** Lär dig att [visa en NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnostisera med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra de kommandon som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnadsfritt interaktivt skal. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du `Connect-AzAccount` också köra för att logga in på Azure med ett konto som har [de behörigheter](virtual-network-network-interface.md#permissions)som krävs ].

Få effektiva säkerhetsregler för ett nätverksgränssnitt med [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). I följande exempel får du gällande säkerhetsregler för ett nätverksgränssnitt med namnet *myVMVMNic*, det vill de är i en resursgrupp med namnet *myResourceGroup:*

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Utdata returneras i json-format. För att förstå utdata, se [tolka kommandoutdata](#interpret-command-output).
Utdata returneras endast om en NSG är associerad med nätverksgränssnittet, undernätet som nätverksgränssnittet finns i, eller båda. Den virtuella datorn måste vara i körläge. En virtuell dator kan ha flera nätverksgränssnitt med olika NSG-enheter tillämpade. Kör kommandot för varje nätverksgränssnitt när du felsöker.

Om du fortfarande har ett anslutningsproblem läser du [ytterligare diagnoser](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella datorn som nätverksgränssnittet är kopplat till, returnerar följande kommandon ID:n för alla nätverksgränssnitt som är anslutna till en virtuell dator:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Du får utdata som liknar följande exempel:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

I föregående utdata är nätverksgränssnittsnamnet *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnostisera med Azure CLI

Om du använder CLI-kommandon (Azure Command-line interface) för att slutföra uppgifter i den här artikeln, kör du antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du `az login` också köra och logga in på Azure med ett konto som har [de nödvändiga behörigheterna](virtual-network-network-interface.md#permissions).

Få effektiva säkerhetsregler för ett nätverksgränssnitt med [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). I följande exempel får du gällande säkerhetsregler för ett nätverksgränssnitt med namnet *myVMVMNic* som finns i en resursgrupp med namnet *myResourceGroup:*

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Utdata returneras i json-format. För att förstå utdata, se [tolka kommandoutdata](#interpret-command-output).
Utdata returneras endast om en NSG är associerad med nätverksgränssnittet, undernätet som nätverksgränssnittet finns i, eller båda. Den virtuella datorn måste vara i körläge. En virtuell dator kan ha flera nätverksgränssnitt med olika NSG-enheter tillämpade. Kör kommandot för varje nätverksgränssnitt när du felsöker.

Om du fortfarande har ett anslutningsproblem läser du [ytterligare diagnoser](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella datorn som nätverksgränssnittet är kopplat till, returnerar följande kommandon ID:n för alla nätverksgränssnitt som är anslutna till en virtuell dator:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

I den returnerade utdata visas information som liknar följande exempel:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

I föregående utdata är nätverksgränssnittsnamnet *myVMVMNic-gränssnitt*.

## <a name="interpret-command-output"></a>Tolka kommandoutdata

Oavsett om du använde [PowerShell](#diagnose-using-powershell)eller [Azure CLI](#diagnose-using-azure-cli) för att diagnostisera problemet får du utdata som innehåller följande information:

- **NetworkSecurityGroup**: Nätverkssäkerhetsgruppens ID.
- **Association**: Om nätverkssäkerhetsgruppen är associerad med ett *NetworkInterface* eller *Undernät*. Om en NSG är associerad till båda returneras utdata med **NetworkSecurityGroup,** **Association**och **EffectiveSecurityRules**för varje NSG. Om NSG associeras eller separeras omedelbart innan kommandot körs för att visa de effektiva säkerhetsreglerna kan du behöva vänta några sekunder innan ändringen ska återspeglas i kommandoutdata.
- **EffectiveSecurityRules**: En förklaring av varje egenskap beskrivs i [Skapa en säkerhetsregel](manage-network-security-group.md#create-a-security-rule). Regelnamn som föregås av *defaultSecurityRules/* är standardsäkerhetsregler som finns i varje NSG. Regelnamn som föregås av *securityRules/* är regler som du har skapat. Regler som anger en [tjänsttagg](security-overview.md#service-tags), till exempel **Internet,** **VirtualNetwork**och **AzureLoadBalancer** för **egenskaperna destinationAddressPrefix** eller **sourceAddressPrefix,** har också värden för egenskapen **expandedDestinationAddressPrefix.** Egenskapen **expandedDestinationAddressPrefix** listar alla adressprefix som representeras av servicetag.

Om du ser dubblettregler som anges i utdata beror det på att en NSG är kopplad till både nätverksgränssnittet och undernätet. Båda NSG:erna har samma standardregler och kan ha ytterligare dubblettregler, om du har skapat egna regler som är desamma i båda NSG:erna.

Regeln som heter **defaultSecurityRules/DenyAllInBound** är vad som förhindrar inkommande kommunikation till den virtuella datorn via port 80, från Internet, enligt beskrivningen i [scenariot](#scenario). Ingen annan regel med högre prioritet (lägre antal) tillåter port 80 inkommande från Internet.

## <a name="resolve-a-problem"></a>Lösa ett problem

Oavsett om du använder [Azure-portalen,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)eller [Azure CLI](#diagnose-using-azure-cli) för att diagnostisera problemet som presenteras i [scenariot](#scenario) i den här artikeln, är lösningen att skapa en nätverkssäkerhetsregel med följande egenskaper:

| Egenskap                | Värde                                                                              |
|---------                |---------                                                                           |
| Källa                  | Alla                                                                                |
| Källportintervall      | Alla                                                                                |
| Mål             | IP-adressen för den virtuella datorn, ett intervall av IP-adresser eller alla adresser i undernätet. |
| Målportintervall | 80                                                                                 |
| Protokoll                | TCP                                                                                |
| Åtgärd                  | Tillåt                                                                              |
| Prioritet                | 100                                                                                |
| Namn                    | Tillåt-HTTP-all                                                                     |

När du har skapat regeln tillåts port 80 inkommande från Internet, eftersom regelns prioritet är högre än standardsäkerhetsregeln *DenyAllInBound*som nekar trafiken. Lär dig hur du [skapar en säkerhetsregel](manage-network-security-group.md#create-a-security-rule). Om olika NSG:er är associerade till både nätverksgränssnittet och undernätet måste du skapa samma regel i båda NSG:erna.

När Azure bearbetar inkommande trafik bearbetar den regler i NSG som är associerade till undernätet (om det finns en associerad NSG) och bearbetar sedan reglerna i NSG som är associerade till nätverksgränssnittet. Om det finns en NSG som är associerad med nätverksgränssnittet och undernätet måste porten vara öppen i båda NSG:erna, för att trafiken ska nå den virtuella datorn. För att underlätta administrations- och kommunikationsproblem rekommenderar vi att du kopplar en NSG till ett undernät i stället för enskilda nätverksgränssnitt. Om virtuella datorer i ett undernät behöver olika säkerhetsregler kan du göra nätverksgränssnitten till medlemmar i en programsäkerhetsgrupp (ASG) och ange ett ASG som källa och mål för en säkerhetsregel. Läs mer om [programsäkerhetsgrupper](security-overview.md#application-security-groups).

Om du fortfarande har kommunikationsproblem läser du [Överväganden](#considerations) och ytterligare diagnos.

## <a name="considerations"></a>Överväganden

Tänk på följande punkter vid felsökning av anslutningsproblem:

* Standardsäkerhetsregler blockerar inkommande åtkomst från Internet och tillåter endast inkommande trafik från det virtuella nätverket. Om du vill tillåta inkommande trafik från Internet lägger du till säkerhetsregler med högre prioritet än standardregler. Läs mer om [standardsäkerhetsregler](security-overview.md#default-security-rules)eller hur du lägger till [en säkerhetsregel](manage-network-security-group.md#create-a-security-rule).
* Om du har peered virtuella nätverk, som standard, **VIRTUAL_NETWORK** tjänst tag automatiskt utökas till att omfatta prefix för peered virtuella nätverk. Om du vill felsöka eventuella problem som rör virtuell nätverks peering kan du visa prefixen i listan **Expanderadadressprefix.** Läs mer om [virtuella nätverks peering](virtual-network-peering-overview.md) och [servicetaggar](security-overview.md#service-tags).
* Gällande säkerhetsregler visas bara för ett nätverksgränssnitt om det finns en NSG som är associerad med den virtuella datorns nätverksgränssnitt och, eller, undernät, och om den virtuella datorn är i körläge.
* Om det inte finns några NSG-grupper som är associerade med nätverksgränssnittet eller undernätet, och du har en [offentlig IP-adress](virtual-network-public-ip-address.md) tilldelad till en virtuell dator, är alla portar öppna för inkommande åtkomst från och utgående åtkomst till var som helst. Om den virtuella datorn har en offentlig IP-adress rekommenderar vi att du använder en NSG på undernätet i nätverksgränssnittet.

## <a name="additional-diagnosis"></a>Ytterligare diagnos

* Om du vill köra ett snabbtest för att avgöra om trafik tillåts till eller från en virtuell dator använder du [funktionen för IP-flödeskontroll](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) för Azure Network Watcher. IP-flöde verifiera talar om för dig om trafik är tillåten eller nekad. Om nekad, IP-flöde verifiera berättar vilken säkerhetsregel som förnekar trafiken.
* Om det inte finns några säkerhetsregler som gör att en virtuell dators nätverksanslutning misslyckas kan problemet bero på:
  * Brandväggsprogram som körs i den virtuella datorns operativsystem
  * Vägar som konfigurerats för virtuella enheter eller lokal trafik. Internettrafik kan omdirigeras till ditt lokala nätverk via [tvångstunneler.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Om du tvingar tunnelinternettrafik till en virtuell installation, eller lokalt, kanske du inte kan ansluta till den virtuella datorn från Internet. Mer information om hur du diagnostiserar vägproblem som kan hindra trafikflödet från den virtuella datorn finns i Diagnostisera ett problem med routning av [nätverksdirigering av virtuella datorer](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om alla uppgifter, egenskaper och inställningar för en [nätverkssäkerhetsgrupp](manage-network-security-group.md#work-with-network-security-groups) och [säkerhetsregler](manage-network-security-group.md#work-with-security-rules).
- Lär dig mer om [standardsäkerhetsregler,](security-overview.md#default-security-rules) [tjänsttaggar](security-overview.md#service-tags)och [hur Azure behandlar säkerhetsregler för inkommande och utgående trafik](security-overview.md#network-security-groups) för en virtuell dator.
