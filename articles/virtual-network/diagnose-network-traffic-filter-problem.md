---
title: Diagnostisera virtuella trafik filter nätverksproblem | Microsoft Docs
description: Lär dig mer om att diagnostisera virtuella trafik filter nätverksproblem genom att visa effektiva säkerhetsregler för en virtuell dator.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: jdial
ms.openlocfilehash: 1c33a75363eec2b4e338ba64e3d1ad877d8b1610
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757235"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnostisera virtuella trafik filter nätverksproblem

I den här artikeln får lära du att diagnostisera nätverksproblem trafik filter genom att visa de (NSG) säkerhetsregler för nätverkssäkerhetsgrupper som gäller för en virtuell dator (VM).

NSG: er kan du styra vilka typer av trafik som flöde till och från en virtuell dator. Du kan koppla en NSG till ett undernät i Azure-nätverk, ett nätverksgränssnitt som är anslutna till en virtuell dator eller båda. Effektiv säkerhetsreglerna som gäller för ett nätverksgränssnitt är en sammanställning av regler som finns i NSG: N som är kopplat till ett nätverksgränssnitt och undernätet nätverksgränssnittet. Regler i olika NSG: er kan ibland står i konflikt med varandra och påverka nätverksanslutning för en virtuell dator. Du kan visa alla effektiva säkerhetsregler från NSG: er som tillämpas på den Virtuella datorns nätverksgränssnitt. Om du inte är bekant med virtuella nätverk, nätverksgränssnittet eller NSG-begrepp finns [översikt över virtuella nätverk](virtual-networks-overview.md), [nätverksgränssnittet](virtual-network-network-interface.md), och [Network security groups översikt](security-overview.md).

## <a name="scenario"></a>Scenario

