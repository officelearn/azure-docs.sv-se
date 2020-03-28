---
title: Självstudiekurs - Skalning av virtuella datorskalor i Azure med Ansible
description: Lär dig hur du använder Ansible för att skala skalningsuppsättningar för virtuella datorer med automatisk skalning i Azure
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156826"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Självstudiekurs: Skalning av virtuella datorskalor i Azure med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Funktionen för att automatiskt justera antalet VM-instanser kallas [automatisk skalning](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Fördelen med automatisk skalning är att det minskar hanteringen omkostnader för att övervaka och optimera prestanda för ditt program. Automatisk skalning kan konfigureras som svar på efterfrågan eller enligt ett definierat schema. Med Ansible kan du ange regler för automatisk skalning som definierar den godtagbara prestandan för en positiv kundupplevelse.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definiera en autoskalningsprofil
> * Automatisk skalning baserat på ett återkommande schema
> * Automatisk skalning baserat på appens prestanda
> * Hämta information om inställningar för automatisk skalning 
> * Inaktivera en automatisk skalningsinställning

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Automatisk skalning baserat på ett schema

Om du vill aktivera autoskalning för en skalningsuppsättning börjar du med att definiera en autoskalningsprofil. Den här profilen definierar skalningsuppsättningens förvalda, lägsta och högsta kapacitet. Med hjälp av dessa restriktioner kan du begränsa kostnaderna genom att inte skapa VM-instanser kontinuerligt, samtidigt som du kan balansera godtagbara prestanda med minsta antal instanser som bevaras vid en nedskalning. 

Med Ansible kan du skala skalningsuppsättningarna på ett visst datum eller ett återkommande schema.

Spelbokskoden i det här avsnittet ökar antalet VM-instanser till tre klockan 10:00 varje måndag.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatisk skalning baserat på prestandadata

Om dina programkrav ökar, ökar även belastningen på VM-instanserna i dina skalningsuppsättningar. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Med Ansible kan du styra vilka mått som ska övervakas, till exempel CPU-användning, diskanvändning och appinläsningstid. Du kan skala in och skala ut i skalningsuppsättningar baserat på tröskelvärden för prestandamått, ett återkommande schema eller efter ett visst datum. 

Spelbokskoden i det här avsnittet kontrollerar CPU-arbetsbelastningen under de föregående 10 minuterna klockan 18:00 varje måndag. 

Baserat på processorprocentmåtten gör spelboken någon av följande åtgärder:

- Skalar ut antalet VM-instanser till fyra
- Skalor i antalet VM-instanser till en

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Hämta information om inställningar för automatisk skalning 

Spelbokskoden i det `azure_rm_autoscale_facts` här avsnittet använder modulen för att hämta information om inställningen för automatisk skalning.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Inaktivera inställningar för automatisk skalning

Det finns två sätt att inaktivera inställningar för automatisk skalning. Ett sätt är `enabled` att `true` ändra `false`nyckeln från till . Det andra sättet är att ta bort inställningen.

Spelbokskoden i det här avsnittet tar bort inställningen för automatisk skalning. 

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Självstudiekurs: Uppdatera anpassad avbildning av Azure-skalningsuppsättningar för virtuella datorer med Ansible](./ansible-vmss-update-image.md)