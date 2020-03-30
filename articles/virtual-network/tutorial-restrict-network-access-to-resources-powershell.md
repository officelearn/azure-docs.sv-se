---
title: Begränsa nätverksåtkomsten till PaaS-resurser – Azure PowerShell
description: I den här artikeln får du lära dig hur du begränsar och begränsar nätverksåtkomst till Azure-resurser, till exempel Azure Storage och Azure SQL Database, med slutpunkter för virtuella nätverkstjänster med Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1d0cf65bb39dbda2b7451c50629ff8949c5507cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185543"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Begränsa nätverksåtkomsten till PaaS-resurser med slutpunkter för virtuella nätverkstjänster med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Med tjänstslutpunkter för virtuellt nätverk kan du begränsa nätverksåtkomsten till vissa Azure-tjänsters resurser till ett undernät för virtuella datorer. Du kan också ta bort resursernas internetåtkomst. Tjänstslutpunkterna möjliggör direktanslutning från ditt virtuella nätverk till Azure-tjänster som stöds, så att du kan använda det privata adressutrymmet i det virtuella nätverket för åtkomst till Azure-tjänsterna. Trafik till Azure-resurser genom tjänstslutpunkterna finns alltid kvar i Microsoft Azure-stamnätverket. I den här artikeln kan du se hur du:

* Skapa ett virtuellt nätverk med ett undernät
* Lägga till ett undernät och aktivera en tjänstslutpunkt
* Skapa en Azure-resurs och tillåt nätverksåtkomst till den från enbart ett undernät
* Distribuera en virtuell dator (VM) till varje undernät
* Bekräfta åtkomst till en resurs från ett undernät
* Bekräfta att åtkomst nekas för en resurs från ett undernät och internet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Innan du skapar ett virtuellt nätverk måste du skapa en resursgrupp för det virtuella nätverket och alla andra resurser som skapas i den här artikeln. Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup:* 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork* med adressprefixet *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Skapa en undernätskonfiguration med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). I följande exempel skapas en undernätskonfiguration för ett undernät med namnet *Offentlig:*

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Skapa undernätet i det virtuella nätverket genom att skriva undernätskonfigurationen till det virtuella nätverket med [Set-AzVirtualNetwork:](/powershell/module/az.network/Set-azVirtualNetwork)

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt

Du kan bara aktivera tjänstslutpunkter för tjänster som stöder tjänstslutpunkter. Visa tjänstslutpunktsaktiverade tjänster som är tillgängliga på en [Azure-plats med Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice). I följande exempel returneras en lista över tjänstslutpunktsaktiverade tjänster som är tillgängliga i *regionen eastus.* Listan över returnerade tjänster växer med tiden när fler Azure-tjänster blir tjänstslutpunkt aktiverad.

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

Skapa ytterligare ett undernät i det virtuella nätverket. I det här exemplet skapas ett undernät med namnet *Privat* med en tjänstslutpunkt för *Microsoft.Storage:* 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Begränsa nätverksåtkomst för ett undernät

Skapa säkerhetsregler för nätverkssäkerhetsgrupper med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Följande regel tillåter utgående åtkomst till de offentliga IP-adresser som tilldelats Azure Storage-tjänsten: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
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

Följande regel nekar åtkomst till alla offentliga IP-adresser. Den tidigare regeln åsidosätter den här regeln på grund av dess högre prioritet, vilket ger åtkomst till de offentliga IP-adresserna för Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
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
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Begränsa nätverksåtkomst till en resurs

