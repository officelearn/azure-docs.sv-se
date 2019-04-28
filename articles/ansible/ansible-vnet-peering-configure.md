---
title: Självstudie – konfigurera Azure vnet-peering med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att ansluta virtuella nätverk med peerkoppling.
keywords: ansible, azure, devops, bash, playbook, nätverk, peering
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: d3d1aab17a94e928d113246f280c3391cae85655
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767091"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Självstudier: Konfigurera Azure vnet-peering med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Peering av virtuella nätverk (VNet)](/azure/virtual-network/virtual-network-peering-overview) kan du smidigt ansluta två virtuella Azure-nätverk. När peer-kopplats visas de två virtuella nätverk som en för anslutningsändamål. 

Trafik dirigeras mellan virtuella datorer i samma virtuella nätverk via privata IP-adresser. På samma sätt dirigeras trafiken mellan virtuella datorer i ett peer-kopplade virtuella nätverk via Microsoft-stamnätsinfrastrukturen. Därför kan kan virtuella datorer i olika virtuella nätverk kommunicera med varandra.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa två virtuella nätverk
> * Peerkoppla två virtuella nätverk
> * Ta bort peering mellan två nätverk

## <a name="prerequisites"></a>Nödvändiga komponenter

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Skapa två resursgrupper

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Exempelkod för spelbok i det här avsnittet används för att:

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

Exempelkod för spelbok i det här avsnittet används för att:

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

Exempelkod för spelbok i det här avsnittet används för att:

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

## <a name="peer-the-two-virtual-networks"></a>Peerkoppla två virtuella nätverk

Exempelkod för spelbok i det här avsnittet används för att:

- Initiera det virtuella nätverkets peering
- Peerkoppla två tidigare skapade virtuella nätverk

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

## <a name="delete-the-virtual-network-peering"></a>Ta bort det virtuella nätverkets peering

Exempelkod för spelbok i det här avsnittet används för att:

- Ta bort peering mellan de två tidigare skapade virtuella nätverk

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Hämta exemplet spelbok

Det finns två sätt att hämta hela exemplet spelboken:

- [Ladda ned spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) och spara den i `vnet_peering.yml`.
- Skapa en ny fil med namnet `vnet_peering.yml` och kopiera in följande innehåll:

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

## <a name="run-the-sample-playbook"></a>Köra exemplet spelbok

Exempelkod för spelbok i det här avsnittet används för att testa olika funktioner som visas i den här självstudien.

Här följer kommentarer att tänka på när du arbetar med exempel-strategibok:

- I den `vars` avsnittet, ersätter den `{{ resource_group_name }}` platshållare med namnet på resursgruppen.

Kör spelbok med hjälp av kommandot ansible-spelbok:

```bash
ansible-playbook vnet_peering.yml
```

När strategiboken, kan du se utdata som liknar följande resultat:

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

När den inte längre behövs kan du ta bort de resurser som skapades i den här artikeln. 

Exempelkod för spelbok i det här avsnittet används för att:

- Ta bort de två resursgrupper skapade tidigare

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

Här följer kommentarer att tänka på när du arbetar med exempel-strategibok:

- Ersätt den `{{ resource_group_name-1 }}` platshållare med namnet på den första resursgruppen skapas.
- Ersätt den `{{ resource_group_name-2 }}` platshållare med namnet på den andra resursgruppen skapas.
- Alla resurser i de två angivna resursgrupperna tas bort.

Kör spelbok med hjälp av kommandot ansible-spelbok:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)