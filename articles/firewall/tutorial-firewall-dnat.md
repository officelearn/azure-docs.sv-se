---
title: Filtrera inkommande trafik med Azure Firewall DNAT med hjälp av Azure-portalen
description: I den här självstudien får du lära dig att distribuera och konfigurera Azure Firewall DNAT via Azure-portalen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 766ad04251fbe404d43734115e41e23ae0a4be28
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982060"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Självstudie: Filtrera inkommande trafik med Azure Firewall DNAT med hjälp av Azure-portalen

Du kan konfigurera Azure Firewall Destination Network Address Translation (DNAT) att översätta och filtrera inkommande trafik till dina undernät. Azure Firewall använder inte begreppen inkommande och utgående regler. Det finns programregler och nätverksregler och de tillämpas på all trafik som kommer in i brandväggen. Nätverksregler tillämpas först, sedan programregler, och reglerna är avslutande.

>[!NOTE]
>Firewall DNAT-funktionen är för tillfället bara tillgänglig i Azure PowerShell och REST.

Om till exempel en nätverksregel matchas utvärderas paketet inte av programregler. Om det inte finns någon nätverksregelmatchning och om paketprotokollet är HTTP/HTTPS utvärderas paketet av programreglerna. Om det fortfarande inte finns någon matchning utvärderas paketet mot [infrastrukturregelsamlingen](infrastructure-fqdns.md). Om det fortfarande inte finns någon matchning nekas paketet som standard.

När du konfigurerar DNAT, ställs åtgärden för NAT-regelsamling in på **Destination Network Address Translation (DNAT)**. Brandväggens offentliga IP-adress och port översätts till en privat IP-adress och port. Sedan tillämpas regler som vanligt, nätverksregler först och sedan programregler. Du kan till exempel konfigurera en nätverksregel att tillåta trafik från Fjärrskrivbord på TCP-port 3389. Adressöversättning sker först och sedan tillämpas nätverks- och programregler med hjälp av de översatta adresserna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * Skapa en standardväg
> * Konfigurera en DNAT-regel
> * Konfigurera en nätverksregel
> * Testa brandväggen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

För den här självstudien skapar du två peerkopplade virtuella nätverk:
- **VN-Hub** – brandväggen finns i det här virtuella nätverket.
- **VN-Spoke** – arbetsbelastningsservern finns i det här virtuella nätverket.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
1. Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).
1. Klicka på startsidan för Azure Portal, klicka på **Resursgrupper** och sedan på **Lägg till**.
2. I fältet **Resursgruppsnamn** anger du **RG-DNAT-Test**.
3. I fältet **Prenumeration** väljer du din prenumeration.
4. I fältet **Resursgruppsplats** väljer du en plats. Alla efterföljande resurser du skapar måste finnas på samma plats.
5. Klicka på **Skapa**.

## <a name="set-up-the-network-environment"></a>Konfigurera nätverksmiljön
Skapa först de virtuella nätverken och peerkoppla dem sedan.

### <a name="create-the-hub-vnet"></a>Skapa det virtuella Hub-nätverket
1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Nätverk** klickar du på **Virtuella nätverk**.
3. Klicka på **Lägg till**.
4. Som **Namn** anger du **VN-Hub**.
5. I fältet **Adressutrymme** skriver du **10.0.0.0/16**.
7. I fältet **Prenumeration** väljer du din prenumeration.
8. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer sedan **RG-DNAT-Test**.
9. Välj samma plats som tidigare i fältet **Plats**.
10. Under **Undernät**, i fältet **Namn**, skriver du **AzureFirewallSubnet**.

     Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
     > [!NOTE]
     > Den minsta storleken på undernätet AzureFirewallSubnet är /25.
11. I fältet **Adressintervall** skriver du **10.0.1.0/24**.
12. Använd övriga standardinställningar och klicka på **Skapa**.

### <a name="create-a-spoke-vnet"></a>Skapa ett virtuellt spoke-nätverk

1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Nätverk** klickar du på **Virtuella nätverk**.
3. Klicka på **Lägg till**.
4. I fältet **Namn** anger du **VN-Spoke**.
5. I fältet **Adressutrymme** skriver du **192.168.0.0/16**.
7. I fältet **Prenumeration** väljer du din prenumeration.
8. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer sedan **RG-DNAT-Test**.
9. Välj samma plats som tidigare i fältet **Plats**.
10. Under **Undernät**, i fältet **Namn** anger du **SN-Workload**.

    Servern kommer att finnas i det här undernätet.
