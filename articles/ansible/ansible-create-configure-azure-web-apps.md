---
title: Skapa Azure-webbappar med Ansible
description: Lär dig att använda Ansible för att skapa en webbapp med Java 8 och Tomcat-körmiljö för container i App Service i Linux
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, Azure App Service, Web App, Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 106fe11355d629e57f71b12d1125d188b98c2236
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051353"
---
# <a name="create-azure-app-service-web-apps-by-using-ansible"></a>Skapa Azure App Service-webbappar med Ansible
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (eller bara Web Apps) är värd för webbprogram, REST API:er och mobila serverdelar. Du kan utveckla i det språk du föredrar&mdash; .NET, .NET Core, Java, Ruby, Node.js, PHP eller Python.

Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Den här artikeln visar hur du använder Ansible för att skapa en webbapp med Java Runtime. 

## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 krävs för att köra följande exempelspelböcker i den här självstudien.

## <a name="create-a-simple-app-service"></a>Skapa en enkel apptjänst
I den här delen finns en Ansible-exempelspelbok som definierar följande resurser:
- Resursgrupp, som din App Service-plan och webbapp distribueras till
- Webbapp med Java 8 och Tomcat-körmiljö för container i App Service i Linux

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
Spara den föregående spelboken som **firstwebapp.yml**.

Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook firstwebapp.yml
```

Utdata efter körningen av Ansible-spelboken visar att webbappen har skapats:

```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] *************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP *************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-service-by-using-traffic-manager"></a>Skapa en apptjänst med hjälp av Traffic Manager
Du kan använda [Azure Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) för att styra hur begäranden från webbklienter distribueras till appar i Azure App Service. När App Service-slutpunkter läggs till i en Azure Traffic Manager-profil spårar Traffic Manager statusen för dina App Service-appar. Statusvärden är körs, stoppad och borttagen. Traffic Manager kan sedan bestämma vilka av dess slutpunkter som ska ta emot trafik.

I App Service körs en app i en [App Service-plan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans
). En App Service-plan definierar en uppsättning beräkningsresurser som en webbapp ska köra. Du kan hantera din App Service-plan och webbapp i olika grupper.

I den här delen finns en Ansible-exempelspelbok som definierar följande resurser:
- Resursgrupp, som din App Service-plan distribueras till
- App Service-plan
- Sekundär resursgrupp, som din webbapp distribueras till
- Webbapp med Java 8 och Tomcat-körmiljö för container i App Service i Linux
- Traffic Manager-profil
- Traffic Manager-slutpunkt med hjälp av webbplatsen du har skapat

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
Spara den föregående spelboken som **webapp.yml** eller [ladda ned spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml).

Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook webapp.yml
```

Utdata från körningen av Ansible-spelboken visar att App service-planen, webbappen, Traffic Manager-profilen och slutpunkten har skapats:
```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Skala Azure App Service-webbappar med Ansible](https://docs.microsoft.com/azure/ansible/ansible-scale-azure-web-apps)