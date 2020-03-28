---
title: Självstudiekurs - Konfigurera Azure-virtuell nätverks peering med Ansible
description: Lär dig hur du använder Ansible för att ansluta virtuella nätverk med virtuell nätverks peering.
keywords: ansible, azurblå, devops, bash, playbook, nätverk, peering
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78699a005d721b46a88a26452f5db68438793d34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155725"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Självstudiekurs: Konfigurera Azure-virtuell nätverks peering med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Med VNet-peering (Virtual Network)](/azure/virtual-network/virtual-network-peering-overview) kan du sömlöst ansluta två virtuella Azure-nätverk. När de två virtuella nätverken har peererats visas de som ett för anslutningsändamål. 

Trafiken dirigeras mellan virtuella datorer i samma virtuella nätverk via privata IP-adresser. På samma sätt dirigeras trafiken mellan virtuella datorer i ett peer-virtuellt nätverk via Microsofts stamnätsinfrastruktur. Därför kan virtuella datorer i olika virtuella nätverk kommunicera med varandra.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa två virtuella nätverk
> * Peer de två virtuella nätverk
> * Ta bort peering mellan de två nätverken

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Skapa två resursgrupper

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Exempelspelbokskoden i det här avsnittet används för att:

- Skapa två resursgrupper 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>Skapa det första virtuella nätverket

Exempelspelbokskoden i det här avsnittet används för att:

- Skapa ett virtuellt nätverk
- Skapa ett undernät i det virtuella nätverket

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>Skapa det andra virtuella nätverket

Exempelspelbokskoden i det här avsnittet används för att:

- Skapa ett virtuellt nätverk
- Skapa ett undernät i det virtuella nätverket

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>Peer de två virtuella nätverk

Exempelspelbokskoden i det här avsnittet används för att:

- Initiera virtual-network peering
- Peer två tidigare skapade virtuella nätverk

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>Ta bort den virtuella nätverks peering

Exempelspelbokskoden i det här avsnittet används för att:

- Ta bort peering mellan de två tidigare skapade virtuella nätverken

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Hämta exempelspelboken

Det finns två sätt att hämta hela exempelspelboken:

- [Ladda ner spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) och `vnet_peering.yml`spara den på .
- Skapa en ny `vnet_peering.yml` fil med namnet och kopiera följande innehåll till den:

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Kör exempeluppspelningsboken

Exempel på spelbokskod i det här avsnittet används för att testa olika funktioner som visas i hela den här självstudien.

Här är några viktiga anteckningar att tänka på när du arbetar med exempelspelsboken:

- Ersätt `vars` platshållaren `{{ resource_group_name }}` med namnet på resursgruppen i avsnittet.

Kör spelboken med kommandot ansible-playbook:

```bash
ansible-playbook vnet_peering.yml
```

När du har kört spelboken visas utdata som liknar följande resultat:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort de resurser som skapas i den här artikeln. 

Exempelspelbokskoden i det här avsnittet används för att:

- Ta bort de två resursgrupper som skapats tidigare

Spara följande spelbok som `cleanup.yml`:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Här är några viktiga anteckningar att tänka på när du arbetar med exempelspelsboken:

- Ersätt `{{ resource_group_name-1 }}` platshållaren med namnet på den första resursgruppen som skapats.
- Ersätt `{{ resource_group_name-2 }}` platshållaren med namnet på den andra resursgruppen som skapats.
- Alla resurser inom de två angivna resursgrupperna tas bort.

Kör spelboken med kommandot ansible-playbook:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)