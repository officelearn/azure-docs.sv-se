---
title: Skapa en privat Azure-slutpunkt med Azure PowerShell | Microsoft Docs
description: Lär dig mer om Azures privata länk
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 0c6fc36be101679cea3a770f311005f63c3f0d66
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737384"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Skapa en privat slut punkt med hjälp av Azure PowerShell
En privat slut punkt är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. Virtual Machines (VM), att kommunicera privat med privata länk resurser. 

I den här snabb starten får du lära dig hur du skapar en virtuell dator på en Azure-Virtual Network, en logisk SQL-Server med en privat Azure-slutpunkt med Azure PowerShell. Sedan kan du på ett säkert sätt komma åt SQL Database från den virtuella datorn.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa dina resurser måste du skapa en resurs grupp som är värd för Virtual Network och den privata slut punkten med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *västkusten* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
I det här avsnittet skapar du ett virtuellt nätverk och ett undernät. Sedan kopplar du under nätet till Virtual Network.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk för din privata slut punkt med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas en Virtual Network med namnet *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Lägg till ett undernät

Azure distribuerar resurser till ett undernät inom en Virtual Network, så du måste skapa ett undernät. Skapa en under näts konfiguration med namnet *mitt undernät* med [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). I följande exempel skapas ett undernät med namnet *mitt undernät* med flaggan för nätverks princip för privat slut punkt inställd på **inaktive rad**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> Det är enkelt att förväxla `PrivateEndpointNetworkPoliciesFlag` parametern med en annan tillgänglig flagga, `PrivateLinkServiceNetworkPoliciesFlag` eftersom de är båda långa orden och har liknande utseende.  Kontrol lera att du använder rätt ett, `PrivateEndpointNetworkPoliciesFlag` .

### <a name="associate-the-subnet-to-the-virtual-network"></a>Koppla under nätet till Virtual Network

Du kan skriva under näts konfigurationen till Virtual Network med [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Det här kommandot skapar undernätet:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator i Virtual Network med [New-AzVM](/powershell/module/az.compute/new-azvm). När du kör följande kommando måste du ange autentiseringsuppgifter. Ange ett användarnamn och lösenord för den virtuella datorn:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

Alternativet `-AsJob` skapar den virtuella datorn i bakgrunden. Du kan fortsätta till nästa steg.

När Azure börjar skapa den virtuella datorn i bakgrunden får du tillbaka något liknande detta:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-logical-sql-server"></a>Skapa en logisk SQL-Server 

Skapa en logisk SQL-Server med kommandot New-AzSqlServer. Kom ihåg att namnet på servern måste vara unikt i Azure, så Ersätt plats hållarens värde inom hakparenteser med ditt eget unika värde:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

Privat slut punkt för servern i Virtual Network med [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet `
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Konfigurera Privat DNS zon 
Skapa en Privat DNS zon för SQL Database domän, skapa en kopplings länk med Virtual Network och skapa en DNS-zon för att koppla den privata slut punkten till Privat DNS zon.

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  

$config = New-AzPrivateDnsZoneConfig -Name "privatelink.database.windows.net" -PrivateDnsZoneId $zone.ResourceId

$privateDnsZoneGroup = New-AzPrivateDnsZoneGroup -ResourceGroupName "myResourceGroup" `
 -PrivateEndpointName "myPrivateEndpoint" -name "MyZoneGroup" -PrivateDnsZoneConfig $config
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Använd [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) för att returnera den offentliga IP-adressen för en virtuell dator. Det här exemplet returnerar den offentliga IP-adressen för den virtuella *myVM* -datorn:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Öppna en kommandotolk på den lokala datorn. Kör mstsc-kommandot. Ersätt <publicIpAddress> med den offentliga IP-adress som returnerades från det senaste steget: 


> [!NOTE]
> Om du har kört dessa kommandon från en PowerShell-kommandotolk på din lokala dator, och du använder version 1.0 av Az PowerShell-modulen eller senare, kan du fortsätta i det gränssnittet.
```
mstsc /v:<publicIpAddress>
```

1. Välj **Anslut** om du uppmanas att göra det. 
2. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn.
  > [!NOTE]
  > Du kan behöva välja fler alternativ > använda ett annat konto för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn. 
  
3. Välj **OK**. 
4. Du kan få en certifikatsvarning. Om så är fallet väljer du **Ja** eller **Fortsätt**. 

## <a name="access-sql-database-privately-from-the-vm"></a>Åtkomst SQL Database privat från den virtuella datorn

1. Öppna PowerShell i fjärr skrivbordet för myVM.
2. Ange `nslookup myserver.database.windows.net`. Kom ihåg att ersätta `myserver` med ditt SQL Server-namn.

    Du får ett meddelande som liknar detta:
    
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
    
3. Installera [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).
4. I **Anslut till Server**anger eller väljer du den här informationen:

    | Inställningen | Värde |
    | --- | --- |
    | Servertyp | Databasmotor |
    | Servernamn | myserver.database.windows.net |
    | Användarnamn | Ange det användar namn som angavs vid skapandet |
    | lösenordsinställning | Ange lösen ordet som angavs när det skapas |
    | Kom ihåg lösen ord | Yes |
    
5. Välj **Anslut**.
6. Bläddra bland **databaser** på den vänstra menyn. 
7. Du kan också Skapa eller fråga efter information från databasen.
8. Stäng fjärr skrivbords anslutningen till *myVM*. 

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten SQL Database och den virtuella datorn använder du [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser den har:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azures privata länk](private-link-overview.md)
