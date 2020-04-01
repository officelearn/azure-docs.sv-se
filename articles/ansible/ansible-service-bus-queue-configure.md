---
title: Självstudiekurs - Konfigurera köer i Azure Service Bus med Ansible
description: Lär dig hur du använder Ansible för att skapa en Azure Service Bus-kö
keywords: ansible, azurblå, devops, bash, playbook, servicebuss, kö
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8ba4c2296d903c4f35aa36eb92dfbc3b56ec4b18
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76713232"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Självstudiekurs: Konfigurera köer i Azure Service Bus med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa en kö
> * Skapa en SAS-plicy
> * Hämta namnområdesinformation
> * Hämta köinformation
> * Återkalla sas-principen i kön

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Skapa servicebusskön

Exempelspelbokskoden skapar följande resurser:
- Azure-resursgrupp
- Service Bus-namnområde i resursgruppen
- Service Bus kö med namnområdet

Spara följande spelbok som `servicebus_queue.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Skapa SAS-principen

En [SAS (Shared Access Signature)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) är en anspråksbaserad auktoriseringsmekanism med token. 

Exempelspelskoden skapar två SAS-principer för en Service Bus-kö med olika behörigheter.

Spara följande spelbok som `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Innan du kör spelboken läser du följande anmärkningar:
- Värdet `rights` representerar det privilegium en användare har med kön. Ange något av följande `manage` `listen`värden: , , `send`eller `listen_send`.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Hämta namnområdesinformation

Exempel på spelbokskod frågar namnområdesinformationen.

Spara följande spelbok som `servicebus_namespace_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Innan du kör spelboken läser du följande anmärkningar:
- Värdet `show_sas_policies` anger om SAS-principerna ska visas under det angivna namnområdet. Som standard är `False` värdet att undvika ytterligare nätverksomkostnader.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Hämta köinformation

Exempel på spelbokskod frågar köinformation. 

Spara följande spelbok som `servicebus_queue_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Innan du kör spelboken läser du följande anmärkningar:
- Värdet `show_sas_policies` anger om SAS-principerna ska visas under den angivna kön. Som standard är det `False` här värdet inställt på att undvika ytterligare nätverksomkostnader.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Återkalla sas-principen i kön

Exempelspelbokskoden tar bort en SAS-köprincip.

Spara följande spelbok som `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort de resurser som skapas i den här artikeln. 

Spara följande kod `cleanup.yml`som:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Självstudiekurs: Konfigurera ett ämne i Azure Service Bus med Ansible](ansible-service-bus-topic-configure.md)