<properties
    pageTitle="Skapa en skaluppsättning för virtuella datorer med PowerShell | Microsoft Azure"
    description="Skapa en skaluppsättning för virtuella datorer med PowerShell"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="davidmu"/>


# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Skapa en Windows-skaluppsättning för virtuella datorer med Azure PowerShell

Här följer en ”fyll i tomrummen-strategi” för hur du skapar en skaluppsättning för virtuella datorer i Azure. I [översikten över skaluppsättningar för virtuella datorer](virtual-machine-scale-sets-overview.md) kan du läsa mer om skaluppsättningar.

Det bör ta cirka 30 minuter att slutföra stegen i den här artikeln.

## <a name="step-1:-install-azure-powershell"></a>Steg 1: Installera Azure PowerShell

Se [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.

## <a name="step-2:-create-resources"></a>Steg 2: Skapa resurser

Skapa de resurser som krävs för den nya skaluppsättningen.

### <a name="resource-group"></a>Resursgrupp

En skaluppsättning för virtuella datorer måste ingå i en resursgrupp.

1. Hämta en lista över tillgängliga platser och tjänster som stöds:

        Get-AzureLocation | Sort Name | Select Name, AvailableServices

    Du bör se något som liknar det här exemplet:

        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}

2. Välj en plats som passar dig bäst och ersätt värdet **$locName** med platsens namn och skapa sedan variabeln:

        $locName = "location name from the list, such as Central US"

3. Ersätt värdet **$rgName** med namnet som du vill använda för den nya resursgruppen och skapa sedan variabeln: 

        $rgName = "resource group name"
        
4. Skapa resursgruppen:
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Du bör se något som liknar det här exemplet:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Lagringskonto

Ett lagringskonto används av en virtuell dator för att lagra operativsystemsdisken och diagnostikdata som används för skalning. När så är möjligt är det bäst att ha ett lagringskonto för varje virtuell dator som skapats i en skaluppsättning. Om det inte går bör du inte planera för fler än 20 VM per lagringskonto. I exemplet i den här artikeln skapas tre lagringskonton för tre virtuella datorer.

1. Ersätt värdet för **$saName** med ett namn för lagringskontot. Testa namnet för att se om det är unikt. 

        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName

    Om svaret är **Sant** är det föreslagna namnet unikt.

3. Ersätt värdet **$saType** med lagringskontots typ och skapa sedan variabeln:  

        $saType = "storage account type"
        
    Möjliga värden är: Standard_LRS, Standard_GRS, Standard_RAGRS och Premium_LRS.
        
4. Skapa kontot:
    
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Du bör se något som liknar det här exemplet:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

5. Upprepa steg 1 till 4 för att skapa tre lagringskonton, till exempel myst1, myst2 och myst3.

### <a name="virtual-network"></a>Virtuellt nätverk

Ett virtuellt nätverk krävs för de virtuella datorerna i skaluppsättningen.

1. Ersätt värdet **$subnetName** med namnet som du vill använda för undernätet i det virtuella nätverket och skapa sedan variabeln: 

        $subnetName = "subnet name"
        
2. Skapa undernätskonfigurationen:
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
    Adressprefixet kan skilja sig från adressprefixet i det virtuella nätverket.

3. Ersätt värdet för **$netName** med namnet som du vill använda för det virtuella nätverket och skapa sedan variabeln: 

        $netName = "virtual network name"
        
4. Skapa det virtuella nätverket:
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="public-ip-address"></a>Offentlig IP-adress

Innan du kan skapa ett nätverksgränssnitt måste du skapa en offentlig IP-adress.

1. Ersätt värdet för **$domName** med domännamnsetiketten som du vill använda med din offentliga IP-adress och skapa sedan variabeln:  

        $domName = "domain name label"
        
    Etiketten får endast innehålla bokstäver, siffror och bindestreck, och det sista tecknet måste vara en bokstav eller en siffra.
    
2. Testa om namnet är unikt:
    
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    Om svaret är **Sant** är det föreslagna namnet unikt.

3. Ersätt värdet för **$pipName** med namnet som du vill använda för den offentliga IP-adressen och skapa sedan variabeln. 

        $pipName = "public ip address name"
        
4. Skapa den offentliga IP-adressen:
    
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### <a name="network-interface"></a>Nätverksgränssnitt

När du nu har den offentliga IP-adressen kan du skapa nätverksgränssnittet.

