---
title: Självstudie – konfigurera ämnen i Azure Service Bus med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att skapa en Azure Service Bus-ämne
keywords: ansible, azure, devops, bash, playbook, service bus, ämnen, prenumerationer
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: f7c6df05c19683fe739520f622e6bcab8478cbd9
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767140"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Självstudier: Konfigurera ämnen i Azure Service Bus med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa ett ämne
> * Skapa en prenumeration
> * Skapa en SAS-princip
> * Hämta namnområdet information
> * Hämta information om ämne och en prenumeration
> * Återkalla en SAS-princip

## <a name="prerequisites"></a>Nödvändiga komponenter

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Skapa Service Bus-ämnet

Spelboken exempelkoden skapar följande resurser:
- Azure-resursgrupp
- Service Bus-namnområde i resursgruppen
- Service Bus-ämne med namnområde

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Skapa prenumerationen

Spelboken exempelkoden skapar prenumeration under en Service Bus-ämne. Azure Service Bus-ämnen kan ha flera prenumerationer. En prenumerant till ett ämne kan får en kopia av alla meddelanden som skickas till ämnet. Prenumerationer är namngivna entiteter, som skapas varaktigt, men du kan också kan upphöra att gälla.

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Skapa SAS-princip

En [signatur för delad åtkomst (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) är en mekanism för anspråksbaserad auktorisering med hjälp av token. 

Spelboken exempelkoden skapar två SAS-principer för en Service Bus-kö med olika behörigheter.

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Hämta information om ämne och en prenumeration

De strategibok kod Exempelfrågor för följande information:
- Information för Service Bus-ämnet
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

Se följande information innan du kör spelboken:
- Den `show_sas_policies` värdet anger om du vill visa SAS-principer under den angivna kön. Det här värdet anges som standard till `False` att undvika ytterligare nätverksresurser.

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook servicebus_list.yml
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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)