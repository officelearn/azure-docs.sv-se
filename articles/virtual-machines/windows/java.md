---
title: Skapa och hantera en virtuell Azure-dator med Java
description: Använd Java och Azure Resource Manager för att distribuera en virtuell dator och alla dess stödresurser.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 86b9943a22e63e4e00b19632477e1ed5496b7d1c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451762"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Skapa och hantera Virtuella Windows-datorer i Azure med Java

En [virtuell Azure-dator](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) behöver flera stödda Azure-resurser. Den här artikeln handlar om att skapa, hantera och ta bort VM-resurser med Java. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Maven-projekt
> * Lägga till beroenden
> * Skapa autentiseringsuppgifter
> * Skapa resurser
> * Utföra hanteringsuppgifter
> * Ta bort resurser
> * Köra appen

Det tar ungefär 20 minuter att göra dessa steg.

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

1. Om du inte redan har gjort det installerar du [Java](https://aka.ms/azure-jdks).
2. Installera [Maven](https://maven.apache.org/download.cgi).
3. Skapa en ny mapp och projektet:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Lägga till beroenden

1. Öppna `testAzureApp` filen under `pom.xml` mappen och lägg till &lt;&gt; byggkonfigurationen i projektet så att programmet kan skapas:

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

Innan du startar det här steget kontrollerar du att du har åtkomst till ett huvudnamn för [Active Directory-tjänsten](../../active-directory/develop/howto-create-service-principal-portal.md). Du bör också registrera program-ID: et, autentiseringsnyckeln och klient-ID som du behöver i ett senare steg.

### <a name="create-the-authorization-file"></a>Skapa auktoriseringsfilen

1. Skapa en `azureauth.properties` fil med namnet och lägg till dessa egenskaper i den:

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

2. Spara filen.
3. Ange en miljövariabel med namnet AZURE_AUTH_LOCATION i skalet med den fullständiga sökvägen till autentiseringsfilen.

### <a name="create-the-management-client"></a>Skapa hanteringsklienten

1. Öppna `App.java` filen `src\main\java\com\fabrikam` under och se till att den här paketsatsen är överst:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. Lägg till dessa importsatser under paketutdraget:
   
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

2. Om du vill skapa de Active Directory-autentiseringsuppgifter som du behöver göra begäranden lägger du till den här koden i huvudmetoden för klassen App:
   
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

Alla resurser måste finnas i en [resursgrupp](../../azure-resource-manager/management/overview.md).

Om du vill ange värden för programmet och skapa resursgruppen lägger du till den här koden i försöksblocket i huvudmetoden:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Skapa tillgänglighetsuppsättningen

[Tillgänglighetsuppsättningar](tutorial-availability-sets.md) gör det enklare för dig att underhålla de virtuella datorer som används av ditt program.

Om du vill skapa tillgänglighetsuppsättningen lägger du till den här koden i försöksblocket i huvudmetoden:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Skapa den offentliga IP-adressen

En [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) behövs för att kommunicera med den virtuella datorn.

Om du vill skapa den offentliga IP-adressen för den virtuella datorn lägger du till den här koden i försöksblocket i huvudmetoden:

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

En virtuell dator måste finnas i ett undernät i ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

Om du vill skapa ett undernät och ett virtuellt nätverk lägger du till den här koden i försöksblocket i huvudmetoden:

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

En virtuell dator behöver ett nätverksgränssnitt för att kommunicera i det virtuella nätverket.

Om du vill skapa ett nätverksgränssnitt lägger du till den här koden i försöksblocket i huvudmetoden:

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

Nu när du har skapat alla stödresurser kan du skapa en virtuell dator.

Om du vill skapa den virtuella datorn lägger du till den här koden i försöksblocket i huvudmetoden:

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
> Den här självstudien skapar en virtuell dator som kör en version av Operativsystemet Windows Server. Mer information om hur du väljer andra avbildningar finns i [Navigera och välja Azure-avbildningar med virtuella datorer med Windows PowerShell och Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Om du vill använda en befintlig disk i stället för en marketplace-avbildning använder du den här koden: 

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

## <a name="perform-management-tasks"></a>Utföra hanteringsuppgifter

Under livscykeln för en virtuell dator kan du vilja köra administrativa uppgifter, genom att till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa kod för att automatisera repetitiva eller komplexa uppgifter.

När du behöver göra något med den virtuella datorn måste du få en instans av den. Lägg till den här koden i försöksblocket för huvudmetoden:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Få information om den virtuella datorn

Om du vill ha information om den virtuella datorn lägger du till den här koden i försöksblocket i huvudmetoden:

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

Du kan stoppa en virtuell dator och behålla alla dess inställningar, men fortsätta att debiteras för den, eller så kan du stoppa en virtuell dator och frigöra den. När en virtuell dator är deallocated, alla resurser som är associerade med den är också deallocated och fakturering slutar för det.

Om du vill stoppa den virtuella datorn utan att frigöra den lägger du till den här koden i försöksblocket i huvudmetoden:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Om du vill frigöra den virtuella datorn ändrar du PowerOff-anropet till den här koden:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Starta den virtuella datorn

Om du vill starta den virtuella datorn lägger du till den här koden i försöksblocket i huvudmetoden:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Ändra storlek på den virtuella datorn

Många aspekter av distributionen bör beaktas när man beslutar om en storlek för din virtuella dator. Mer information finns i [VM-storlekar](sizes.md).  

Om du vill ändra storleken på den virtuella datorn lägger du till den här koden i försöksblocket i huvudmetoden:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Lägga till en datadisk i en virtuell dator

Om du vill lägga till en datadisk till den virtuella datorn som har 2 GB i storlek, har en LUN på 0 och en cachelagringstyp readwrite lägger du till den här koden i försöksblocket i huvudmetoden:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Ta bort resurser

Eftersom du debiteras för resurser som används i Azure är det alltid bra att ta bort resurser som inte längre behövs. Om du vill ta bort de virtuella datorerna och alla stödresurser behöver du bara ta bort resursgruppen.

1. Om du vill ta bort resursgruppen lägger du till den här koden i försöksblocket i huvudmetoden:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Spara App.java-filen.

## <a name="run-the-application"></a>Köra appen

Det bör ta ungefär fem minuter för den här konsolen programmet att köras helt från början till.

1. Om du vill köra programmet använder du det här Maven-kommandot:

    ```
    mvn compile exec:java
    ```

2. Innan du trycker på **Retur** för att börja ta bort resurser kan det ta några minuter att verifiera att resurserna skapas i Azure-portalen. Klicka på distributionsstatusen om du vill se information om distributionen.


## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du använder [Azure-biblioteken för Java](https://docs.microsoft.com/java/azure/java-sdk-azure-overview).

