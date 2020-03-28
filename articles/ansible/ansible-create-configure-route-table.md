---
title: Självstudiekurs - Konfigurera Azure-vägtabeller med Ansible
description: Lär dig hur du skapar, hanterar och tar bort Azure-vägtabeller med Ansible. Lär dig också hur du skapar och tar bort rutter.
keywords: ansible, azure, devops, bash, playbook, nätverk, väg, routningstabell
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659805"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Självstudiekurs: Konfigurera Azure-vägtabeller med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du behöver mer kontroll över miljöns routning kan du skapa en [flödestabell](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Skapa en flödestabell Skapa ett virtuellt nätverk och undernät Associera en flödestabell med ett undernät Ta bort en flödestabell från ett undernät Skapa och ta bort vägar Fråga en vägtabell Ta bort en vägtabell

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Skapa en routningstabell

Spelbokskoden i det här avsnittet skapar en rutttabell. Information om rutttabellsbegränsningar finns i [Azure-gränser](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Spelbokskoden i det här avsnittet:

* Skapar ett virtuellt nätverk
* Skapar ett undernät i det virtuella nätverket
* Associerar en flödestabell till undernätet

Flödestabeller är inte associerade till virtuella nätverk. I stället associeras vägtabeller med undernätet i ett virtuellt nätverk.

Den virtuella nätverks- och vägtabellen måste samexistera på samma Azure-plats och prenumeration.

Undernät och flödestabeller har en 1:1:1-relation. Ett undernät kan definieras utan associerad flödestabell eller en flödestabell. Flödestabeller kan associeras med inga, ett eller många undernät. 

Trafiken från undernätet dirigeras baserat på:

- rutter som definierats inom flödestabeller
- [standardvägar](/azure/virtual-network/virtual-networks-udr-overview#default)
- rutter som sprids från ett lokalt nätverk

Det virtuella nätverket måste vara anslutet till en azure-virtuell nätverksgateway. Gatewayen kan vara ExpressRoute eller VPN om du använder BGP med en VPN-gateway.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Avassociera en routningstabell från ett undernät

Spelbokskoden i det här avsnittet tar bort en vägtabell från ett undernät.

När du tar bort en flödestabell från `route_table` ett undernät `None`anger du för undernätet till . 

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Skapa en väg

Spelbokskoden i det här avsnittet en väg inom en rutttabell. 

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

Innan du kör spelboken läser du följande anmärkningar:

* `virtual_network_gateway`definieras som `next_hop_type`. Mer information om hur Azure väljer vägar finns i [Översikt över Routning](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix`definieras som `10.1.0.0/16`. Prefixet kan inte dupliceras i flödestabellen.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Ta bort en väg

Spelbokskoden i det här avsnittet tar bort en rutt från en rutttabell.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Hämta information om flödestabell

Spelbokskoden i det här avsnittet `azure_rm_routetable_facts` använder modulen Ansible för att hämta vägtabellinformation.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Ta bort en routningstabell

Spelbokskoden i det här avsnittet är en rutttabell.

När en flödestabell tas bort tas även alla dess vägar bort.

Det går inte att ta bort en vägtabell om den är associerad med ett undernät. [Koppla från flödestabellen från alla undernät](#dissociate-a-route-table-from-a-subnet) innan du försöker ta bort flödestabellen. 

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)