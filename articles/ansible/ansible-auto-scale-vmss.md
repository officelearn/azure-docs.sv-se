---
title: Självstudie – automatisk skalning VM scale sets i Azure med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att skala VM scale sets med automatisk skalning i Azure
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 4f2cd66b7460fc6fe48cb55f45bf4bc309ae054c
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231277"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Självstudier: Skala VM scale sets i Azure med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Funktionen för att automatiskt justera antalet Virtuella datorinstanser kallas [Autoskala](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Fördelen med automatisk skalning är det minskar hanteringsomkostnaderna för att övervaka och optimera prestanda för ditt program. Automatisk skalning kan konfigureras vid behov eller enligt ett definierat schema. Med Ansible kan ange du regler för automatisk skalning som definierar acceptabel prestanda för en positiv kundupplevelse.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definiera en autoskalningsprofil
> * Automatisk skalning baserat på ett återkommande schema
> * Automatisk skalning baserat på prestanda
> * Hämta information om automatisk skalning 
> * Inaktivera en autoskalningsinställning

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Automatisk skalning baserat på ett schema

Om du vill aktivera autoskalning för en skalningsuppsättning börjar du med att definiera en autoskalningsprofil. Den här profilen definierar skalningsuppsättningens förvalda, lägsta och högsta kapacitet. Med hjälp av dessa restriktioner kan du begränsa kostnaderna genom att inte skapa VM-instanser kontinuerligt, samtidigt som du kan balansera godtagbara prestanda med minsta antal instanser som bevaras vid en nedskalning. 

Ansible kan du skala dina skalningsuppsättningar på ett visst datum eller återkommande schema.

Spelboken koden i det här avsnittet ökar antalet Virtuella datorinstanser till tre kl. 10:00 varje måndag.

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatisk skalning baserat på prestandadata

Om dina programkrav ökar, ökar även belastningen på VM-instanserna i dina skalningsuppsättningar. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Ansible kan du styra vilka mått som ska övervakas, till exempel CPU-användning och diskanvändning app-inläsningstid. Du kan skala i och skala ut i skala anger baserat på prestanda tröskelmått, genom ett återkommande schema eller genom att ett visst datum. 

Spelboken koden i det här avsnittet kontrollerar CPU-belastningen för tidigare 10 minuter vid 18:00 varje måndag. 

Spelboken gör baserat på mått för CPU-procentandel, något av följande åtgärder:

- Skalar ut antalet VM-instanser till fyra
- Skalar in antalet VM-instanser till en

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Hämta information om inställningar för automatisk skalning 

Spelboken koden i det här avsnittet använder de `azure_rm_autoscale_facts` modul för att hämta information om autoskalningsinställningen.

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Inaktivera inställningarna för automatisk skalning

Det finns två sätt att inaktivera inställningarna för automatisk skalning. Ett sätt är att ändra den `enabled` nyckel från `true` till `false`. Det andra sättet är att ta bort inställningen.

Spelboken koden i det här avsnittet tar du bort inställningen för automatisk skalning. 

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Självstudie: Update anpassad avbildning av Azure VM scale sets med Ansible](./ansible-vmss-update-image.md)