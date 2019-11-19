---
title: Självstudie – Skala appar i Azure App Service med Ansible
description: Lär dig hur du skalar upp en app i Azure App Service
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, scale, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155925"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Självstudie: Skala appar i Azure App Service med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Hämta fakta om en befintlig App Service-plan
> * Skala upp App Service plan till S2 med tre arbetare

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service app** – om du inte har en Azure App Service-app [konfigurerar du en app i Azure App Service med Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Skala upp en app

Det finns två arbets flöden för skalning: *skala upp* och *skala ut*.

**Skala upp:** För att skala upp innebär att du får fler resurser. Bland dessa resurser ingår processor, minne, disk utrymme, virtuella datorer och mycket annat. Du skalar upp en app genom att ändra pris nivån för den App Service plan som appen tillhör. 
**Skala ut:** För att skala ut innebär att öka antalet VM-instanser som kör din app. Beroende på din App Service plan pris nivå kan du skala upp till så många som 20 instanser. Med automatisk [skalning](/azure/azure-monitor/platform/autoscale-get-started) kan du skala antalet instanser automatiskt baserat på fördefinierade regler och scheman.

Spelbok-koden i det här avsnittet definierar följande åtgärd:

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

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook webapp_scaleup.yml
```

När du har kört Spelbok visas utdata som liknar följande resultat:

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