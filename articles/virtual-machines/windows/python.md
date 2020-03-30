---
title: Skapa och hantera en Windows-virtuell dator i Azure med Python
description: Lär dig att använda Python för att skapa och hantera en Windows VM i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: a85a9c28acd2d50d95159883a01b27c8ed1d2f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461089"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Skapa och hantera Virtuella Windows-datorer i Azure med Python

En [virtuell Azure-dator](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) behöver flera stödda Azure-resurser. Den här artikeln handlar om att skapa, hantera och ta bort VM-resurser med Python. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Visual Studio-projekt
> * Installera paket
> * Skapa autentiseringsuppgifter
> * Skapa resurser
> * Utföra hanteringsuppgifter
> * Ta bort resurser
> * Köra appen

Det tar ungefär 20 minuter att göra dessa steg.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Om du inte redan har gjort det installerar du [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Välj **Python-utveckling** på sidan Arbetsbelastningar och klicka sedan på **Installera**. I sammanfattningen kan du se att **Python 3 64-bitars (3.6.0)** väljs automatiskt åt dig. Om du redan har installerat Visual Studio kan du lägga till Python-arbetsbelastningen med Visual Studio Launcher.
2. När du har installerat och startat Visual Studio klickar du på **Arkiv** > **nytt** > **projekt**.
3. Klicka på **Mallar** > **Python** > **Python Application**, ange *myPythonProject* för namnet på projektet, välj platsen för projektet och klicka sedan på **OK**.

## <a name="install-packages"></a>Installera paket

1. Högerklicka på **Python-miljöer**under *minPythonProject*i Solution Explorer och välj sedan **Lägg till virtuell miljö**.
2. På skärmen Lägg till virtuell miljö godkänner du standardnamnet *på env*, kontrollerar att *Python 3.6 (64-bitars)* är markerat för bastolken och klickar sedan på **Skapa**.
3. Högerklicka på den *miljö med env* som du skapade, klicka på **Installera Python-paket,** ange *azure* i sökrutan och tryck sedan på Retur.

Du bör se i utdatafönstren att azure-paketen har installerats. 

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Innan du startar det här steget kontrollerar du att du har ett [huvudnamn för Active Directory-tjänsten](../../active-directory/develop/howto-create-service-principal-portal.md). Du bör också registrera program-ID: et, autentiseringsnyckeln och klient-ID som du behöver i ett senare steg.

1. Öppna *myPythonProject.py* fil som har skapats och lägg sedan till den här koden så att programmet kan köras:

    ```python
    if __name__ == "__main__":
    ```

2. Om du vill importera koden som behövs lägger du till dessa satser överst i PY-filen:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Nästa i .py-filen lägger du till variabler efter importsatserna för att ange vanliga värden som används i koden:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Ersätt **prenumerations-ID** med din prenumerationsidentifierare.

4. Om du vill skapa de Active Directory-autentiseringsuppgifter som du behöver göra begäranden lägger du till den här funktionen efter variablerna i .py-filen:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Ersätt **program-ID,** **autentiseringsnyckel**och **klient-ID** med de värden som du tidigare samlat in när du skapade ditt Azure Active Directory-tjänsthuvudnamn.

5. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Skapa resurser
 
### <a name="initialize-management-clients"></a>Initiera hanteringsklienter

Hanteringsklienter behövs för att skapa och hantera resurser med Python SDK i Azure. Om du vill skapa hanteringsklienter lägger du till den här koden under **IF-satsen** i slutet av PY-filen:

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Skapa den virtuella datorn och stödresurser

Alla resurser måste finnas i en [resursgrupp](../../azure-resource-manager/management/overview.md).

1. Om du vill skapa en resursgrupp lägger du till den här funktionen efter variablerna i .py-filen:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Tillgänglighetsuppsättningar](tutorial-availability-sets.md) gör det enklare för dig att underhålla de virtuella datorer som används av ditt program.

1. Om du vill skapa en tillgänglighetsuppsättning lägger du till den här funktionen efter variablerna i .py-filen:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

En [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) behövs för att kommunicera med den virtuella datorn.

1. Om du vill skapa en offentlig IP-adress för den virtuella datorn lägger du till den här funktionen efter variablerna i .py-filen:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