1. I fältet **Adressintervall** skriver du **192.168.1.0/24**.
2. Använd övriga standardinställningar och klicka på **Skapa**.

### <a name="peer-the-vnets"></a>Peerkoppla de virtuella nätverken

Peerkoppla nu de två virtuella nätverken.

#### <a name="hub-to-spoke"></a>Hub till spoke

1. Klicka nu på det virtuella nätverket **VN-Hub**.
2. Under **Inställningar** klickar du på **Peering-sessioner**.
3. Klicka på **Lägg till**.
4. Ange **Peer-HubSpoke** som namn.
5. Välj **VN-Spoke** för det virtuella nätverket.
7. Klicka på **OK**.

#### <a name="spoke-to-hub"></a>Spoke till hub

1. Klicka på det virtuella nätverket **VN-Spoke**.
2. Under **Inställningar** klickar du på **Peering-sessioner**.
3. Klicka på **Lägg till**.
4. Ange **Peer-SpokeHub** som namn.
5. Välj **VN-Hub** för det virtuella nätverket.
6. Klicka på **Tillåt vidarebefordrad trafik**.
7. Klicka på **OK**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell arbetsbelastningsdator och placera den i undernätet **SN-Workload**.

1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Compute** klickar du på **Virtuella datorer**.
3. Klicka på **Lägg till**, klicka på **Windows Server**, klicka på **Windows Server 2016 Datacenter** och sedan på **Skapa**.

**Grundläggande inställningar**

1. I fältet **Namn** skriver du **Srv-Workload**.
5. Ange ett användarnamn och lösenord.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. I fältet **Resursgrupp** klickar du på **Använd befintlig** och väljer sedan **RG-DNAT-Test**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Klicka på **OK**.

**Storlek**

1. Välj en lämplig storlek för den virtuella testdator som ska köra Windows Server. Exempelvis **B2ms** (8 GB RAM-minne, 16 GB lagring).
2. Klicka på **Välj**.

**Inställningar**

1. Under **Nätverk**, i fältet **Virtuellt nätverk**, väljer du **VN-Spoke**.
2. I fältet **Undernät** väljer du **SN-Workload**.
3. Klicka på **Offentlig IP-adress** och klicka sedan på **Ingen**.
4. I fältet **Välj offentliga inkommande portar** väljer du **Inga offentliga inkommande portar**. 
2. Lämna övriga standardvärden och klicka på **OK**.

**Sammanfattning**

Granska sammanfattningen och klicka sedan på **Skapa**. Det här kan ta några minuter.

När distributionen är klar antecknar du den privata IP-adressen för den virtuella datorn. Den används senare när du konfigurerar brandväggen. Klicka på namnet på den virtuella datorn. Under **Inställningar** klickar du på **Nätverk** för att hitta den privata IP-adressen.


## <a name="deploy-the-firewall"></a>Distribuera brandväggen

1. På portalens startsida klickar du på **Skapa en resurs**.
2. Klicka på **Nätverk**, och sedan efter **Aktuella** klickar du på **Visa alla**.
3. Klicka på **Brandvägg** och sedan på **Skapa**. 
4. På sidan **Skapa en brandvägg** använder du följande tabell till att konfigurera brandväggen:
   
   |Inställning  |Värde  |
   |---------|---------|
   |Namn     |FW-DNAT-test|
   |Prenumeration     |\<din prenumeration\>|
   |Resursgrupp     |**Använd befintlig**: RG-DNAT-Test |
   |Plats     |Välj samma plats som tidigare|
   |Välj ett virtuellt nätverk     |**Använd befintlig**: VN-Hub|
   |Offentlig IP-adress     |**Skapa ny**. Den offentliga IP-adressen måste vara Standard SKU-typen.|

2. Klicka på **Granska + skapa**.
3. Granska sammanfattningen och klicka sedan på **Skapa** för att skapa brandväggen.

   Distributionen kan ta några minuter.
4. När distributionen är klar går du till resursgruppen **RG-DNAT-Test** och klickar på brandväggen **FW-DNAT-test**.
6. Skriv ned den privata IP-adressen. Du kommer att använda den senare när du skapar standardvägen.


