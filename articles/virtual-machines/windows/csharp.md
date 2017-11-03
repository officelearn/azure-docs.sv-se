---
title: "Skapa och hantera en virtuell Azure-dator med hjälp av C# | Microsoft Docs"
description: "Använd C# och Azure Resource Manager för att distribuera en virtuell dator och alla dess stödfiler resurser."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: 5d9021c2f65b70e36d5ea82992c9fb9d2d6d394a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Skapa och hantera virtuella Windows-datorer i Azure med C# #

En [Azure virtuella](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) måste flera stödjande Azure-resurser. Den här artikeln omfattar att skapa, hantera och ta bort VM-resurser med hjälp av C#. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Visual Studio-projekt
> * Installera paketet
> * Skapa autentiseringsuppgifter
> * Skapa resurser
> * Utföra administrativa uppgifter
> * Ta bort resurser
> * Köra programmet

Det tar ungefär 20 minuter för att utföra de här stegen.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Om du inte redan gjort installera [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Välj **.NET skrivbord development** på arbetsbelastningar sidan och klicka sedan på **installera**. Sammanfattningsvis, kan du se att **utvecklingsverktyg för .NET Framework 4 4.6** väljs automatiskt för dig. Om du redan har installerat Visual Studio kan du lägga till .NET arbetsbelastningen i Visual Studio-starta.
2. I Visual Studio klickar du på **filen** > **ny** > **projekt**.
3. I **mallar** > **Visual C#**väljer **Konsolapp (.NET Framework)**, ange *myDotnetProject* för namnet på projektet, välj platsen för projektet och klicka sedan på **OK**.

## <a name="install-the-package"></a>Installera paketet

NuGet-paket är det enklaste sättet att installera de bibliotek som du behöver för att slutföra de här stegen. För att få de bibliotek som du behöver i Visual Studio kan du utföra de här stegen:

1. Klicka på **verktyg** > **Nuget Package Manager**, och klicka sedan på **Pakethanterarkonsolen**.
2. Ange följande kommando i konsolen:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Innan du startar det här steget, se till att du har åtkomst till en [Active Directory-tjänstens huvudnamn](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Du bör anteckna det program-ID, autentiseringsnyckeln och klient-ID som du behöver i ett senare steg.

### <a name="create-the-authorization-file"></a>Skapa auktoriseringsfilen

1. I Solution Explorer högerklickar du på *myDotnetProject* > **Lägg till** > **nytt objekt**, och välj sedan **textfil** i *Visual C# objekt*. Namn på filen *azureauth.properties*, och klicka sedan på **Lägg till**.
2. Lägg till dessa auktorisering egenskaper:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Ersätt  **&lt;prenumerations-id&gt;**  med prenumerations-ID  **&lt;program-id&gt;**  med programidentifierare Active Directory  **&lt;autentiseringsnyckel&gt;**  med nyckeln för programmet och  **&lt;klient-id&gt;**  med klient-ID.

3. Spara filen azureauth.properties. 
4. Ange en miljövariabel i Windows som heter AZURE_AUTH_LOCATION med den fullständiga sökvägen till auktoriseringsfilen som du skapade. Till exempel kan följande PowerShell-kommando användas:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Skapa management-klienten

1. Öppna filen Program.cs för projektet som du skapade och Lägg sedan till följande using-instruktioner till befintliga instruktioner överst i filen:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Lägg till den här koden Main-metoden för att skapa management-klienten:

    ```
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

Alla resurser måste finnas i en [resursgruppen](../../azure-resource-manager/resource-group-overview.md).

Lägg till den här koden Main-metoden för att ange värden för programmet och skapa resursgruppen:

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Skapa tillgänglighetsuppsättningen

[Tillgänglighetsuppsättningar](tutorial-availability-sets.md) gör det enklare att underhålla de virtuella datorerna som används av ditt program.

Lägg till den här koden Main-metoden för att skapa tillgänglighetsuppsättningen:

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Skapa offentlig IP-adress

En [offentliga IP-adressen](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) behövs för att kommunicera med den virtuella datorn.

Lägg till den här koden Main-metoden för att skapa den offentliga IP-adressen för den virtuella datorn:
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Skapa virtuella nätverk

En virtuell dator måste vara i ett undernät för en [för virtuella nätverk](../../virtual-network/virtual-networks-overview.md).

Lägg till den här koden Main-metoden för att skapa ett undernät och ett virtuellt nätverk:

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Skapa nätverksgränssnittet

En virtuell dator måste ett nätverksgränssnitt för att kommunicera på det virtuella nätverket.

Lägg till den här koden Main-metoden för att skapa ett nätverksgränssnitt:

```
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

Lägg till den här koden Main-metoden för att skapa den virtuella datorn:

```
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
> Den här guiden skapar en virtuell dator som kör en version av operativsystemet Windows Server. Läs mer om att välja andra bilder i [analysera och välja avbildningar för virtuell Azure-dator med Windows PowerShell och Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Om du vill använda en befintlig disk i stället för en marketplace-avbildning, Använd den här koden:

```
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

## <a name="perform-management-tasks"></a>Utföra administrativa uppgifter

Under livscykeln för en virtuell dator kan du vill köra hanteringsuppgifter, till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa kod för att automatisera repetitiva och komplicerade uppgifter.

När du behöver göra något med den virtuella datorn måste du hämta en instans av den:

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Hämta information om den virtuella datorn

Lägg till den här koden Main-metoden för att få information om den virtuella datorn:

```
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

Du kan stoppa en virtuell dator och behålla alla inställningar, men fortsätter att debiteras för den eller stoppa en virtuell dator och frigör den. När en virtuell dator har frigjorts alla resurser som är associerade med den är också frigjord och fakturering avslutas för den.

Lägg till den här koden Main-metoden för att stoppa den virtuella datorn utan att det har frigjorts den:

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Om du vill ta bort den virtuella datorn ändra avstängningsläge anrop till den här koden:

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>Starta den virtuella datorn

Lägg till den här koden Main-metoden för att starta den virtuella datorn:

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Ändra storlek på den virtuella datorn

Många aspekter av distributionen bör övervägas när du funderar över en storlek för den virtuella datorn. Mer information finns i [VM-storlekar](sizes.md).  

Lägg till den här koden Main-metoden om du vill ändra storleken på den virtuella datorn:

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Lägg till en datadisk till den virtuella datorn

Lägg till den här koden Main-metoden för att lägga till en datadisk är 2 GB i storlek, han LUN 0 och en cachelagring typ av ReadWrite för att lägga till en datadisk till den virtuella datorn:

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Ta bort resurser

Eftersom du debiteras för de resurser som används i Azure, men det är alltid bra att ta bort resurser som inte längre behövs. Om du vill ta bort de virtuella datorerna och alla stödresurser är allt du behöver göra resursgruppen.

Lägg till den här koden Main-metoden för att ta bort resursgruppen:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Köra programmet

Det bör ta ungefär fem minuter för den här konsolen programmet helt från början till slut. 

1. Klicka på att köra konsolprogrammet **starta**.

2. Innan du trycker på **ange** om du vill börja ta bort resurser, du kan ta några minuter för att verifiera att skapa resurser i Azure-portalen. Klicka på Distributionsstatus för att visa information om hur du distribuerar.

## <a name="next-steps"></a>Nästa steg
* Dra nytta av att använda en mall för att skapa en virtuell dator med hjälp av informationen i [distribuera ett Azure-dator med hjälp av C# och Resource Manager-mall](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Lär dig mer om hur du använder den [Azure-bibliotek för .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).

