---
title: 'Skapa och hantera en virtuell Azure-dator med C #'
description: Använd C# och Azure Resource Manager för att distribuera en virtuell dator och alla dess stödresurser.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 07c66b2955f3df1ffae1a0cb0c2b0888bdc790e9
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082891"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Skapa och hantera virtuella Windows-datorer i Azure med C # #

En [virtuell Azure-dator](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) behöver flera stödda Azure-resurser. Den här artikeln handlar om att skapa, hantera och ta bort VM-resurser med C#. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Visual Studio-projekt
> * Installera paketet
> * Skapa autentiseringsuppgifter
> * Skapa resurser
> * Utföra hanteringsuppgifter
> * Ta bort resurser
> * Köra appen

Det tar ungefär 20 minuter att göra dessa steg.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Om du inte redan har gjort det installerar du [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Välj **.NET-skrivbordsutveckling** på sidan Arbetsbelastningar och klicka sedan på **Installera**. I sammanfattningen kan du se att **.NET Framework 4 - 4.6 utvecklingsverktyg** väljs automatiskt åt dig. Om du redan har installerat Visual Studio kan du lägga till .NET-arbetsbelastningen med Visual Studio Launcher.
2. Klicka på **Arkiv** > **nytt** > **projekt**i Visual Studio.
3. I **Mallar** > **Visual C#** väljer du Console App **(.NET Framework),** anger *myDotnetProject* för namnet på projektet, väljer platsen för projektet och klickar sedan på **OK**.

## <a name="install-the-package"></a>Installera paketet

NuGet-paket är det enklaste sättet att installera biblioteken som du behöver för att slutföra dessa steg. Så här hämtar du de bibliotek som du behöver i Visual Studio:

1. Klicka på **Verktyg** > **Nuget Package Manager**och sedan på Package Manager **Console**.
2. Skriv det här kommandot i konsolen:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Innan du startar det här steget kontrollerar du att du har åtkomst till ett huvudnamn för [Active Directory-tjänsten](../../active-directory/develop/howto-create-service-principal-portal.md). Du bör också registrera program-ID: et, autentiseringsnyckeln och klient-ID som du behöver i ett senare steg.

### <a name="create-the-authorization-file"></a>Skapa auktoriseringsfilen

1. Högerklicka på *myDotnetProject* > **Add** > **New Item**i Solution Explorer och välj sedan **Textfil** i *Visuella C#-objekt*. Ge filen namnet *azureauth.properties*och klicka sedan på **Lägg till**.
2. Lägg till dessa auktoriseringsegenskaper:

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

    Ersätt ** &lt;&gt; prenumerations-ID** med din prenumerationsidentifierare, ** &lt;&gt; program-ID** med Active Directory-programidentifieraren, ** &lt;autentiseringsnyckeln&gt; ** med programnyckeln och ** &lt;klient-ID&gt; ** med klientidentifieraren.

3. Spara filen azureauth.properties. 
4. Ange en miljövariabel i Windows med namnet AZURE_AUTH_LOCATION med den fullständiga sökvägen till auktoriseringsfilen som du skapade. Följande PowerShell-kommando kan till exempel användas:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Skapa hanteringsklienten

1. Öppna Program.cs filen för projektet som du skapade. Lägg sedan till dessa med hjälp av satser till befintliga satser överst i filen:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Om du vill skapa hanteringsklienten lägger du till den här koden i huvudmetoden:

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

Alla resurser måste finnas i en [resursgrupp](../../azure-resource-manager/management/overview.md).

Om du vill ange värden för programmet och skapa resursgruppen lägger du till den här koden i huvudmetoden:

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Skapa tillgänglighetsuppsättningen

[Tillgänglighetsuppsättningar](tutorial-availability-sets.md) gör det enklare för dig att underhålla de virtuella datorer som används av ditt program.

Om du vill skapa tillgänglighetsuppsättningen lägger du till den här koden i huvudmetoden:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Skapa den offentliga IP-adressen

En [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) behövs för att kommunicera med den virtuella datorn.

Om du vill skapa den offentliga IP-adressen för den virtuella datorn lägger du till den här koden i huvudmetoden:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

En virtuell dator måste finnas i ett undernät i ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

Om du vill skapa ett undernät och ett virtuellt nätverk lägger du till den här koden i huvudmetoden:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Skapa nätverksgränssnittet

En virtuell dator behöver ett nätverksgränssnitt för att kommunicera i det virtuella nätverket.

Om du vill skapa ett nätverksgränssnitt lägger du till den här koden i huvudmetoden:

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

Nu när du har skapat alla stödresurser kan du skapa en virtuell dator.

Om du vill skapa den virtuella datorn lägger du till den här koden i huvudmetoden:

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
> Den här självstudien skapar en virtuell dator som kör en version av Operativsystemet Windows Server. Mer information om hur du väljer andra avbildningar finns i [Navigera och välja Azure-avbildningar med virtuella datorer med Windows PowerShell och Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Om du vill använda en befintlig disk i stället för en marketplace-avbildning använder du den här koden:

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

## <a name="perform-management-tasks"></a>Utföra hanteringsuppgifter

Under livscykeln för en virtuell dator kan du vilja köra administrativa uppgifter, genom att till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa kod för att automatisera repetitiva eller komplexa uppgifter.

När du behöver göra något med den virtuella datorn måste du få en instans av den:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Få information om den virtuella datorn

Om du vill ha information om den virtuella datorn lägger du till den här koden i huvudmetoden:

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

Du kan stoppa en virtuell dator och behålla alla dess inställningar, men fortsätta att debiteras för den, eller så kan du stoppa en virtuell dator och frigöra den. När en virtuell dator är deallocated, alla resurser som är associerade med den är också deallocated och fakturering slutar för det.

Om du vill stoppa den virtuella datorn utan att frigöra den lägger du till den här koden i huvudmetoden:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Om du vill frigöra den virtuella datorn ändrar du PowerOff-anropet till den här koden:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>Starta den virtuella datorn

Om du vill starta den virtuella datorn lägger du till den här koden i huvudmetoden:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Ändra storlek på den virtuella datorn

Många aspekter av distributionen bör beaktas när man beslutar om en storlek för din virtuella dator. Mer information finns i [VM-storlekar](sizes.md).  

Om du vill ändra storleken på den virtuella datorn lägger du till den här koden i huvudmetoden:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Lägga till en datadisk i en virtuell dator

Om du vill lägga till en datadisk på den virtuella datorn lägger du till den här koden i huvudmetoden. I det här exemplet läggs en datadisk som är 2 GB till, han a LUN på 0 och en cachelagringstyp av ReadWrite:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Ta bort resurser

Eftersom du debiteras för resurser som används i Azure är det alltid bra att ta bort resurser som inte längre behövs. Om du vill ta bort de virtuella datorerna och alla stödresurser behöver du bara ta bort resursgruppen.

Om du vill ta bort resursgruppen lägger du till den här koden i huvudmetoden:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Köra appen

Det bör ta ungefär fem minuter för den här konsolen programmet att köras helt från början till. 

1. Om du vill köra konsolprogrammet klickar du på **Start**.

2. Innan du trycker på **Retur** för att börja ta bort resurser kan det ta några minuter att verifiera att resurserna skapas i Azure-portalen. Klicka på distributionsstatusen om du vill se information om distributionen.

## <a name="next-steps"></a>Nästa steg
* Dra nytta av att använda en mall för att skapa en virtuell dator med hjälp av informationen i [Distribuera en virtuell Azure-dator med C# och en Resource Manager-mall](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Läs mer om hur du använder [Azure-biblioteken för .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