## <a name="create-a-default-route"></a>Skapa en standardväg

För undernätet **SN-Workload** ställer du in att den utgående standardvägen ska gå via brandväggen.

1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Nätverk** klickar du på **Routningstabeller**.
3. Klicka på **Lägg till**.
4. I fältet **Namn** skriver du **RT-FWroute**.
5. I fältet **Prenumeration** väljer du din prenumeration.
6. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer **RG-DNAT-Test**.
7. Välj samma plats som tidigare i fältet **Plats**.
8. Klicka på **Skapa**.
9. Klicka på **Uppdatera** och klicka sedan på routningstabellen **RT-FWroute**.
10. Klicka på **Undernät** och sedan på **Associera**.
11. Klicka på **Virtuellt nätverk** och välj sedan **VN-Spoke**.
12. I fältet **Undernät** klickar du på **SN-Workload**.
13. Klicka på **OK**.
14. Klicka på **Vägar** och sedan på **Lägg till**.
15. I fältet **Vägnamn** skriver du **FW-DG**.
16. I fältet **Adressprefix** skriver du **0.0.0.0/0**.
17. I fältet **Nästa hopptyp** väljer du **Virtuell installation**.

    Azure Firewall är egentligen en hanterad tjänst, men en virtuell installation fungerar i det här fallet.
1. I fältet **Nästa hoppadress** skriver du brandväggens privata IP-adress som du skrev ned tidigare.
2. Klicka på **OK**.


## <a name="configure-a-dnat-rule"></a>Konfigurera en DNAT-regel

```azurepowershell-interactive
 $rgName  = "RG-DNAT-Test"
 $firewallName = "FW-DNAT-test"
 $publicip = type the Firewall public ip
 $newAddress = type the private IP address for the Srv-Workload virtual machine 
 
# Get Firewall
    $firewall = Get-AzureRmFirewall -ResourceGroupName $rgName -Name $firewallName
  # Create NAT rule
    $natRule = New-AzureRmFirewallNatRule -Name RL-01 -SourceAddress * -DestinationAddress $publicip -DestinationPort 3389 -Protocol TCP -TranslatedAddress $newAddress -TranslatedPort 3389
  # Create NAT rule collection
    $natRuleCollection = New-AzureRmFirewallNatRuleCollection -Name RC-DNAT-01 -Priority 200 -Rule $natRule
  # Add NAT Rule collection to firewall:
    $firewall.AddNatRuleCollection($natRuleCollection)
  # Save:
    $firewall | Set-AzureRmFirewall
```
## <a name="configure-a-network-rule"></a>Konfigurera en nätverksregel

1. Öppna **RG-DNAT-Test** och klicka på brandväggen **FW-DNAT-test**.
1. På sidan **FW-DNAT-test**, under **Inställningar** klickar du på **Regler**.
2. Klicka på **Lägg till nätverksregelsamling**.

Konfigurera regeln med hjälp av följande tabell och klicka sedan på **Lägg till**:


|Parameter  |Värde  |
|---------|---------|
|Namn     |**RC-Net-01**|
|Prioritet     |**200**|
|Åtgärd     |**Tillåt**|

Under **Regler**:

|Parameter  |Inställning  |
|---------|---------|
|Namn     |**RL-RDP**|
|Protokoll     |**TCP**|
|Källadresser     |*|
|Måladresser     |**Srv-Workload** privat IP-adress|
|Målportar|**3389**|


## <a name="test-the-firewall"></a>Testa brandväggen

1. Anslut ett fjärrskrivbord till brandväggens offentliga IP-adress. Du ska vara ansluten till den virtuella datorn **Srv-Workload**.
3. Stäng fjärrskrivbordet.
4. Ändra åtgärden för nätverksregelsamling **RC-Net-01** till **Neka**.
5. Försök ansluta till brandväggens offentliga IP-adress igen. Den här gången borde det inte lyckas på grund av **Neka**-regeln.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **RG-DNAT-Test** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * Skapa en standardväg
> * Konfigurera en DNAT-regel
> * Konfigurera en nätverksregel
> * Testa brandväggen

Därefter kan du övervaka Azure Firewall-loggarna.

> [!div class="nextstepaction"]
> [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)
