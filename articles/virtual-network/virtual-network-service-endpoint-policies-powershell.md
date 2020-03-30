---
title: Begränsa dataexfiltration till Azure Storage - Azure PowerShell
description: I den här artikeln får du lära dig hur du begränsar och begränsar virtuell nätverksdataexfiltration till Azure Storage-resurser med slutpunktsprinciper för virtuella nätverkstjänster med Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 673431e2ddfc9a641bb1c640891daac79350cb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78253031"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Hantera dataexfiltration till Azure Storage-konton med slutpunktsprinciper för virtuella nätverkstjänster med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Med slutpunktsprinciper för virtuella nätverkstjänster kan du tillämpa åtkomstkontroll på Azure Storage-konton inifrån ett virtuellt nätverk över tjänstslutpunkter. Detta är en nyckel till att skydda dina arbetsbelastningar, hantera vilka lagringskonton som är tillåtna och var dataexfiltration är tillåtet.
I den här artikeln kan du se hur du:

* Skapa ett virtuellt nätverk.
* Lägg till ett undernät och aktivera tjänstslutpunkt för Azure Storage.
* Skapa två Azure Storage-konton och tillåt nätverksåtkomst till det från undernätet som skapats ovan.
* Skapa en princip för tjänstslutpunkt för att endast tillåta åtkomst till ett av lagringskontona.
* Distribuera en virtuell dator (VM) till undernätet.
* Bekräfta åtkomst till det tillåtna lagringskontot från undernätet.
* Bekräfta åtkomst nekas till det icke tillåtna lagringskontot från undernätet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Innan du skapar ett virtuellt nätverk måste du skapa en resursgrupp för det virtuella nätverket och alla andra resurser som skapas i den här artikeln. Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup:* 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork* med adressprefixet *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt

Skapa ett undernät i det virtuella nätverket. I det här exemplet skapas ett undernät med namnet *Privat* med en tjänstslutpunkt för *Microsoft.Storage:* 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Begränsa nätverksåtkomsten för undernätet

Skapa säkerhetsregler för nätverkssäkerhetsgrupper med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Följande regel tillåter utgående åtkomst till de offentliga IP-adresser som tilldelats Azure Storage-tjänsten: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Följande regel nekar åtkomst till alla offentliga IP-adresser. Den tidigare regeln åsidosätter den här regeln på grund av dess högre prioritet, vilket ger åtkomst till de offentliga IP-adresserna för Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Följande regel tillåter fjärrskrivbordsprotokoll (RDP) trafik som kommer till undernätet var som helst. Fjärrskrivbordsanslutningar tillåts till undernätet, så att du kan bekräfta nätverksåtkomst till en resurs i ett senare steg.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
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

