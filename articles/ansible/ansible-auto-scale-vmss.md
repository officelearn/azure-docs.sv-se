---
title: Självstudie – skala skalnings uppsättningar för virtuella datorer i Azure med Ansible
description: Lär dig hur du använder Ansible för att skala skalnings uppsättningar för virtuella datorer med autoskalning i Azure
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 784cb532c11b16c820336ceeaf8d38f0225c832f
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242097"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Självstudie: skala skalnings uppsättningar för virtuella datorer i Azure med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Funktionen för automatisk justering av antalet virtuella dator instanser kallas för automatisk [skalning](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Fördelen med autoskalning är att det minskar hanterings omkostnaderna för att övervaka och optimera programmets prestanda. Autoskalning kan konfigureras som svar på begäran eller enligt ett definierat schema. Med Ansible kan du ange regler för autoskalning som definierar acceptabla prestanda för en positiv kund upplevelse.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definiera en autoskalningsprofil
> * Autoskalning baserat på ett återkommande schema
> * Autoskalning baserat på App-prestanda
> * Hämta information om autoskalning-inställningar 
> * Inaktivera en inställning för autoskalning

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Autoskalning baserat på ett schema

Om du vill aktivera autoskalning för en skalningsuppsättning börjar du med att definiera en autoskalningsprofil. Den här profilen definierar skalningsuppsättningens förvalda, lägsta och högsta kapacitet. Med hjälp av dessa restriktioner kan du begränsa kostnaderna genom att inte skapa VM-instanser kontinuerligt, samtidigt som du kan balansera godtagbara prestanda med minsta antal instanser som bevaras vid en nedskalning. 

Med Ansible kan du skala skalnings uppsättningarna på ett visst datum eller återkommande schema.

Spelbok-koden i det här avsnittet ökar antalet virtuella dator instanser till tre vid 10:00 varje måndag.

Spara följande spelbok som `vmss-auto-scale.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Autoskalning baserat på prestanda data

Om dina programkrav ökar, ökar även belastningen på VM-instanserna i dina skalningsuppsättningar. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Med Ansible kan du kontrol lera vilka mått som ska övervakas, till exempel processor användning, disk användning och inläsnings tid för appar. Du kan skala in och skala ut i skalnings uppsättningar baserat på tröskelvärden för prestanda mått, med ett återkommande schema eller ett visst datum. 

Spelbok-koden i det här avsnittet kontrollerar CPU-arbetsbelastningen under de senaste 10 minuterna vid 18:00 varje måndag. 

Spelbok gör någon av följande åtgärder baserat på måtten för PROCESSORns procents ATS:

- Skalar ut antalet virtuella dator instanser till fyra
- Skalar i antal VM-instanser till en

Spara följande spelbok som `vmss-auto-scale-metrics.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Hämta information om autoskalning-inställningar 

Spelbok-koden i det här avsnittet använder modulen `azure_rm_autoscale_facts` för att hämta information om inställningen för autoskalning.

Spara följande spelbok som `vmss-auto-scale-get-settings.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Inaktivera inställningar för autoskalning

Det finns två sätt att inaktivera inställningar för autoskalning. Ett sätt är att ändra `enabled`-nyckeln från `true` till `false`. Det andra sättet är att ta bort inställningen.

Spelbok-koden i det här avsnittet tar bort den automatiska skalnings inställningen. 

Spara följande spelbok som `vmss-auto-scale-delete-setting.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Självstudie: uppdatera anpassad avbildning av skalnings uppsättningar för virtuella Azure-datorer med Ansible](./ansible-vmss-update-image.md)