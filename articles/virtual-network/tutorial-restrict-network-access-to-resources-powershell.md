---
title: Begränsa nätverksåtkomst till PaaS - resurser, Azure PowerShell | Microsoft Docs
description: Lär dig mer om att begränsa och begränsa nätverksåtkomst till Azure-resurser, till exempel Azure Storage och Azure SQL Database med virtuella nätverksslutpunkter med hjälp av PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 7e402af74babda2ce32d4a1597c61d71aba89b9e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Begränsa nätverksåtkomst till PaaS-resurser med virtuella nätverksslutpunkter med hjälp av PowerShell

Slutpunkter för virtuellt nätverk kan du begränsa nätverksåtkomsten till resurser i Azure-tjänsten som ett undernät för virtuellt nätverk. Du kan också ta bort internet-åtkomst till resurserna. Tjänsteslutpunkter ger direkt anslutning från det virtuella nätverket till stöds Azure-tjänster, så att du kan använda privata adressutrymmet för det virtuella nätverket för att komma åt Azure-tjänster. Trafik till Azure-resurser via Tjänsteslutpunkter alltid kvar på stamnät Microsoft Azure-nätverk. I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk med ett undernät
> * Lägg till ett undernät och aktivera en tjänstslutpunkt
> * Skapa en Azure-resurs och ge åtkomst till nätverket till den från bara ett undernät
> * Distribuera en virtuell dator (VM) till varje undernät
> * Bekräfta åtkomst till en resurs från ett undernät
> * Bekräfta att åtkomst nekas till en resurs från ett undernät och internet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt i den här artikeln kräver Azure PowerShell Modulversion 5.4.1 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Du måste skapa en resursgrupp för det virtuella nätverket och alla andra resurser som skapats i den här artikeln innan du skapar ett virtuellt nätverk. Skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup*: 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork* med adressprefixet *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Skapa en konfiguration av undernät med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). I följande exempel skapas en konfiguration av undernät för ett undernät med namnet *offentliga*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Skapa undernätet i det virtuella nätverket genom att skriva konfiguration av undernät till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork):

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt 

Du kan aktivera Tjänsteslutpunkter endast för tjänster som stöder slutpunkter. Visa tjänsten endpoint-aktiverade tjänster som är tillgängliga i en Azure-plats med [Get-AzureRmVirtualNetworkAvailableEndpointService](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice). I följande exempel returneras en lista över service-slutpunkt-aktiverade tjänster som är tillgängliga i den *eastus* region. Listan över tjänster som returneras kommer att växa med tiden när flera Azure-tjänster blir tjänstslutpunkten aktiverad.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

Skapa en ytterligare undernät i det virtuella nätverket. I det här exemplet, ett undernät med namnet *privata* skapas med en tjänstslutpunkt för *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-and-from-a-subnet"></a>Begränsa nätverksåtkomst till och från ett undernät