En virtuell dator måste finnas i ett undernät i ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

1. Om du vill skapa ett virtuellt nätverk lägger du till den här funktionen efter variablerna i .py-filen:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Om du vill lägga till ett undernät i det virtuella nätverket lägger du till den här funktionen efter variablerna i .py-filen:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

En virtuell dator behöver ett nätverksgränssnitt för att kommunicera i det virtuella nätverket.

1. Om du vill skapa ett nätverksgränssnitt lägger du till den här funktionen efter variablerna i .py-filen:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Nu när du har skapat alla stödresurser kan du skapa en virtuell dator.

1. Om du vill skapa den virtuella datorn lägger du till den här funktionen efter variablerna i .py-filen:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Den här självstudien skapar en virtuell dator som kör en version av Operativsystemet Windows Server. Mer information om hur du väljer andra avbildningar finns i [Navigera och välja Azure-avbildningar med virtuella datorer med Windows PowerShell och Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Utföra hanteringsuppgifter

Under livscykeln för en virtuell dator kan du vilja köra administrativa uppgifter, genom att till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa kod för att automatisera repetitiva eller komplexa uppgifter.

### <a name="get-information-about-the-vm"></a>Få information om den virtuella datorn

1. Om du vill ha information om den virtuella datorn lägger du till den här funktionen efter variablerna i .py-filen:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Stoppa den virtuella datorn

Du kan stoppa en virtuell dator och behålla alla dess inställningar, men fortsätta att debiteras för den, eller så kan du stoppa en virtuell dator och frigöra den. När en virtuell dator är deallocated, alla resurser som är associerade med den är också deallocated och fakturering slutar för det.

1. Om du vill stoppa den virtuella datorn utan att frigöra den lägger du till den här funktionen efter variablerna i .py-filen:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Om du vill frigöra den virtuella datorn ändrar du power_off anropet till den här koden:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Starta den virtuella datorn

1. Om du vill starta den virtuella datorn lägger du till den här funktionen efter variablerna i .py-filen:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Ändra storlek på den virtuella datorn

Många aspekter av distributionen bör beaktas när man beslutar om en storlek för din virtuella dator. Mer information finns i [VM-storlekar](sizes.md).

1. Om du vill ändra storleken på den virtuella datorn lägger du till den här funktionen efter variablerna i .py-filen:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Lägga till en datadisk i en virtuell dator

Virtuella datorer kan ha en eller flera [datadiskar](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) som lagras som virtuella hårddiskar.

1. Om du vill lägga till en datadisk på den virtuella datorn lägger du till den här funktionen efter variablerna i .py-filen: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Ta bort resurser

Eftersom du debiteras för resurser som används i Azure är det alltid en bra idé att ta bort resurser som inte längre behövs. Om du vill ta bort de virtuella datorerna och alla stödresurser behöver du bara ta bort resursgruppen.

1. Om du vill ta bort resursgruppen och alla resurser lägger du till den här funktionen efter variablerna i .py-filen:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Om du vill anropa funktionen som du tidigare har lagt till lägger du till den här koden under **IF-satsen** i slutet av .py-filen:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Spara *myPythonProject.py*.

## <a name="run-the-application"></a>Köra appen

1. Om du vill köra konsolprogrammet klickar du på **Start** i Visual Studio.

2. Tryck på **Retur** när statusen för varje resurs har returnerats. I statusinformationen bör du se ett **efterföljande etableringstillstånd.** När den virtuella datorn har skapats har du möjlighet att ta bort alla resurser som du skapar. Innan du trycker på **Retur** för att börja ta bort resurser kan det ta några minuter innan du verifierar att de skapas i Azure-portalen. Om du har Azure-portalen öppen kan du behöva uppdatera bladet för att se nya resurser.  

    Det bör ta ungefär fem minuter för den här konsolen programmet att köras helt från början till. Det kan ta flera minuter efter att programmet har slutförts innan alla resurser och resursgruppen tas bort.


## <a name="next-steps"></a>Nästa steg

- Om det var problem med distributionen, är nästa steg att titta på [Felsöka resursgruppdistribueringar med Azure-portalen](../../resource-manager-troubleshoot-deployments-portal.md)
- Läs mer om [Azure Python-biblioteket](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

