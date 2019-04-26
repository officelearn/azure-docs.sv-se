---
title: Skapa, ändra eller ta bort en Azure-routningstabell med Ansible
description: Lär dig hur du använder Ansible för att skapa, ändra eller ta bort en routningstabell med Ansible
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, nätverk, väg, routningstabell
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 025a8182d32a7d0d00a48795c848d356eb1c3d4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60396826"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Skapa, ändra eller ta bort en Azure-routningstabell med Ansible
Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du vill ändra någon standardroutning i Azure kan du göra det genom att skapa en [routningstabell](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Den här artikeln visar hur du skapar, ändrar eller tar bort Azure routningstabeller och även hur du kopplar routningstabeller till undernät. 

## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 krävs om du vill köra följande exempelspelböcker i den här självstudien.

## <a name="create-a-route-table"></a>Skapa en routningstabell
I det här avsnittet visas en Ansible-exempelspelbok som skapar en routningstabell. Det finns en gräns för hur många routningstabeller du kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Spara den här spelboken som `route_table_create.yml`. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät
Ett undernät kan ha ingen eller en associerad routningstabell. En routningstabell kan kopplas till inget eller flera undernät. Eftersom routningstabeller inte är kopplade till virtuella nätverk måste du associera en routningstabell till varje undernät som du vill associera routningstabellen till. All trafik som lämnar undernätet dirigeras enligt de vägar som du har skapat i routningstabeller, [standardvägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default) och vägar som spridits från ett lokalt nätverk, om det virtuella nätverket är anslutet till en virtuell Azure-nätverksgateway (ExpressRoute, eller VPN, om du använder BGP med en VPN-gateway). Du kan bara associera en routningstabell till undernät i virtuella nätverk som finns på samma Azure-plats och i samma prenumeration som routningstabellen.

I det här avsnittet presenteras en Ansible-spelbok som skapar ett virtuellt nätverk och ett undernät och sedan kopplar en routningstabell till undernätet.

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Spara den här spelboken som `route_table_associate.yml`. Med kommandot **ansible-playbook** kör du Ansible-spelboken enligt följande:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Avassociera en routningstabell från ett undernät
När du kopplar du bort en routningstabell från ett undernät behöver du bara konfigurera `route_table` i ett undernät till `None`. Ett Ansible-spelboksexempel följer nedan. 

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Spara den här spelboken som `route_table_dissociate.yml`. Med kommandot **ansible-playbook** kör du Ansible-spelboken enligt följande:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Skapa en väg
I det här avsnittet visas en Ansible-exempelspelbok som skapar en väg under routningstabellen. Den definierar `virtual_network_gateway` som `next_hop_type` och `10.1.0.0/16` som `address_prefix`. Prefixet kan inte dupliceras i mer än en väg i routningstabellen, även om prefixet som kan finnas i ett annat prefix. Mer information om hur Azure väljer vägar och en detaljerad beskrivning av alla nästa hopptyper finns i [Routningsöversikt](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```
Spara den här spelboken som `route_create.yml`. Med kommandot **ansible-playbook** kör du Ansible-spelboken enligt följande:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Ta bort en väg
I det här avsnittet visas en Ansible-exempelspelbok som tar bort en väg från en routningstabell.

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Spara den här spelboken som `route_delete.yml`. Med kommandot **ansible-playbook** kör du Ansible-spelboken enligt följande:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>Skaffa information om en routningstabell
Du kan visa information om en route_table genom Ansible-modulen med namnet `azure_rm_routetable_facts`. Faktamodulen returnerar information om routningstabellen med alla de vägar som är kopplade till den.
Ett Ansible-spelboksexempel följer nedan. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Spara den här spelboken som `route_table_facts.yml`. Med kommandot **ansible-playbook** kör du Ansible-spelboken enligt följande:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Ta bort en routningstabell
Om en routingtabell är kopplad till undernät kan den inte tas bort. [Koppla bort](#dissociate-a-route-table-from-a-subnet) en routningstabell från alla undernät innan du försöker ta bort den.

Du kan ta bort routningstabellen tillsammans med alla vägar. Ett Ansible-spelboksexempel följer nedan. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Spara den här spelboken som `route_table_delete.yml`. Med kommandot **ansible-playbook** kör du Ansible-spelboken enligt följande:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible i Azure](https://docs.microsoft.com/azure/ansible/)
