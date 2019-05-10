---
title: Självstudie – Konfigurera köer i Azure Service Bus med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att skapa en Azure Service Bus-kö
keywords: ansible, azure, devops, bash, playbook, service bus, kö
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 6efc11106fae18beac43ab1896733ab6bfc64dad
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230768"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Självstudier: Konfigurera köer i Azure Service Bus med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa en kö
> * Skapa en SAS-plicy
> * Hämta namnområdet information
> * Hämta information om kön
> * Återkalla queue SAS-princip

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Skapa Service Bus-kö

Spelboken exempelkoden skapar följande resurser:
- Azure-resursgrupp
- Service Bus-namnområde i resursgruppen
- Service Bus-kö med namnområde

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Skapa SAS-princip

En [signatur för delad åtkomst (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) är en mekanism för anspråksbaserad auktorisering med hjälp av token. 

Spelboken exempelkoden skapar två SAS-principer för en Service Bus-kö med olika behörigheter.

Spara följande spelbok som `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
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

Se följande information innan du kör spelboken:
- Den `rights` värde representerar den behörighet som en användare har med kön. Ange något av följande värden: `manage`, `listen`, `send`, eller `listen_send`.

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Hämta namnområdet information

Spelboken exempelkoden frågar namnområde information.

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

Se följande information innan du kör spelboken:
- Den `show_sas_policies` värdet anger om du vill visa SAS-principer under det angivna namnområdet. Som standard är värdet `False` att undvika ytterligare nätverksresurser.

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Hämta information om kön

Exempelfrågor för spelbok kod köa information. 

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

Se följande information innan du kör spelboken:
- Den `show_sas_policies` värdet anger om du vill visa SAS-principer under den angivna kön. Det här värdet anges som standard till `False` att undvika ytterligare nätverksresurser.

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Återkalla queue SAS-princip

Spelboken exempelkoden tar bort en kö-SAS-princip.

Spara följande spelbok som `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort de resurser som skapades i den här artikeln. 

Spara följande kod som `cleanup.yml`:

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Självstudie: Konfigurera ett ämne i Azure Service Bus med Ansible](ansible-service-bus-topic-configure.md)