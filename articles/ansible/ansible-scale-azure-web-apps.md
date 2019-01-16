---
title: Skala Azure App Service-webbappar med hjälp av Ansible
description: Lär dig att använda Ansible för att skapa en webbapp med Java 8 och Tomcat-körmiljö för container i App Service i Linux
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, scale, Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 4ef8320d3eba841ee64557e31e63b4e79ee3aa92
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159901"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>Skala Azure App Service-webbappar med hjälp av Ansible
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (eller bara Web Apps) är värd för webbprogram, REST-API:er och mobila serverdelar. Du kan utveckla i det språk du föredrar&mdash; .NET, .NET Core, Java, Ruby, Node.js, PHP eller Python.

Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Den här artikeln visar hur du använder Ansible för att skala din app i Azure App Service.

## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service-webbappar** – Om du inte redan har en webbapp i Azure App Service kan du [skapa Azure-webbappar med hjälp av Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app-in-app-service"></a>Skala upp en app i App Service
Du kan skala upp genom att ändra prisnivån för den App Service-plan som appen hör till. I det här avsnittet visas en Ansible-exempelspelbok som definierar följande åtgärd:
- Hämta fakta om en befintlig App Service-plan
- Uppdatera App Service-planen till S2 med tre arbetare

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

Spara den spelboken som *webapp_scaleup.yml*.

Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook webapp_scaleup.yml
```

När du har kört spelboken visar utdata som liknar följande exempel att App Service-planen har uppdaterats till S2 med tre arbetare:
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App service plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible i Azure](https://docs.microsoft.com/azure/ansible/)