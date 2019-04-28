---
title: Självstudie – skala appar i Azure App Service med Ansible | Microsoft Docs
description: Lär dig att skala upp en app i Azure App Service
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, scale, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 213c4e086db8b40fdec26ce9fb3e0be5ad055cbc
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764309"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Självstudier: Skala appar i Azure App Service med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Hämta fakta om en befintlig App Service-plan
> * Skala upp App Service-planen s2 med tre Worker

## <a name="prerequisites"></a>Nödvändiga komponenter

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service-app** – om du inte har en Azure App Service-app, [konfigurera en app i Azure App Service med Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Skala upp en app

Det finns två arbetsflöden för att skala: *skala upp* och *skala ut*.

**Skala upp:** Om du vill skala upp innebär det att skaffa fler resurser. Dessa resurser inkluderar CPU, minne, diskutrymme, virtuella datorer och mycket mer. Du skala upp en app genom att ändra prisnivån för App Service-plan som appen tillhör. 
**Skala ut:** Om du vill skala ut innebär det att öka antalet VM-instanser som kör din app. Beroende på din App Service-plan som prisnivå kan skala du ut till upp till 20 instanser. [Automatisk skalning](/azure/azure-monitor/platform/autoscale-get-started) gör att du kan skala instansantalet automatiskt baserat på fördefinierade regler och scheman.

Spelboken koden i det här avsnittet definierar följande åtgärd:

* Hämta fakta om en befintlig App Service-plan
* Uppdatera App Service-planen till S2 med tre arbetare

Spara följande spelbok som `webapp_scaleup.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook webapp_scaleup.yml
```

När strategiboken, kan du se utdata som liknar följande resultat:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)