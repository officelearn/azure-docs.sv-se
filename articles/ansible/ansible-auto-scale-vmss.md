---
title: Skala en VM-skalningsuppsättning automatiskt i Azure med Ansible
description: Lär dig att använda Ansible för att skala en VM-skalningsuppsättning med autoskalning i Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, scale, autoscale, virtual machine, virtual machine scale set, vmss
author: tomarcher
manager: jeconnoc
ms.author: yuwzho, kyliel
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: c6678d6df3a695d3a0471e5779bc3af4b6ba6c84
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53411169"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Skala en VM-skalningsuppsättning automatiskt i Azure med Ansible
Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera dina VM-skalningsuppsättningar (VMSS) i Azure på samma sätt som för alla andra Azure-resurser. 

När du skapar en skalningsuppsättning, definierar du antalet virtuella datorinstanser som du vill köra. När ditt program behöver ändras, kan du automatiskt öka eller minska antalet virtuella datorinstanser. Möjligheten att skala automatiskt låter dig hålla dig uppdaterad med kundernas behov eller svara på ändringar i programprestandan under hela livscykeln för din app. I den här artikeln skapar du en autoskalningsinställning och kopplar den till en befintlig VM-skalningsuppsättning. Du kan konfigurera en regel som skalar ut eller in efter behov i inställningen för autoskalning.

## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- En befintlig VM-skalningsuppsättning i Azure. - Om du inte har någon, kan du [skapa VM-skalningsuppsättningar i Azure med Ansible](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss).

> [!Note]
> Ansible 2.7 krävs för att köra följande exempelspelböcker i den här självstudien. 

## <a name="auto-scale-based-on-a-schedule"></a>Autoskalning baserat på ett schema   
Om du vill aktivera autoskalning för en skalningsuppsättning börjar du med att definiera en autoskalningsprofil. Den här profilen definierar skalningsuppsättningens förvalda, lägsta och högsta kapacitet. Med hjälp av dessa restriktioner kan du begränsa kostnaderna genom att inte skapa VM-instanser kontinuerligt, samtidigt som du kan balansera godtagbara prestanda med minsta antal instanser som bevaras vid en nedskalning. 

Du kan skala in och ut i Virtual Machine Scale Sets med ett återkommande schema eller vid ett visst datum. I det här avsnittet visas ett exempel på en Ansible-spelbok där en inställning för autoskalning skapas, som ökar antalet VM-instanser till tre i dina skalningsuppsättningar kl. 10:00 varje måndag i tidszonen PST. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
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

Spara spelboken som *vmss-auto-scale.yml*. Med kommandot **ansible-playbook** kör du Ansible-spelboken enligt följande:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>Autoskalning som baseras på prestandadata
Om dina programkrav ökar, ökar även belastningen på VM-instanserna i dina skalningsuppsättningar. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Du kan styra vilka mått som ska övervakas, som CPU eller disk, hur länge programbelastningen måste uppfylla ett visst tröskelvärde och hur många virtuella datorinstanser som ska läggas till skalningsuppsättningen.

Du kan skala in och ut i Virtual Machine Scale Sets baserat på tröskelvärden för prestandamått, med ett återkommande schema eller vid ett visst datum. I det här avsnittet visas ett exempel på en Ansible-spelbok som kontrollerar arbetsbelastningen under de senaste 10 minuterna kl. 18:00 varje måndag i tidszonen PST. Den skalar sedan ut antalet VM-instanser i dina skalningsuppsättningar med fyra eller skalar in till en instans enligt CPU:ns procentmått. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
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

Spara spelboken som *vmss-auto-scale-metrics.yml*. Med kommandot **ansible-playbook** kör du Ansible-spelboken enligt följande:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>Hämta information om befintliga inställningar för autoskalning
Du kan hämta information om alla autoskalningsinställningar via modulen *azure_rm_autoscale_facts* med spelboken på följande sätt:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>Inaktivera inställningarna för autoskalning
Du kan inaktivera inställningen för autoskalning genom att ändra `enabled: true` till `enabled: false`, eller genom att ta bort spelbokens inställningar för autoskalning på följande sätt:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Exempel på Ansible-spelbok för VM-skalningsuppsättningar](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)