Skapa nätverkssäkerhet grupp säkerhetsregler med [ny AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Följande regel tillåter utgående åtkomst till de offentliga IP-adresser som tilldelats till tjänsten Azure Storage: 

```azurepowershell-interactive
$rule1 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Följande regel nekar åtkomst till offentliga IP-adresser. Tidigare regeln åsidosätter den här regeln på grund av högre prioritet, vilket ger åtkomst till offentliga IP-adresserna för Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Deny-internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Följande regel tillåter trafik för Remote Desktop Protocol (RDP) till undernätet för inkommande från var som helst. Anslutning till fjärrskrivbord tillåts undernät, så att du kan bekräfta nätverksåtkomst till en resurs i ett senare steg.

```azurepowershell-interactive
$rule3 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Skapa en nätverkssäkerhetsgrupp med [ny AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Associera nätverkssäkerhetsgruppen till den *privata* undernät med [Set AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) och sedan skriva konfiguration av undernät till det virtuella nätverket. I följande exempel associerar den *myNsgPrivate* nätverkssäkerhetsgruppen till den *privata* undernät:

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Begränsa nätverksåtkomsten till en resurs

Stegen för att begränsa nätverksåtkomsten till resurser som skapats via Azure-tjänster aktiverad för slutpunkter varierar tjänster. Finns i dokumentationen för enskilda tjänster för specifika steg för varje tjänst. Resten av den här artikeln innehåller steg för att begränsa nätverksåtkomsten för ett Azure Storage-konto, som exempel.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett Azure storage-konto med [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Ersätt `<replace-with-your-unique-storage-account-name>` med ett namn som är unikt för alla Azure platser mellan 3 till 24 tecken, med endast siffror och gemener.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

När lagringskontot har skapats kan du hämta nyckeln för lagringskontot i en variabel med [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

Nyckeln används för att skapa en filresurs i ett senare steg. Ange `$storageAcctKey` och anteckna värdet för, som du måste också ange den manuellt i ett senare steg när du mappar filresursen till en enhet på en virtuell dator.

### <a name="create-a-file-share-in-the-storage-account"></a>Skapa en filresurs i storage-konto

Skapa en kontext för ditt lagringskonto och nyckeln med [ny AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext). Kontexten innehåller lagringskontots namn och åtkomstnyckel:

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

Skapa en filresurs med [ny AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare):

$share = New-AzureStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Neka alla nätverksåtkomst till ett lagringskonto

Storage-konton godkänna anslutningar från klienter i ett nätverk som standard. Om du vill begränsa åtkomsten till valda nätverken, ändra standardåtgärden att *neka* med [uppdatering AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). Lagringskontot är inte tillgänglig från alla nätverk när nätverksåtkomst nekas.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Aktivera nätverksåtkomst från ett undernät

Hämta det skapade virtuellt nätverket med [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) och sedan hämta privata undernätsobjekt i en variabel med [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

Tillåt nätverksåtkomst till storage-konto från den *privata* undernät med [Lägg till AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzureRmStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Distribuera en virtuell dator till varje undernät för att testa nätverksåtkomst till ett lagringskonto.

### <a name="create-the-first-virtual-machine"></a>Skapa den första virtuella datorn

Skapa en virtuell dator i den *offentliga* undernät med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). När du kör det kommando som följer tillfrågas du om autentiseringsuppgifter. De värden som du anger är konfigurerade som användarnamn och lösenord för den virtuella datorn. Den `-AsJob` alternativet skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Utdata som liknar följande exempel utdata returneras:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-virtual-machine"></a>Skapa den andra virtuella datorn

Skapa en virtuell dator i den *privata* undernät:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Det tar några minuter för Azure för att skapa den virtuella datorn. Fortsätt inte till nästa steg förrän Azure har skapat den virtuella datorn och returnerar utdata till PowerShell. 

## <a name="confirm-access-to-storage-account"></a>Bekräfta åtkomst till lagringskontot

Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) att returnera offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den *myVmPrivate* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ersätt `<publicIpAddress>` i följande kommando, med den offentliga IP-adress returnerades från föregående kommando och ange följande kommando: 

```powershell
mstsc /v:<publicIpAddress>
```

En Remote Desktop Protocol (RDP)-fil skapas och hämtas till datorn. Öppna hämtade rdp-filen. Välj **Anslut**. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn. Du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn. Välj **OK**. Du kan få en certifikatvarning under inloggningen. Om varningen, väljer **Ja** eller **Fortsätt**, för att fortsätta med anslutningen.

På den *myVmPrivate* VM, mappa Azure-filresursen enhet Z med hjälp av PowerShell. Innan du kör kommandona som följer ersätta `<storage-account-key>` och `<storage-account-name>` med värden från du anges eller hämtas i [skapa ett lagringskonto](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```
PowerShell returnerar utdata som liknar följande exempel utdata:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Azure-filresursen som mappats till Z-enheten.

Bekräfta att den virtuella datorn har ingen utgående anslutning till alla andra offentliga IP-adresser:

```powershell
ping bing.com
```

Du får inga svar eftersom nätverkssäkerhetsgruppen som är kopplat till den *privata* undernät inte tillåter utgående åtkomst till den offentliga IP-adresser än de adresser som tilldelats till tjänsten Azure Storage.

Stäng fjärrskrivbordssession till den *myVmPrivate* VM.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bekräfta att åtkomst nekas till storage-konto

Hämta den offentliga IP-adressen för den *myVmPublic* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ersätt `<publicIpAddress>` i följande kommando, med den offentliga IP-adress returnerades från föregående kommando och ange följande kommando: 

```powershell
mstsc /v:<publicIpAddress>
```

På den *myVmPublic* VM, försök att mappa Azure-filresursen Z-enheten. Innan du kör kommandona som följer ersätta `<storage-account-key>` och `<storage-account-name>` med värden från du anges eller hämtas i [skapa ett lagringskonto](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Åtkomst nekad till resursen och du får en `New-PSDrive : Access is denied` fel. Åtkomst nekas eftersom den *myVmPublic* VM distribueras i det *offentliga* undernät. Den *offentliga* undernätet har inte en tjänstslutpunkt aktiverat för Azure Storage och lagringskontot kan bara åtkomst till nätverket från den *privata* undernät, inte den *offentliga*undernät.

Stäng fjärrskrivbordssession till den *myVmPublic* VM.

Från datorn, försök visa filresurser i storage-konto med följande kommando:

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Åtkomst nekad, och du får en *Get-AzureStorageFile: fjärrservern returnerade ett fel: (403) förbjuden. HTTP-statuskod: 403 - HTTP-felmeddelande: denna begäran har inte behörighet att utföra den här åtgärden* fel, eftersom datorn inte är i den *privata* undernätet för den *MyVirtualNetwork* virtuella nätverk.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) att ta bort resursgruppen och alla resurser som den innehåller:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du har aktiverat en tjänstslutpunkt för ett undernät för virtuellt nätverk i den här självstudiekursen. Du har lärt dig att slutpunkter kan aktiveras för resurser som har distribuerats med Azure-tjänster. Du har skapat ett Azure Storage-konto och begränsad nätverksåtkomst till lagringskontot endast resurser inom ett undernät för virtuellt nätverk. Innan du skapar slutpunkter i produktion virtuella nätverk, rekommenderas att du noggrant bekanta dig med [tjänstens slutpunkter](virtual-network-service-endpoints-overview.md).

Om du har flera virtuella nätverk i ditt konto kan du vill ansluta två virtuella nätverk tillsammans så resurser inom varje virtuellt nätverk kan kommunicera med varandra. Gå vidare till nästa kurs att lära dig att ansluta virtuella nätverk.

> [!div class="nextstepaction"]
> [Ansluta virtuella nätverk](./tutorial-connect-virtual-networks-powershell.md)
