---
title: Självstudiekurs - Konfigurera appar i Azure App Service med Ansible
description: Lär dig hur du skapar en app i Azure App Service med Java 8 och Tomcat-behållarkörningen
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 2891ff47b17900c4c1c8e1c21f22495b65108fd5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156568"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Självstudiekurs: Konfigurera appar i Azure App Service med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa en app i Azure App Service med Java 8 och Tomcat-behållarens körning
> * Skapa en Azure Traffic Manager-profil
> * Definiera en Traffic Manager-slutpunkt med hjälp av den skapade appen

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Skapa en grundläggande apptjänst

Spelbokskoden i det här avsnittet definierar följande resurser:

* Azure-resursgrupp där App Service-planen och appen distribueras
* Apptjänst på Linux med Java 8 och Tomcat-behållaren runtime

Spara följande spelbok som `firstwebapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook firstwebapp.yml
```

När du har kört spelboken visas utdata som liknar följande resultat:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Skapa en app och använda Azure Traffic Manager

[Med Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) kan du styra hur begäranden från webbklienter distribueras till appar i Azure App Service. När App Service-slutpunkter läggs till i en Azure Traffic Manager-profil spårar Traffic Manager statusen för dina App Service-appar. Statusvärden är körs, stoppad och borttagen. Traffic Manager används för att bestämma vilka slutpunkter som ska ta emot trafiken.

I App Service körs en app i en [App Service-plan](/azure/app-service/overview-hosting-plans). En App Service-plan definierar en uppsättning beräkningsresurser för en app som ska köras. Du kan hantera din App Service-plan och webbapp i olika grupper.

Spelbokskoden i det här avsnittet definierar följande resurser:

* Azure-resursgrupp där App Service-planen distribueras
* App Service-plan
* Azure-resursgrupp där appen distribueras
* Apptjänst på Linux med Java 8 och Tomcat-behållaren runtime
* Traffic Manager-profil
* Traffic Manager-slutpunkt med hjälp av den skapade appen

Spara följande spelbok som `webapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook webapp.yml
```

När du har kört spelboken visas utdata som liknar följande resultat:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Självstudiekurs: Skala appar i Azure App Service med Ansible](/azure/ansible/ansible-scale-azure-web-apps)