---
title: Självstudie – konfigurera Azure Route-tabeller med Ansible
description: Lär dig att skapa, ändra och ta bort Azure Route-tabeller med Ansible
keywords: ansible, azure, devops, bash, playbook, nätverk, väg, routningstabell
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 14753af58a179ddf4011cb29c7ed08faab62875c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241781"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Självstudie: Konfigurera Azure Route-tabeller med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du behöver mer kontroll över din miljös routning kan du [skapa en routningstabell](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Skapa en routningstabell skapa ett virtuellt nätverk och ett undernät associera en routningstabell med ett undernät ta bort koppling från en routningstabell från ett undernät skapa och ta bort vägar fråga en routningstabell ta bort en routningstabell

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Skapa en routningstabell

Spelbok-koden i det här avsnittet skapar en routningstabell. Information om begränsningar för väg tabeller finns i [Azure-gränser](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Spara följande spelbok som `route_table_create.yml`:

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

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Spelbok-koden i det här avsnittet:

* Skapar ett virtuellt nätverk
* Skapar ett undernät i det virtuella nätverket
* Kopplar en routningstabell till under nätet

Routningstabeller är inte kopplade till virtuella nätverk. Routningstabeller är i stället kopplade till under nätet för ett virtuellt nätverk.

Det virtuella nätverket och routningstabellen måste finnas på samma Azure-plats och i samma Azure-prenumeration.

Undernät och routningstabeller har en en-till-många-relation. Ett undernät kan definieras utan någon kopplad routningstabell eller en routningstabell. Routningstabeller kan associeras med ingen, en eller flera undernät. 

Trafik från under nätet dirigeras utifrån:

- vägar som definierats i routningstabeller
- [standard vägar](/azure/virtual-network/virtual-networks-udr-overview#default)
- vägar som sprids från ett lokalt nätverk

Det virtuella nätverket måste vara anslutet till en virtuell Azure-nätverksgateway. Gatewayen kan vara ExpressRoute eller VPN om du använder BGP med en VPN-gateway.

Spara följande spelbok som `route_table_associate.yml`:

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

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Avassociera en routningstabell från ett undernät

Spelbok-koden i det här avsnittet kopplar bort en routningstabell från ett undernät.

När du kopplar bort en routningstabell från ett undernät anger du `route_table` för under nätet till `None`. 

Spara följande spelbok som `route_table_dissociate.yml`:

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

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Skapa en väg

Spelbok-koden i det här avsnittet en väg i en routningstabell. 

Spara följande spelbok som `route_create.yml`:

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

Se följande anmärkningar innan du kör Spelbok:

* `virtual_network_gateway` definieras som `next_hop_type`. Mer information om hur Azure väljer vägar finns i [routning – översikt](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` definieras som `10.1.0.0/16`. Prefixet kan inte dupliceras i routningstabellen.

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Ta bort en väg

Spelbok-koden i det här avsnittet tar bort en väg från en routningstabell.

Spara följande spelbok som `route_delete.yml`:

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

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Hämta information om routningstabell

Spelbok-koden i det här avsnittet använder Ansible-modulen `azure_rm_routetable_facts` för att hämta information om routningstabellen.

Spara följande spelbok som `route_table_facts.yml`:

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

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Ta bort en routningstabell

Spelbok-koden i det här avsnittet en routningstabell.

När en routningstabell tas bort, tas även alla vägar bort.

Det går inte att ta bort en routningstabell om den är associerad med ett undernät. Koppla bort [routningstabellen från alla undernät](#dissociate-a-route-table-from-a-subnet) innan du försöker ta bort routningstabellen. 

Spara följande spelbok som `route_table_delete.yml`:

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

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)