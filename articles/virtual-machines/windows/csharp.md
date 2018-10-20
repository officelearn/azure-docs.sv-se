---
title: Skapa och hantera Azure-datorer med C# | Microsoft Docs
description: Använda C# och Azure Resource Manager för att distribuera en virtuell dator och alla dess resurser.
services: virtual-machines-windows
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: zarhoads
ms.openlocfilehash: 7281b2bfc8137e4f60c3309c9a57ccabf0fdb4cb
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471432"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Skapa och hantera Windows virtuella datorer i Azure med C# #

En [Azure-dator](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) måste flera stödjande Azure-resurser. Den här artikeln beskriver skapa, hantera och ta bort VM-resurser med C#. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Visual Studio-projekt
> * Installera paketet
> * Skapa autentiseringsuppgifter
> * Skapa resurser
> * Utföra administrativa uppgifter
> * Ta bort resurser
> * Köra programmet

Det tar cirka 20 minuter för att utföra de här stegen.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Om du inte redan gjort installera [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Välj **.NET-skrivbordsutveckling** på arbetsbelastningar sidan och klicka sedan på **installera**. Sammanfattningsvis ska du se att **utvecklingsverktyg för .NET Framework 4 4.6** väljs automatiskt åt dig. Om du redan har installerat Visual Studio kan du lägga till .NET-arbetsbelastningen i Visual Studio-starta.
2. I Visual Studio klickar du på **Arkiv** > **Nytt** > **Projekt**.
3. I **mallar** > **Visual C#** väljer **Konsolapp (.NET Framework)**, ange *myDotnetProject* för namnet på den projektet, välj platsen för projektet och klicka sedan på **OK**.

## <a name="install-the-package"></a>Installera paketet

NuGet-paket är det enklaste sättet att installera de bibliotek som du måste slutföra de här stegen. För att få de bibliotek som du behöver i Visual Studio, gör du följande:

1. Klicka på **verktyg** > **Nuget-Pakethanteraren**, och klicka sedan på **Pakethanterarkonsolen**.
2. Ange följande kommando i konsolen:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Innan du startar det här steget, se till att du har åtkomst till en [Active Directory-tjänstobjekt](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Du bör anteckna program-ID och autentiseringsnyckel klient-ID som du behöver i ett senare steg.

### <a name="create-the-authorization-file"></a>Skapa auktoriseringsfilen

1. I Solution Explorer högerklickar du på *myDotnetProject* > **Lägg till** > **nytt objekt**, och välj sedan **textfil** i *Visual C#-objekt*. Ge filen namnet *azureauth.properties*, och klicka sedan på **Lägg till**.
2. Lägg till de här egenskaperna för auktorisering:

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

    Ersätt **&lt;prenumerations-id&gt;** med ditt prenumerations-ID **&lt;program-id&gt;** med Active Directory-program identifierare **&lt;autentiseringsnyckeln&gt;** med programnyckel och **&lt;klient-id&gt;** med klient-ID.

3. Spara filen azureauth.properties. 
4. Ange en miljövariabel i Windows med namnet AZURE_AUTH_LOCATION med den fullständiga sökvägen till auktoriseringsfilen som du skapade. Till exempel kan följande PowerShell-kommando användas:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Skapa management-klienten

1. Öppna filen Program.cs för projektet som du skapade och sedan lägga till dessa using-satser till de befintliga-instruktionerna överst i filen:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Lägg till den här kod till Main-metoden för att skapa management-klienten:

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

Alla resurser måste finnas i en [resursgrupp](../../azure-resource-manager/resource-group-overview.md).

Lägg till den här kod till Main-metoden för att ange värden för programmet och skapa resursgruppen:

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Skapa tillgänglighetsuppsättning

[Tillgänglighetsuppsättningar](tutorial-availability-sets.md) gör det enklare att underhålla de virtuella datorerna som används av ditt program.

Lägg till den här koden i Main-metoden för att skapa tillgänglighetsuppsättningen:

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Skapa offentlig IP-adress

En [offentliga IP-adressen](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) krävs för att kommunicera med den virtuella datorn.

Lägg till den här koden i Main-metoden för att skapa den offentliga IP-adressen för den virtuella datorn:
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

En virtuell dator måste vara i ett undernät för ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

Lägg till den här koden i Main-metoden för att skapa ett undernät och ett virtuellt nätverk:

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

En virtuell dator behöver ett nätverksgränssnitt ska kunna kommunicera på det virtuella nätverket.

Lägg till den här koden i Main-metoden för att skapa ett nätverksgränssnitt:

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

Nu när du har skapat alla stödresurser, kan du skapa en virtuell dator.

Lägg till den här koden i Main-metoden för att skapa den virtuella datorn:

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
> Den här självstudiekursen skapar en virtuell dator som kör en version av operativsystemet Windows Server. Läs mer om att välja andra bilder i [analysera och välja avbildningar av virtuella datorer med Windows PowerShell och Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Om du vill använda en befintlig disk i stället för en marketplace-avbildning, använder du den här koden:

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

Under livscykeln för en virtuell dator kan du vilja köra administrativa uppgifter, genom att till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kan du skapa kod för att automatisera repetitiva eller komplicerade uppgifter.

När du behöver göra något med den virtuella datorn, måste du hämta en instans av den:

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Få information om den virtuella datorn

Lägg till den här koden i Main-metoden för att få information om den virtuella datorn:

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

Du kan stoppa en virtuell dator och behålla alla dess inställningar, men fortsätter att debiteras för den eller stoppa en virtuell dator och frigör den. När en virtuell dator har frigjorts är alla resurser som är associerade med den också frigörs och faktureringssupport upphör för den.

Lägg till den här koden i Main-metoden för att stoppa den virtuella datorn utan att de frigörs den:

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Om du vill frigöra den virtuella datorn, ändrar du avstängningsläge anropet till den här koden:

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>Starta den virtuella datorn

Lägg till den här koden i Main-metoden för att starta den virtuella datorn:

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Ändra storlek på den virtuella datorn

Många aspekter av distributionen bör övervägas när du bestämmer dig en storlek för den virtuella datorn. Mer information finns i [VM-storlekar](sizes.md).  

Lägg till den här koden i Main-metoden om du vill ändra storleken på den virtuella datorn:

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Lägga till en datadisk till den virtuella datorn

Lägg till den här kod till Main-metoden för att lägga till en datadisk som är 2 GB i storlek, han en LUN 0 och en typ av cachelagring av ReadWrite för att lägga till en datadisk till den virtuella datorn:

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Ta bort resurser

Eftersom du debiteras för resurser som används i Azure, men det är alltid bra att ta bort resurser som inte längre behövs. Om du vill ta bort de virtuella datorerna och alla stödresurser är allt du behöver göra ta bort resursgruppen.

Ta bort resursgruppen genom att lägga till den här koden till Main-metoden:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Köra programmet

Det bör ta ungefär fem minuter för den här konsolprogram för att köra helt från början till slut. 

1. För att köra konsolprogrammet, klickar du på **starta**.

2. Innan du trycker på **RETUR** om du vill börja ta bort resurser, du kan ta några minuter för att verifiera att skapa resurser i Azure-portalen. Klicka på distributionsstatusen för att visa information om hur du distribuerar.

## <a name="next-steps"></a>Nästa steg
* Dra nytta av att använda en mall för att skapa en virtuell dator med hjälp av informationen i [distribuera en Azure-dator med C# och Resource Manager-mall](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Läs mer om hur du använder den [Azure libraries för .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).