Du försöker ansluta till en virtuell dator via port 80 från internet, men om anslutningen misslyckas. För att avgöra varför du inte åtkomst till port 80 från Internet, kan du visa effektiva säkerhetsregler för ett nätverksgränssnitt med hjälp av Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), eller [Azure CLI](#diagnose-using-azure-cli).

De steg som följer förutsätter att du har en befintlig virtuell dator att visa effektiva säkerhetsregler för. Om du inte har en befintlig virtuell dator, först distribuera en [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM utföra åtgärder i den här artikeln med. Exemplen i den här artikeln gäller för en virtuell dator med namnet *myVM* med ett nätverksgränssnitt med namnet *myVMVMNic*. Den virtuella datorn och ett nätverksgränssnitt finns i en resursgrupp med namnet *myResourceGroup*, och finns i den *östra USA* region. Ändra värdena i instruktioner för den virtuella datorn som du felsöker problemet för.

## <a name="diagnose-using-azure-portal"></a>Diagnostisera med hjälp av Azure portal

1. Logga in på Azure [portal](https://portal.azure.com) med ett Azure-konto som har den [behörighet](virtual-network-network-interface.md#permissions).
2. Ange namnet på den virtuella datorn i sökrutan överst på Azure-portalen. När namnet på den virtuella datorn visas i sökresultaten väljer du den.
3. Under **inställningar**väljer **nätverk**som visas i följande bild:

    ![Visa säkerhetsregler](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

    Reglerna du ser i bilden ovan är för ett nätverksgränssnitt med namnet **myVMVMNic**. Du ser att det finns **INKOMMANDE PORTREGLER** för nätverksgränssnittet från två olika nätverkssäkerhetsgrupper:- **mySubnetNSG**: som är associerade med undernätet som nätverksgränssnittet tillhör.
        - **myVMNSG**: som är kopplad till nätverksgränssnitt på den virtuella datorn med namnet **myVMVMNic**.

    Regeln med namnet **DenyAllInBound** är vad förhindrar att inkommande kommunikation till den virtuella datorn via port 80, internet, enligt beskrivningen i den [scenariot](#scenario). Regeln listorna *0.0.0.0/0* för **källa**, inklusive internet. Ingen regel med högre prioritet (lägre nummer) tillåter port 80 inkommande. Att tillåta port 80 för inkommande till den virtuella datorn från internet, se [lösa ett problem](#resolve-a-problem). Läs mer om säkerhetsregler och hur Azure använder dem i [Nätverkssäkerhetsgrupper](security-overview.md).

    Längst ned i bilden du också se **utgående PORTREGLER**. Under som är regler för utgående porten för nätverksgränssnittet. Även om bilden illustrerar bara fyra regler för inkommande trafik för varje NSG, kan dina NSG: er ha många fler än fyra regler. Du ser i bilden **VirtualNetwork** under **källa** och **mål** och **AzureLoadBalancer** under  **KÄLLAN**. **VirtualNetwork** och **AzureLoadBalancer** är [tjänsten taggar](security-overview.md#service-tags). Tjänsten taggar representerar en grupp med IP-adressprefix för att minimera komplexitet för skapande av säkerhet.

4. Se till att den virtuella datorn körs tillstånd och välj sedan **effektiva säkerhetsregler**som visas i föregående bild för att se gällande säkerhetsregler visas i följande bild:

    ![Visa effektiva säkerhetsregler](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

    Reglerna är samma som du såg i steg 3, även om det finns olika flikarna för NSG: N som är kopplad till nätverksgränssnittet och undernätet. Som du ser i bilden visas endast de första 50 reglerna. Om du vill hämta en .csv-fil som innehåller alla regler, Välj **hämta**.

    Se som prefix varje service-tagg representerar, markerar du en regel, till exempel regeln med namnet **AllowAzureLoadBalancerInbound**. Följande bild visar prefix för den **AzureLoadBalancer** tjänsten tagg:

    ![Visa effektiva säkerhetsregler](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

    Även om den **AzureLoadBalancer** tjänsten endast representerar ett prefix, andra tjänsten taggar representerar flera prefix.

4. De här stegen visade säkerhetsregler för ett nätverksgränssnitt med namnet **myVMVMNic**, men du har också sett ett nätverksgränssnitt med namnet **myVMVMNic2** i några av de föregående bilderna. Den virtuella datorn i det här exemplet har två nätverksgränssnitt som är kopplade till den. De effektiva säkerhetsreglerna kan vara olika för varje nätverksgränssnitt.

    Se regler för den **myVMVMNic2** nätverksgränssnittet, markerar du den. I bilden nedan visas nätverksgränssnittet har samma regler som är associerade med undernätet som den **myVMVMNic** nätverksgränssnittet eftersom båda nätverksgränssnitt i samma undernät. När du kopplar en NSG till ett undernät som dess regler tillämpas på alla nätverksgränssnitt i undernätet.

    ![Visa säkerhetsregler](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

    Till skillnad från den **myVMVMNic** nätverksgränssnitt, den **myVMVMNic2** nätverksgränssnittet har inte en nätverkssäkerhetsgrupp som är kopplade till den. Varje gränssnitt och undernätet kan ha noll eller en NSG som är kopplade till den. NSG: N som är associerade med varje nätverksgränssnitt eller undernät kan vara på samma eller olika. Du kan associera samma nätverkssäkerhetsgruppen till så många nätverkskort och undernät som du väljer.

Även om effektiva säkerhetsregler har visas via den virtuella datorn, du kan också visa effektiva säkerhetsregler via en:
- **Enskilda nätverksgränssnittet**: Lär dig hur du [visa ett nätverksgränssnitt](virtual-network-network-interface.md#view-network-interface-settings).
- **Enskilda NSG**: Lär dig hur du [visa en NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnostisera med hjälp av PowerShell

Du kan köra kommandon som visas i den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud-gränssnittet är ett kostnadsfritt interaktiva gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn, måste den *AzureRM* PowerShell-modul, version 6.0.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt, måste du också köra `Login-AzureRmAccount` att logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions)].

Hämta de effektiva säkerhetsreglerna för ett nätverksgränssnitt med [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup). I följande exempel hämtas effektiva säkerhetsregler för ett nätverksgränssnitt med namnet *myVMVMNic*, som i en resursgrupp med namnet *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic interface `
  -ResourceGroupName myResourceGroup
```

Utdata returneras i json-format. För att förstå utdata, se [tolka kommandoutdata](#interpret-command-output).
Utdata returneras bara om en NSG är associerad med nätverksgränssnittet eller den nätverksgränssnittet är i undernätet. Den virtuella datorn måste vara körs. En virtuell dator kan ha flera nätverksgränssnitt med olika NSG: er som används. När du felsöker, kör du kommandot för varje nätverksgränssnitt.

Om du fortfarande har anslutningsproblem, se [ytterligare diagnos](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men du vet namnet på den virtuella datorn nätverksgränssnittet är ansluten till, returnera ID-numren för alla nätverksgränssnitt som är kopplad till en virtuell dator med följande kommandon:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Utdata som liknar följande exempel visas:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

I tidigare utdata nätverksgränssnittets namn är *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnostisera med Azure CLI

Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` och logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions).

Hämta de effektiva säkerhetsreglerna för ett nätverksgränssnitt med [az nätverk nic lista-gällande-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). I följande exempel hämtas effektiva säkerhetsregler för ett nätverksgränssnitt med namnet *myVMVMNic* som finns på en resursgrupp med namnet *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Utdata returneras i json-format. För att förstå utdata, se [tolka kommandoutdata](#interpret-command-output).
Utdata returneras bara om en NSG är associerad med nätverksgränssnittet eller den nätverksgränssnittet är i undernätet. Den virtuella datorn måste vara körs. En virtuell dator kan ha flera nätverksgränssnitt med olika NSG: er som används. När du felsöker, kör du kommandot för varje nätverksgränssnitt.

Om du fortfarande har anslutningsproblem, se [ytterligare diagnos](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men du vet namnet på den virtuella datorn nätverksgränssnittet är ansluten till, returnera ID-numren för alla nätverksgränssnitt som är kopplad till en virtuell dator med följande kommandon:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

I den returnerade utdatan visas information som liknar följande exempel:

```azurecli
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

I tidigare utdata nätverksgränssnittets namn är *myVMVMNic gränssnittet*.

## <a name="interpret-command-output"></a>Tolka kommandoutdata

Oavsett om du använde den [PowerShell](#diangose-using-powershell), eller [Azure CLI](#diagnose-using-azure-cli) om du vill felsöka problemet visas utdata som innehåller följande information:

- **NetworkSecurityGroup**: ID för nätverkssäkerhetsgruppen.
- **Associationen**: om nätverkssäkerhetsgruppen är kopplad till en *NetworkInterface* eller *undernät*. Om en NSG är associerad till både utdata returneras med **NetworkSecurityGroup**, **Association**, och **EffectiveSecurityRules**, för varje NSG. Om NSG: N som är associerade eller koppla bort omedelbart innan du kör kommandot för att visa effektiva säkerhetsregler, kan du behöva vänta några sekunder innan ändringen så att den återger kommandots utdata.
- **EffectiveSecurityRules**: en förklaring av varje egenskap beskrivs i [skapar en säkerhetsregel](manage-network-security-group.md#create-a-security-rule). Regeln namn som inleds med *defaultSecurityRules /* är standard säkerhetsregler som finns i varje NSG. Regeln namn som inleds med *securityRules /* är regler som du har skapat. Regler som anger en [tjänsten taggen](security-overview.md#service-tags), som **Internet**, **VirtualNetwork**, och **AzureLoadBalancer** för den  **destinationAddressPrefix** eller **sourceAddressPrefix** egenskaper, också ha värden för den **expandedDestinationAddressPrefix** egenskapen. Den **expandedDestinationAddressPrefix** egenskapslistor alla adressprefix som representeras av tjänsten-taggen.

Om du ser dubbla reglerna i utdata, beror det på att en NSG är kopplad till nätverksgränssnittet såväl undernätet. Både NSG: er har samma standardregler och kan ha ytterligare dubbla regler, om du har skapat dina egna regler som är samma i båda NSG: er.

Regeln med namnet **defaultSecurityRules/DenyAllInBound** är vad förhindrar att inkommande kommunikation till den virtuella datorn via port 80, internet, enligt beskrivningen i den [scenariot](#scenario). Ingen regel med högre prioritet (lägre nummer) tillåter port 80 för inkommande från internet.

## <a name="resolve-a-problem"></a>Lösa problem

Om du använder Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), eller [Azure CLI](#diagnose-using-azure-cli) att diagnosticera problemet visas i den [scenariot](#scenario) i det här artikeln lösningen är att skapa en nätverkssäkerhetsregeln med följande egenskaper:

| Egenskap                 | Värde                                                                              |
|---------                |---------                                                                           |
| Källa                  | Alla                                                                                |
| Källportintervall      | Alla                                                                                |
| Mål             | IP-adressen för den virtuella datorn, ett intervall med IP-adresser eller alla adresser i undernätet. |
| Målportintervall | 80                                                                                 |
| Protokoll                | TCP                                                                                |
| Åtgärd                  | Tillåt                                                                              |
| Prioritet                | 100                                                                                |
| Namn                    | Tillåt HTTP-alla                                                                     |

När du skapar regeln port 80 tillåts inkommande från internet, eftersom prioritet för regeln är högre än standardsäkerhetsregel med namnet *DenyAllInBound*, som nekar trafik. Lär dig hur du [skapar en säkerhetsregel](manage-network-security-group.md#create-a-security-rule). Om olika NSG: er som är associerade till både nätverksgränssnittet och undernätet, måste du skapa samma regel i båda NSG: er.

När Azure processer inkommande trafik, den bearbetar regler i NSG: N som är associerade med undernätet (om det finns en tillhörande NSG) och sedan den bearbetar reglerna i NSG: N som är kopplad till nätverksgränssnittet. Om det finns en NSG som kopplats till nätverksgränssnittet och undernätet, måste porten vara öppna i båda NSG: er, för trafik till den virtuella datorn. För att underlätta administration och kommunikation problem, rekommenderar vi att du kopplar en NSG till ett undernät i stället för enskilda nätverksgränssnitt. Om olika säkerhetsregler virtuella datorer i ett undernät måste du kontrollera nätverkets gränssnitt medlemmar i en säkerhetsgrupp för programmet (ASG) och ange en ASG som källa och mål för en säkerhetsregel. Lär dig mer om [programmet säkerhetsgrupper](security-overview.md#application-security-groups).

Om det fortfarande uppstår problem, se [överväganden](#considerations) och [ytterligare diagnos](#additional-dignosis).

## <a name="considerations"></a>Överväganden

Tänk på följande när du felsöker problem med nätverksanslutningen:

* Standard säkerhetsregler blockera inkommande åtkomst från internet och tillåter bara inkommande trafik från det virtuella nätverket. Lägg till säkerhetsregler med högre prioritet än standardreglerna tillåter inkommande trafik från Internet. Lär dig mer om [standard säkerhetsregler](security-overview.md#default-security-rules), eller så [lägga till en säkerhetsregel](manage-network-security-group.md#create-a-security-rule).
* Om du har peerkoppla virtuella nätverk som standard den **VIRTUAL_NETWORK** service taggen utökas automatiskt för att inkludera prefix för peerkoppla virtuella nätverk. För att felsöka eventuella problem som rör peering virtuellt nätverk kan du visa prefix i den **ExpandedAddressPrefix** lista. Lär dig mer om [virtuellt nätverk peering](virtual-network-peering-overview.md) och [tjänsten taggar](security-overview.md#service-tags).
* Effektiva säkerhetsregler visas bara för ett nätverksgränssnitt om det finns en NSG som associeras med den Virtuella datorns nätverksgränssnitt och, eller undernät, och om den virtuella datorn är i körläge.
* Om det finns inga NSG: er som är kopplad till nätverksgränssnittet eller undernät och du har en [offentliga IP-adressen](virtual-network-public-ip-address.md) tilldelas en virtuell dator, alla portar är öppna för inkommande åtkomst från och utgående åtkomst till valfri plats. Om den virtuella datorn har en offentlig IP-adress, rekommenderar vi använda en NSG till undernätet för nätverksgränssnittet.

## <a name="additional-diagnosis"></a>Ytterligare diagnos

* Kör en snabb test för att avgöra om tillåts trafik till eller från en virtuell dator med den [IP-flöde Kontrollera](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) möjligheterna för Azure Nätverksbevakaren. IP-flöde Kontrollera berättar om trafik tillåts eller nekas. Om nekades, IP-flöde kontrollerar du talar om vilka säkerhetsregeln nekar trafik.
* Om det finns inga säkerhetsregler som orsakar en virtuell dator är ansluten till nätverket misslyckas, kan det bero på följande:
  * Brandväggsprogram som körs i den Virtuella datorns operativsystem
  * Vägar som konfigurerats för virtuella installationer eller lokal trafik. Internet-trafik kan omdirigeras till ditt lokala nätverk via [Tvingad tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du tvingar tunnel internet-trafik till en virtuell installation eller lokalt, kan du kanske inte ansluta till den virtuella datorn från internet. Om du vill lära dig att felsöka väg problem som kan störa trafikflödet från den virtuella datorn, se [diagnostisera virtuella trafik routning nätverksproblem](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om alla aktiviteter, egenskaper och inställningar för en [nätverkssäkerhetsgruppen](manage-network-security-group.md#work-with-network-security-groups) och [säkerhetsregler](manage-network-security-group.md#work-with-security-rules).
- Lär dig mer om [standard säkerhetsregler](security-overview.md#default-security-rules), [tjänsten taggar](security-overview.md#service-tags), och [hur Azure bearbetar säkerhetsregler för inkommande och utgående trafik](security-overview.md#network-security-groups) för en virtuell dator.
