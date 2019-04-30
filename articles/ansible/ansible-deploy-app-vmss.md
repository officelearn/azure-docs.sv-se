---
title: Självstudie – distribuera appar till skalningsuppsättningar för virtuella datorer i Azure med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att konfigurera Azure VM-skalningsuppsättningar och distribuera program i skalningsuppsättningen
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3c45a0bc5bbabeb6f4511140f83b08fd2943127c
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763283"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Självstudier: Distribuera appar till skalningsuppsättningar för virtuella datorer i Azure med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Hämta värdinformation för en grupp med virtuella Azure-datorer
> * Klona och skapa exempelappen
> * Installera JRE (Java Runtime Environment) på en skalningsuppsättning
> * Distribuera Java-program till en skalningsuppsättning

## <a name="prerequisites"></a>Nödvändiga komponenter

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- [!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) används för att ladda ned ett Java-exempel som används in den här självstudien.
- **Java SE Development Kit (JDK)** – [JDK](https://aka.ms/azure-jdks) används till att skapa Java-exempelprojektet.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) används för att skapa Java exempelprojektet.

## <a name="get-host-information"></a>Hämta värdinformation

Spelboken koden i det här avsnittet hämtar värdinformation för en grupp av virtuella datorer. Koden hämtar de offentliga IP-adresserna och belastningsutjämnare inom en angiven resursgrupp och skapar en värdgrupp med namnet `scalesethosts` i inventeringen.

Spara följande exempelspelbok som `get-hosts-tasks.yml`:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Förbereda ett program för distribution

Spelboken koden i det här avsnittet använder `git` att klona en Java-exempelprojektet från GitHub och skapar projektet. 

Spara följande spelbok som `app.yml`:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Kör Ansible-spelboksexemplet med följande kommando:

  ```bash
  ansible-playbook app.yml
  ```

När strategiboken, kan du se utdata som liknar följande resultat:

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Distribuera programmet till en skalningsuppsättning

Spelboken koden i det här avsnittet används för att:

* Installera JRE på en värdgrupp med namnet `saclesethosts`
* Distribuera Java-program till en värdgrupp med namnet `saclesethosts`

Det finns två sätt att hämta exemplet spelboken:

* [Ladda ned spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) och spara den i `vmss-setup-deploy.yml`.
* Skapa en ny fil med namnet `vmss-setup-deploy.yml` och kopiera in följande innehåll:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

Se följande information innan du kör spelboken:

* I den `vars` avsnittet, ersätter den `{{ admin_password }}` med ditt eget lösenord.
* Du använder den ssh anslutningstyp med lösenord, installera sshpass programmet:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* I vissa miljöer kan du se ett felmeddelande om att använda ett SSH-lösenord i stället för en nyckel. Om du får felet, kan du inaktivera kontroll genom att lägga till följande rad-värdnyckeln `/etc/ansible/ansible.cfg` eller `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Kör spelboken med följande kommando:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Utdata från köra ansible-spelbok kommando anger att Java-exempelprogram har installerats värdgrupp av skalningsuppsättningen:

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Kontrollera resultatet

Kontrollera resultatet av ditt arbete genom att gå till URL: en för belastningsutjämnaren för din skalningsuppsättning:

![Java-app som körs i en skalningsuppsättning i Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Skala VM scale sets i Azure med Ansible](./ansible-auto-scale-vmss.md)