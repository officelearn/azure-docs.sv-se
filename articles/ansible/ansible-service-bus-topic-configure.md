---
title: Självstudiekurs - Konfigurera ämnen i Azure Service Bus med Ansible
description: Lär dig hur du använder Ansible för att skapa ett Azure Service Bus-ämne
keywords: ansible, azurblå, devops, bash, playbook, servicebuss, ämnen, prenumerationer
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d07c7622043353a79d5a82994c2fab4f0835b453
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155791"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Självstudiekurs: Konfigurera ämnen i Azure Service Bus med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa ett ämne
> * Skapa en prenumeration
> * Skapa en SAS-princip
> * Hämta namnområdesinformation
> * Hämta ämnes- och prenumerationsinformation
> * Återkalla en SAS-princip

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Skapa servicebussavsnittet

Exempelspelbokskoden skapar följande resurser:
- Azure-resursgrupp
- Service Bus-namnområde i resursgruppen
- Service Bus-ämne med namnområdet

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Skapa prenumerationen

Exempelspelbokskoden skapar prenumerationen under ett Service Bus-ämne. Azure Service Bus-ämnen kan ha flera prenumerationer. En prenumerant på ett ämne kan ta emot en kopia av varje meddelande som skickas till ämnet. Prenumerationer heter entiteter, som skapas på ett sätt, men som eventuellt kan upphöra att gälla.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Skapa SAS-principen

En [SAS (Shared Access Signature)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) är en anspråksbaserad auktoriseringsmekanism med token. 

Exempelspelskoden skapar två SAS-principer för en Service Bus-kö med olika behörigheter.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Hämta ämnes- och prenumerationsinformation

Exempel på spelbokskodfrågor för följande information:
- Information om servicebuss
- Lista över prenumerationsinformation för ämnet
 
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

Innan du kör spelboken läser du följande anmärkningar:
- Värdet `show_sas_policies` anger om SAS-principerna ska visas under den angivna kön. Som standard är det `False` här värdet inställt på att undvika ytterligare nätverksomkostnader.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook servicebus_list.yml
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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)