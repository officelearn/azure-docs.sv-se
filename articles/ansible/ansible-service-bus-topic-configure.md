---
title: Självstudie – konfigurera ämnen i Azure Service Bus med Ansible
description: Lär dig hur du använder Ansible för att skapa ett Azure Service Bus ämne
keywords: Ansible, Azure, DevOps, bash, Spelbok, Service Bus, ämnen, prenumerationer
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 952779db582f9437f10608bf86b0b80560ded2c0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241218"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Självstudie: Konfigurera ämnen i Azure Service Bus med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa ett ämne
> * Skapa en prenumeration
> * Skapa en SAS-princip
> * Hämta namn områdes information
> * Hämta ämnes-och prenumerations information
> * Återkalla en SAS-princip

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Skapa Service Bus avsnittet

Exemplet Spelbok-koden skapar följande resurser:
- Azure-resursgrupp
- Service Bus namn område i resurs gruppen
- Service Bus ämne med namn området

Spara följande spelbok som `servicebus_topic.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Skapa prenumerationen

Exemplet Spelbok-koden skapar prenumerationen under ett Service Bus ämne. Azure Service Bus ämnen kan ha flera prenumerationer. En prenumerant på ett ämne kan ta emot en kopia av varje meddelande som skickas till ämnet. Prenumerationer kallas entiteter, som varaktigt skapas, men som kan gå ut.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Spara följande spelbok som `servicebus_subscription.yml`:

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Skapa SAS-principen

En [signatur för delad åtkomst (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) är en anspråksbaserad mekanism för auktorisering med hjälp av tokens. 

Exempel koden Spelbok skapar två SAS-principer för en Service Bus kö med olika behörigheter.

Spara följande spelbok som `servicebus_topic_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook servicebus_topic_policy.yml
```

## <a name="retrieve-namespace-information"></a>Hämta namn områdes information

Exempel koden Spelbok frågar efter namn områdes informationen.

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

Se följande anmärkningar innan du kör Spelbok:
- Värdet `show_sas_policies` anger om SAS-principerna ska visas i den angivna namn rymden. Som standard är värdet `False` för att undvika ytterligare nätverks belastning.

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-topic-and-subscription-information"></a>Hämta ämnes-och prenumerations information

Exemplet på Spelbok kod frågar efter följande information:
- Information om Service Bus ämnet
- Lista över prenumerations information för ämnet
 
Spara följande spelbok som `servicebus_list.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

Se följande anmärkningar innan du kör Spelbok:
- Värdet `show_sas_policies` anger om SAS-principerna ska visas i den angivna kön. Som standard är det här värdet inställt på `False` för att undvika ytterligare nätverks belastning.

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook servicebus_list.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Återkalla SAS-principen för kön

Spelbok-koden tar bort en SAS-princip för kö.

Spara följande spelbok som `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de resurser som skapats i den här artikeln när de inte längre behövs. 

Spara följande kod som `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)