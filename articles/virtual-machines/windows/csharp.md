---
title: 'Skapa och hantera en virtuell Azure-dator med hjälp av C #'
description: Använd C# och Azure Resource Manager för att distribuera en virtuell dator och alla dess stöd resurser.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 07c66b2955f3df1ffae1a0cb0c2b0888bdc790e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82082891"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Skapa och hantera virtuella Windows-datorer i Azure med hjälp av C # #

En [virtuell Azure-dator](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) behöver flera stöd för Azure-resurser. Den här artikeln beskriver hur du skapar, hanterar och tar bort virtuella dator resurser med C#. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Visual Studio-projekt
> * Installera paketet
> * Skapa autentiseringsuppgifter
> * Skapa resurser
> * Utföra hanterings uppgifter
> * Ta bort resurser
> * Köra appen

Det tar ungefär 20 minuter att utföra dessa steg.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Om du inte redan har gjort det installerar du [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Välj **.net Desktop Development** på sidan arbets belastningar och klicka sedan på **Installera**. I sammanfattningen kan du se att **.NET Framework 4-4,6 utvecklingsverktyg** väljs automatiskt åt dig. Om du redan har installerat Visual Studio kan du lägga till .NET-arbetsbelastningen med hjälp av Visual Studio Launcher.
2. I Visual Studio klickar du på **fil** > **nytt** > **projekt**.
3. I **mallar** > **Visual C#** väljer du **konsol program (.NET Framework)**, anger *myDotnetProject* som namn på projektet, väljer projektets plats och klickar sedan på **OK**.

## <a name="install-the-package"></a>Installera paketet

NuGet-paket är det enklaste sättet att installera de bibliotek som du behöver för att slutföra de här stegen. Gör så här för att hämta de bibliotek som du behöver i Visual Studio:

1. Klicka på **verktyg** > **NuGet Package Manager**och klicka sedan på **Package Manager-konsolen**.
2. Skriv det här kommandot i-konsolen:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Innan du startar det här steget ska du kontrol lera att du har åtkomst till ett [Active Directory tjänstens huvud namn](../../active-directory/develop/howto-create-service-principal-portal.md). Du bör också registrera program-ID, autentiseringsnyckel och klient-ID som du behöver i ett senare steg.

### <a name="create-the-authorization-file"></a>Skapa verifierings filen

1. I Solution Explorer högerklickar du på *myDotnetProject* > **Lägg till** > **nytt objekt**och väljer sedan **textfil** i *Visual C#-objekt*. Ge filen namnet *azureauth. Properties*och klicka sedan på **Lägg till**.
2. Lägg till följande egenskaper för auktorisering:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Ersätt ** &lt;prenumerations-&gt; ID** med prenumerations-ID, ** &lt;program&gt; -ID** med Active Directory-program-ID, ** &lt;autentisering-&gt; nyckel** med program nyckeln och ** &lt;klient-&gt; ID** med klient-ID: t.

3. Spara filen azureauth. Properties. 
4. Ange en miljö variabel i Windows med namnet AZURE_AUTH_LOCATION med den fullständiga sökvägen till den auktoriserade filen som du skapade. Du kan till exempel använda följande PowerShell-kommando:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Skapa hanterings klienten

1. Öppna Program.cs-filen för det projekt som du har skapat. Lägg sedan till dessa med-instruktioner till de befintliga instruktionerna överst i filen:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Lägg till den här koden i huvud metoden för att skapa hanterings klienten:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Skapa resurser

### <a name="create-the-resource-group"></a>Skapa en resursgrupp

Alla resurser måste finnas i en [resurs grupp](../../azure-resource-manager/management/overview.md).

Om du vill ange värden för programmet och skapa resurs gruppen lägger du till den här koden i huvud metoden:

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Skapa tillgänglighets uppsättningen

[Tillgänglighets uppsättningar](tutorial-availability-sets.md) gör det enklare för dig att underhålla de virtuella datorer som används av ditt program.

Om du vill skapa tillgänglighets uppsättningen lägger du till den här koden i huvud metoden:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Skapa den offentliga IP-adressen

En [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) krävs för att kommunicera med den virtuella datorn.

Om du vill skapa den offentliga IP-adressen för den virtuella datorn lägger du till den här koden i huvud metoden:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

En virtuell dator måste finnas i ett undernät för ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

Om du vill skapa ett undernät och ett virtuellt nätverk lägger du till den här koden i huvud metoden:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Skapa nätverks gränssnittet

En virtuell dator behöver ett nätverks gränssnitt för att kommunicera med det virtuella nätverket.

Om du vill skapa ett nätverks gränssnitt lägger du till den här koden i huvud metoden:

```csharp
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Nu när du har skapat alla stöd resurser kan du skapa en virtuell dator.

Lägg till den här koden i huvud metoden för att skapa den virtuella datorn:

```csharp
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Den här självstudien skapar en virtuell dator som kör en version av operativ systemet Windows Server. Mer information om hur du väljer andra bilder finns i [navigera och välja avbildningar av virtuella Azure-datorer med Windows PowerShell och Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Använd den här koden om du vill använda en befintlig disk i stället för en Marketplace-avbildning:

```csharp
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Utföra hanterings uppgifter

Under livscykeln för en virtuell dator kan du vilja köra administrativa uppgifter, genom att till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa kod för att automatisera repetitiva eller komplexa uppgifter.

När du behöver göra något med den virtuella datorn måste du skaffa en instans av den:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Hämta information om den virtuella datorn

Om du vill hämta information om den virtuella datorn lägger du till den här koden i huvud metoden:

```csharp
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Stoppa den virtuella datorn

Du kan stoppa en virtuell dator och behålla alla inställningar, men fortsätta att debiteras för den, eller så kan du stoppa en virtuell dator och frigöra den. När en virtuell dator frigörs frigörs även alla resurser som är kopplade till den och faktureringen upphör.

Om du vill stoppa den virtuella datorn utan att ta bort tilldelningen, lägger du till den här koden i huvud metoden:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Om du vill frigöra den virtuella datorn ändrar du avstängnings läge-anropet till den här koden:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>Starta den virtuella datorn

För att starta den virtuella datorn lägger du till den här koden i huvud metoden:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Ändra storlek på den virtuella datorn

Många aspekter av distributionen bör övervägas när du bestämmer dig för en storlek på den virtuella datorn. Mer information finns i [VM-storlekar](sizes.md).  

Om du vill ändra storleken på den virtuella datorn lägger du till den här koden i huvud metoden:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Lägga till en datadisk i en virtuell dator

Lägg till den här koden i Main-metoden för att lägga till en datadisk till den virtuella datorn. Det här exemplet lägger till en datadisk som är 2 GB i storlek, han eller hon är en LUN på 0 och en typ av ReadWrite:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Ta bort resurser

Eftersom du debiteras för resurser som används i Azure är det alltid en bra idé att ta bort resurser som inte längre behövs. Om du vill ta bort de virtuella datorerna och alla stödda resurser måste du ta bort resurs gruppen.

Om du vill ta bort resurs gruppen lägger du till den här koden i huvud metoden:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Köra appen

Det bör ta ungefär fem minuter för konsol programmet att köras helt från början till slut. 

1. Kör konsol programmet genom att klicka på **Start**.

2. Innan du trycker på **RETUR** för att börja ta bort resurser kan det ta några minuter innan du verifierar att resurserna har skapats i Azure Portal. Klicka på distributions status om du vill se information om distributionen.

## <a name="next-steps"></a>Nästa steg
* Dra nytta av att använda en mall för att skapa en virtuell dator med hjälp av informationen i [distribuera en virtuell Azure-dator med C# och en Resource Manager-mall](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Lär dig mer om att använda [Azure-biblioteken för .net](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