Skapa en nätverkssäkerhetsgrupp med [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Associera nätverkssäkerhetsgruppen till det *privata* undernätet med [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) och skriv sedan undernätskonfigurationen till det virtuella nätverket. I följande exempel associeras säkerhetsgruppen *myNsgPrivate-nätverk* till det *privata* undernätet:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Begränsa nätverksåtkomsten till Azure Storage-konton

De steg som behövs för att begränsa nätverksåtkomsten till resurser som har skapats via Azure-tjänster som är aktiverade för tjänstslutpunkter varierar från tjänst till tjänst. Läs dokumentationen för enskilda tjänster för specifika åtgärder för varje tjänst. Resten av den här artikeln innehåller steg för att begränsa nätverksåtkomsten för ett Azure Storage-konto, som ett exempel.

### <a name="create-two-storage-accounts"></a>Skapa två lagringskonton

Skapa ett Azure-lagringskonto med [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

När lagringskontot har skapats hämtar du nyckeln för lagringskontot till en variabel med [Get-AzStorageAccountKey:](/powershell/module/az.storage/get-azstorageaccountkey)

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

Nyckeln används för att skapa en filresurs i ett senare steg. Ange `$storageAcctKey` och notera värdet, eftersom du också måste ange det manuellt i ett senare steg när du mappar filresursen till en enhet i en virtuell dator.

Upprepa nu ovanstående steg för att skapa ett andra lagringskonto.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Hämta även lagringskontonyckeln från det här kontot för att senare skapa en filresurs.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Skapa en filresurs i vart och ett av lagringskontot

Skapa en kontext för ditt lagringskonto och nyckel med [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). Kontexten kapslar in lagringskontots namn och kontonyckel:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Skapa en filresurs med [New-AzStorageShare:](/powershell/module/az.storage/new-azstorageshare)

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Neka all nätverksåtkomst till ett lagringskonton

Som standard godkänner lagringskonton nätverksanslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk ändrar du standardåtgärden till *Neka* med [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). När nätverksåtkomsten nekas är lagringskontot inte tillgängligt från något nätverk.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Aktivera endast nätverksåtkomst från VNet-undernätet

Hämta det skapade virtuella nätverket med [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) och hämta sedan det privata undernätsobjektet till en variabel med [Get-AzVirtualNetworkSubnetConfig:](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Tillåt nätverksåtkomst till lagringskontot från det *privata* undernätet med [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Tillämpa principen för att tillåta åtkomst till giltigt lagringskonto

Om du vill vara säker på att användarna i det virtuella nätverket bara kan komma åt Azure Storage-konton som är säkra och tillåtna kan du skapa en princip för tjänstslutpunkt med listan över tillåtna lagringskonton i definitionen. Den här principen tillämpas sedan på det virtuella nätverksundernätet som är anslutet till lagring via tjänstslutpunkter.

### <a name="create-a-service-endpoint-policy"></a>Skapa en princip för tjänstslutpunkt

I det här avsnittet skapas principdefinitionen med listan över tillåtna resurser för åtkomst via tjänstens slutpunkt

Hämta resurs-ID:t för det första (tillåtna) lagringskontot 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Skapa principdefinitionen så att ovanstående resurs tillåts

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Skapa princip för tjänstslutpunkt med hjälp av principdefinitionen som skapats ovan

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Associera tjänstslutpunktsprincipen till det virtuella nätverksundernätet

När du har skapat princip för tjänstslutpunkt associeras den med målundernätet med tjänstslutpunktskonfigurationen för Azure Storage.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Validera åtkomstbegränsning till Azure Storage-konton

### <a name="deploy-the-virtual-machine"></a>Distribuera den virtuella datorn

Om du vill testa nätverksåtkomst till ett lagringskonto distribuerar du en virtuell dator i undernätet.

Skapa en virtuell dator i det *privata* undernätet med [New-AzVM](/powershell/module/az.compute/new-azvm). När du kör kommandot nedan uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn. Alternativet `-AsJob` skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Utdata som liknar följande exempelutdata returneras:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Bekräfta åtkomst till det *tillåtna* lagringskontot

Använd [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att returnera den offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den virtuella datorn *myVmPrivate:*

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ersätt `<publicIpAddress>` i följande kommando med den offentliga IP-adressen som returnerades från föregående kommando och ange sedan följande kommando:

```powershell
mstsc /v:<publicIpAddress>
```

En RDP-fil (Remote Desktop Protocol) skapas och laddas ned till datorn. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn. Välj **OK**. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

På den *virtuella datorn myVmPrivate* mappar Azure-filresursen från tillåtna lagringskontot till enhet Z med PowerShell. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell returnerar utdata som liknar följande exempelutdata:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Azure-fildelningen har mappats till enhet Z.

Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPrivate*.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Bekräfta att åtkomst nekas icke *tillåtna* lagringskonto

På samma *virtuella myVmPrivate-dator* försöker du mappa Azure-filresursen till enhet X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

Åtkomst till resursen nekas och `New-PSDrive : Access is denied` du får ett felmeddelande. Åtkomst nekas eftersom lagringskontot *inte tillåts inte finns* i listan över tillåtna resurser i tjänstslutpunktsprincipen. 

Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPublic*.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla resurser som den innehåller:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tillämpat en princip för tjänstslutpunkt över en Azure-slutpunkt för virtuella nätverkstjänsten på Azure Storage. Du skapade Azure Storage-konton och begränsad nätverksåtkomst till endast vissa lagringskonton (och därmed nekat andra) från ett virtuellt nätverksundernät. Mer information om tjänstslutpunktsprinciper finns i Översikt över principer för [tjänstslutpunkter](virtual-network-service-endpoint-policies-overview.md).
