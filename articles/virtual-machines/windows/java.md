---
title: Skapa och hantera Azure-datorer med hjälp av Java | Microsoft Docs
description: Använd Java och Azure Resource Manager för att distribuera en virtuell dator och alla dess resurser.
services: virtual-machines-windows
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: zarhoads
ms.openlocfilehash: 4495b6813a3936967ceb81beb216850538b04af5
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465601"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Skapa och hantera Windows-datorer i Azure med Java

En [Azure-dator](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) måste flera stödjande Azure-resurser. Den här artikeln beskriver skapa, hantera och ta bort VM-resurser med hjälp av Java. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Maven-projekt
> * Lägga till beroenden
> * Skapa autentiseringsuppgifter
> * Skapa resurser
> * Utföra administrativa uppgifter
> * Ta bort resurser
> * Köra programmet

Det tar cirka 20 minuter för att utföra de här stegen.

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

1. Om du inte redan har gjort det installerar [Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
2. Installera [Maven](http://maven.apache.org/download.cgi).
3. Skapa en ny mapp och projektet:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Lägga till beroenden

1. Under den `testAzureApp` mappen öppnar den `pom.xml` filen och Lägg till versionskonfiguration till &lt;projekt&gt; att aktivera ditt program:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Lägg till de beroenden som behövs för att komma åt Azure Java SDK.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency> 
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Spara filen.

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Innan du startar det här steget, se till att du har åtkomst till en [Active Directory-tjänstobjekt](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Du bör anteckna program-ID och autentiseringsnyckel klient-ID som du behöver i ett senare steg.

### <a name="create-the-authorization-file"></a>Skapa auktoriseringsfilen

1. Skapa en fil med namnet `azureauth.properties` och Lägg till de här egenskaperna:

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

2. Spara filen.
3. Ange en miljövariabel som heter AZURE_AUTH_LOCATION i ditt gränssnitt med den fullständiga sökvägen till autentiseringsfilen.

### <a name="create-the-management-client"></a>Skapa management-klienten

1. Öppna den `App.java` filen under `src\main\java\com\fabrikam` och se till att det här paketet-instruktionen är högst upp:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. Under instruktionen paketet lägger du till dessa import-satserna:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Skapa Active Directory-autentiseringsuppgifter som du behöver göra begäranden genom att lägga till den här koden till main-metoden i klassen App:
   
    ```java
    try {    
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Skapa resurser

### <a name="create-the-resource-group"></a>Skapa en resursgrupp

Alla resurser måste finnas i en [resursgrupp](../../azure-resource-manager/resource-group-overview.md).

Lägg till den här koden try-block i main-metoden för att ange värden för programmet och skapa resursgruppen:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Skapa tillgänglighetsuppsättning

[Tillgänglighetsuppsättningar](tutorial-availability-sets.md) gör det enklare att underhålla de virtuella datorerna som används av ditt program.

Lägg till den här koden try-block i main-metoden för att skapa tillgänglighetsuppsättningen:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Skapa offentlig IP-adress

En [offentliga IP-adressen](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) krävs för att kommunicera med den virtuella datorn.

Lägg till den här koden try-block i main-metoden för att skapa den offentliga IP-adressen för den virtuella datorn:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

En virtuell dator måste vara i ett undernät för ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

Lägg till den här koden try-block i main-metoden för att skapa ett undernät och ett virtuellt nätverk:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>Skapa nätverksgränssnittet

En virtuell dator behöver ett nätverksgränssnitt ska kunna kommunicera på det virtuella nätverket.

Lägg till den här koden try-block i main-metoden för att skapa ett nätverksgränssnitt:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Nu när du har skapat alla stödresurser, kan du skapa en virtuell dator.

Lägg till den här koden try-block i main-metoden för att skapa den virtuella datorn:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> Den här självstudiekursen skapar en virtuell dator som kör en version av operativsystemet Windows Server. Läs mer om att välja andra bilder i [analysera och välja avbildningar av virtuella datorer med Windows PowerShell och Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Om du vill använda en befintlig disk i stället för en marketplace-avbildning, använder du den här koden: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk") 
    .withRegion(Region.US_EAST) 
    .withExistingResourceGroup("myResourceGroup") 
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd") 
    .withSizeInGB(128) 
    .withSku(DiskSkuTypes.PremiumLRS) 
    .create(); 

azure.virtualMachines.define("myVM") 
    .withRegion(Region.US_EAST) 
    .withExistingResourceGroup("myResourceGroup") 
    .withExistingPrimaryNetworkInterface(networkInterface) 
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows) 
    .withExistingAvailabilitySet(availabilitySet) 
    .withSize(VirtualMachineSizeTypes.StandardDS1) 
    .create(); 
``` 

## <a name="perform-management-tasks"></a>Utföra administrativa uppgifter

Under livscykeln för en virtuell dator kan du vilja köra administrativa uppgifter, genom att till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kan du skapa kod för att automatisera repetitiva eller komplicerade uppgifter.

När du behöver göra något med den virtuella datorn kan behöva du hämta en instans av den. Lägg till den här koden try-block av main-metoden:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Få information om den virtuella datorn

Lägg till den här koden try-block i main-metoden för att få information om den virtuella datorn:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();   
```

### <a name="stop-the-vm"></a>Stoppa den virtuella datorn

Du kan stoppa en virtuell dator och behålla alla dess inställningar, men fortsätter att debiteras för den eller stoppa en virtuell dator och frigör den. När en virtuell dator har frigjorts är alla resurser som är associerade med den också frigörs och faktureringssupport upphör för den.

Lägg till den här koden try-block i main-metoden för att stoppa den virtuella datorn utan att de frigörs den:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Om du vill frigöra den virtuella datorn, ändrar du avstängningsläge anropet till den här koden:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Starta den virtuella datorn

Lägg till den här koden try-block i main-metoden för att starta den virtuella datorn:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Ändra storlek på den virtuella datorn

Många aspekter av distributionen bör övervägas när du bestämmer dig en storlek för den virtuella datorn. Mer information finns i [VM-storlekar](sizes.md).  

Lägg till den här koden try-block i main-metoden för att ändra storleken på den virtuella datorn:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Lägga till en datadisk till den virtuella datorn

Om du vill lägga till en datadisk till den virtuella datorn som är 2 GB i storlek, har du ett LUN 0 och en typ av cachelagring av ReadWrite, lägga till den här koden i blocket try i main-metoden:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Ta bort resurser

Eftersom du debiteras för resurser som används i Azure, men det är alltid bra att ta bort resurser som inte längre behövs. Om du vill ta bort de virtuella datorerna och alla stödresurser är allt du behöver göra ta bort resursgruppen.

1. Lägg till den här koden try-block i main-metoden för att ta bort resursgruppen:
   
```java
System.out.println("Deleting resources...");
azure.resourceGroups().deleteByName("myResourceGroup");
```

2. Spara filen App.Java.

## <a name="run-the-application"></a>Köra programmet

Det bör ta ungefär fem minuter för den här konsolprogram för att köra helt från början till slut.

1. Använd följande Maven-kommando för att köra programmet:

    ```
    mvn compile exec:java
    ```

2. Innan du trycker på **RETUR** om du vill börja ta bort resurser, du kan ta några minuter för att verifiera att skapa resurser i Azure-portalen. Klicka på distributionsstatusen för att visa information om hur du distribuerar.


## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du använder den [Azure libraries för Java](https://docs.microsoft.com/java/azure/java-sdk-azure-overview).

