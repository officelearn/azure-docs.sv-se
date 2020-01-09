---
title: Skapa en privat Azure-slutpunkt med Azure PowerShell | Microsoft Docs
description: Lär dig mer om Azures privata länk
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 60032677594537f1e7791b7108eebd5d4cfad5b4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430353"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Skapa en privat slut punkt med hjälp av Azure PowerShell
En privat slut punkt är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. Virtual Machines (VM), att kommunicera privat med privata länk resurser. 

I den här snabb starten får du lära dig hur du skapar en virtuell dator på en Azure-Virtual Network, en SQL Database-Server med en privat Azure-slutpunkt med hjälp av Azure PowerShell. Sedan kan du på ett säkert sätt komma åt SQL Database-servern från den virtuella datorn.

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
> Det är enkelt att blanda `PrivateEndpointNetworkPoliciesFlag` parameter med en annan tillgänglig flagga `PrivateLinkServiceNetworkPoliciesFlag`, eftersom de är båda långa orden och har liknande utseende.  Kontrol lera att du använder rätt `PrivateEndpointNetworkPoliciesFlag`.

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

## <a name="create-a-sql-database-server"></a>Skapa en SQL Database-Server 

Skapa en SQL Database-Server genom att använda kommandot New-AzSqlServer. Kom ihåg att namnet på din SQL Database Server måste vara unikt i Azure, så Ersätt plats hållarens värde inom hakparenteser med ditt eget unika värde:

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

Privat slut punkt för SQL Database-servern i Virtual Network med [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

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
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Konfigurera Privat DNS zon 
Skapa en privat DNS-zon för SQL Database Server domän och skapa en kopplings länk med det virtuella nätverket: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
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

## <a name="access-sql-database-server-privately-from-the-vm"></a>Åtkomst SQL Database Server privat från den virtuella datorn

1. Öppna PowerShell i fjärr skrivbordet för myVM.
2. Ange `nslookup myserver.database.windows.net`. 

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Installera SQL Server Management Studio
4. I Anslut till server anger eller väljer du den här informationen: Ange värde Server Typ Välj databas motor.
      Server namn Välj myserver.database.windows.net användar namn Ange ett användar namn som angavs vid skapandet.
      Lösen ord ange ett lösen ord som angavs när det skapas.
      Kom ihåg lösen ord Välj Ja.
5. Välj Anslut.
6. Bläddra bland databaser från menyn till vänster. 
7. Du kan också Skapa eller fråga efter information från en databas
8. Stäng fjärr skrivbords anslutningen till *myVM*. 

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten, SQL Database Server och den virtuella datorn använder du [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser den har:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azures privata länk](private-link-overview.md)