De steg som behövs för att begränsa nätverksåtkomsten till resurser som har skapats via Azure-tjänster som är aktiverade för tjänstslutpunkter varierar från tjänst till tjänst. Läs dokumentationen för enskilda tjänster för specifika åtgärder för varje tjänst. Resten av den här artikeln innehåller steg för att begränsa nätverksåtkomsten för ett Azure Storage-konto, som ett exempel.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Skapa ett Azure-lagringskonto med [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Ersätt `<replace-with-your-unique-storage-account-name>` med ett namn som är unikt på alla Azure-platser, mellan 3-24 tecken i längd, med endast siffror och gemener.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

När lagringskontot har skapats hämtar du nyckeln för lagringskontot till en variabel med [Get-AzStorageAccountKey:](/powershell/module/az.storage/get-azstorageaccountkey)

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

Nyckeln används för att skapa en filresurs i ett senare steg. Ange `$storageAcctKey` och notera värdet, eftersom du också måste ange det manuellt i ett senare steg när du mappar filresursen till en enhet i en virtuell dator.

### <a name="create-a-file-share-in-the-storage-account"></a>Skapa en filresurs i lagringskontot

Skapa en kontext för ditt lagringskonto och nyckel med [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). Kontexten kapslar in lagringskontots namn och kontonyckel:

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

Skapa en filresurs med [New-AzStorageShare:](/powershell/module/az.storage/new-azstorageshare)

$share = Ny-AzStorageDel min-fil-aktie -Kontext $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Neka all nätverksåtkomst till ett lagringskonto

Som standard godkänner lagringskonton nätverksanslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk ändrar du standardåtgärden till *Neka* med [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). När nätverksåtkomsten nekas är lagringskontot inte tillgängligt från något nätverk.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Aktivera nätverksåtkomst från ett undernät

Hämta det skapade virtuella nätverket med [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) och hämta sedan det privata undernätsobjektet till en variabel med [Get-AzVirtualNetworkSubnetConfig:](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

Tillåt nätverksåtkomst till lagringskontot från det *privata* undernätet med [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Om du vill testa nätverksåtkomsten till ett lagringskonto distribuerar du en virtuell dator till varje undernät.

### <a name="create-the-first-virtual-machine"></a>Skapa din första virtuella dator

Skapa en virtuell dator i det *offentliga* undernätet med [New-AzVM](/powershell/module/az.compute/new-azvm). När du kör kommandot nedan uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn. Alternativet `-AsJob` skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Utdata som liknar följande exempelutdata returneras:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>Skapa den andra virtuella datorn

Skapa en virtuell dator i undernätet *Privat:*

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Det tar några minuter för Azure att skapa den virtuella datorn. Fortsätt inte till nästa steg förrän Azure har skapat den virtuella datorn och returnerar utdata till PowerShell.

## <a name="confirm-access-to-storage-account"></a>Bekräfta åtkomst till lagringskontot

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

På den virtuella datorn *myVmPrivate* mappar du Azure-fildelningen till enhet Z med PowerShell. Innan du kör de kommandon `<storage-account-key>` `<storage-account-name>` som följer ersätter du och med värden från dig som har angett eller hämtats i [Skapa ett lagringskonto](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell returnerar utdata som liknar följande exempelutdata:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Azure-fildelningen har mappats till enhet Z.

Bekräfta att den virtuella datorn inte har någon utgående anslutning till andra offentliga IP-adresser:

```powershell
ping bing.com
```

Du får inga svar eftersom nätverkssäkerhetsgruppen som är kopplad till det *privata* undernätet inte tillåter utgående åtkomst till offentliga IP-adresser förutom de adresser som är kopplade till Azure Storage-tjänsten.

Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bekräfta att åtkomst till lagringskontot nekas

Hämta den offentliga IP-adressen för *myVmPublic* VM:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ersätt `<publicIpAddress>` i följande kommando med den offentliga IP-adressen som returnerades från föregående kommando och ange sedan följande kommando: 

```powershell
mstsc /v:<publicIpAddress>
```

Försök att mappa Azure-filresursen på den virtuella datorn *myVmPublic* till enhet Z. Innan du kör de kommandon `<storage-account-key>` `<storage-account-name>` som följer ersätter du och med värden från dig som har angett eller hämtats i [Skapa ett lagringskonto](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Åtkomst till resursen nekas och `New-PSDrive : Access is denied` du får ett felmeddelande. Åtkomst nekas eftersom den virtuella datorn *myVmPublic* distribueras i det *offentliga* undernätet. Det *offentliga* undernätet har ingen tjänstslutpunkt aktiverad för Azure Storage, och lagringskontot tillåter endast nätverksåtkomst från det *privata* undernätet, inte det *offentliga*.

Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPublic*.

Från datorn försöker du visa filresurserna i lagringskontot med följande kommando:

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Åtkomst nekas och du får en *Get-AzStorageFile: Fjärrservern returnerade ett fel: (403) Förbjudet. HTTP-statuskod: 403 - HTTP-felmeddelande: Den här begäran har inte behörighet att utföra det här åtgärdsfelet,* eftersom datorn inte finns i det *privata* undernätet i det virtuella nätverket *MyVirtualNetwork.*

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla resurser som den innehåller:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du aktiverat en tjänstslutpunkt för ett virtuellt nätverksundernät. Du har lärt dig att tjänstslutpunkter kan aktiveras för resurser som distribueras med flera Azure-tjänster. Du har skapat ett Azure Storage-konto och begränsat nätverksåtkomst för lagringskontot till enbart resurser inom ett undernät för ett virtuellt nätverk. Om du vill veta mer om tjänstslutpunkter går du till [Översikt över tjänstslutpunkter](virtual-network-service-endpoints-overview.md) och [Hantera undernät](virtual-network-manage-subnet.md).

Om du har flera virtuella nätverk i ditt konto kanske du vill ansluta två virtuella nätverk så att resurserna i vart och ett kan kommunicera med varandra. Mer information finns i [Ansluta virtuella nätverk](tutorial-connect-virtual-networks-powershell.md).