1. Ersätt värdet för **$nicName** med namnet som du vill använda för nätverksgränssnittet och skapa sedan variabeln: 

        $nicName = "network interface name"
        
2. Skapa nätverksgränssnittet:
    
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### <a name="configuration-of-the-scale-set"></a>Konfiguration av skaluppsättningen

Du har alla resurser som du behöver för konfigurationen av skaluppsättningen, så nu skapar vi den.  

1. Ersätt värdet för **$ipName** med namnet som du vill använda för IP-konfigurationen. Skapa sedan variabeln: 

        $ipName = "IP configuration name"
        
2. Skapa IP-konfigurationen:

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Ersätt värdet för **$vmssConfig** med namnet som du vill använda för konfigurationen för skaluppsättningen och skapa sedan variabeln:   

        $vmssConfig = "Scale set configuration name"
        
3. Skapa konfigurationen för skaluppsättningen:

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    I det här exemplet visas en skaluppsättning som skapas med tre virtuella datorer. I [översikten över skaluppsättningar för virtuella datorer](virtual-machine-scale-sets-overview.md) finns mer information om kapaciteten för olika skaluppsättningar. Det här steget omfattar också storleken (kallas SkuName) för de virtuella datorerna i uppsättningen. Om du vill hitta en storlek som motsvarar dina behov kan du titta i [Storlekar för virtuella datorer](../virtual-machines/virtual-machines-windows-sizes.md).
    
4. Lägg till konfigurationen av nätverksgränssnittet i konfigurationen för skaluppsättningen:
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    Du bör se något som liknar det här exemplet:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Operativsystemprofil

1. Ersätt värdet **$computerName** med prefixet för datornamnet som du vill använda och skapa sedan variabeln: 

        $computerName = "computer name prefix"
        
2. Ersätt värdet för **$adminName**-namnet för administratörskontot på de virtuella datorerna och skapa sedan variabeln:

        $adminName = "administrator account name"
        
3. Ersätt värdet **$adminPassword** med kontolösenordet och skapa sedan variabeln:

        $adminPassword = "password for administrator accounts"
        
4. Skapa operativsystemprofilen:

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Lagringsprofil

1. Ersätt värdet för **$storageProfile** med namnet som du vill använda för lagringsprofilen och skapa sedan variabeln:  

        $storageProfile = "storage profile name"
        
2. Skapa variablerna som definierar den avbildning som du ska använda:  
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    Om du vill ha information om andra avbildningar du kan använda ska du titta i [Hitta och välj avbildningar av virtuella datorer i Azure med Windows PowerShell och Azure CLI](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).
        
3. Ersätt värdet för **$vhdContainers** med en lista som innehåller sökvägar till platser där de virtuella hårddiskarna lagras, till exempel https://mystorage.blob.core.windows.net/vhds, och skapa sedan variabeln:
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. Skapa lagringsprofilen:

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Skaluppsättning för virtuella datorer

Slutligen kan du skapa skaluppsättningen.

1. Ersätt värdet för **$vmssName** med namnet på skaluppsättningen för den virtuella datorn och skapa sedan variabeln:

        $vmssName = "scale set name"
        
2. Skapa skaluppsättningen:

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Det bör se ut ungefär som i det här exemplet där du kan se en slutförd distribution:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3:-explore-resources"></a>Steg 3: Utforska resurser

Använd dessa resurser för att utforska den skaluppsättning för virtuella datorer som du skapade:

- Azure Portal – En begränsad mängd information är tillgänglig genom portalen.
- [Resursutforskaren i Azure](https://resources.azure.com/) – Det här verktyget är bäst när du vill utforska det aktuella tillståndet för skaluppsättningen.
- Azure PowerShell – Använd det här kommandot för att hämta information:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## <a name="next-steps"></a>Nästa steg

- Hantera skaluppsättningen som du skapade med hjälp av informationen i [Hantera virtuella datorer i en skaluppsättning för virtuella datorer](virtual-machine-scale-sets-windows-manage.md)
- Överväg att konfigurera automatisk skalning av skaluppsättningen med hjälp av informationen i [Automatisk skalning och skaluppsättningar för virtuella datorer](virtual-machine-scale-sets-autoscale-overview.md)
- Läs mer om vertikal skalning i [Vertikal automatisk skalning med skaluppsättningar för virtuella datorer](virtual-machine-scale-sets-vertical-scale-reprovision.md)



<!--HONumber=Oct16_HO3-->


