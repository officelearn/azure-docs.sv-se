---
title: Självstudiekurs - Distribuera appar till skalningsuppsättningar för virtuella datorer i Azure med Ansible
description: Lär dig hur du använder Ansible för att konfigurera Azure-skalningsuppsättningar för virtuella datorer och distribuera program i skalningsuppsättningen
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.openlocfilehash: d638ae3f0c33734b42ef5456772fcd2bc62e35a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75940865"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Självstudiekurs: Distribuera appar till skalningsuppsättningar för virtuella datorer i Azure med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Hämta värdinformation för en grupp virtuella Azure-datorer
> * Klona och skapa exempelappen
> * Installera JRE (Java Runtime Environment) på en skalningsuppsättning
> * Distribuera Java-programmet till en skalningsuppsättning

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) används för att ladda ner ett Java-exempel som används i den här självstudien.
- **Java SE Development Kit (JDK)** – [JDK](https://aka.ms/azure-jdks) används till att skapa Java-exempelprojektet.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) används för att bygga exempel Java-projektet.

## <a name="get-host-information"></a>Hämta värdinformation

Spelbokskoden i det här avsnittet hämtar värdinformation för en grupp virtuella datorer. Koden hämtar offentliga IP-adresser och belastningsutjämnaren inom en `scalesethosts` angiven resursgrupp och skapar en värdgrupp med namnet i lagret.

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
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Förbereda ett program för distribution

Spelbokskoden i det `git` här avsnittet används för att klona ett Java-exempelprojekt från GitHub och bygger projektet. 

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

När du har kört spelboken visas utdata som liknar följande resultat:

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

Spelbokskoden i det här avsnittet används för att:

* Installera JRE på en värdgrupp med namnet`saclesethosts`
* Distribuera Java-programmet till en värdgrupp med namnet`saclesethosts`

Det finns två sätt att hämta exempelspelboken:

* [Ladda ner spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) och `vmss-setup-deploy.yml`spara den på .
* Skapa en ny `vmss-setup-deploy.yml` fil med namnet och kopiera följande innehåll till den:

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

Innan du kör spelboken läser du följande anmärkningar:

* I `vars` avsnittet ersätter `{{ admin_password }}` du platshållaren med ditt eget lösenord.
* Om du vill använda ssh-anslutningstypen med lösenord installerar du sshpass-programmet:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    Centos:

    ```bash
    yum install sshpass
    ```

* I vissa miljöer kan du se ett fel om hur du använder ett SSH-lösenord i stället för en nyckel. Om felet visas kan du inaktivera värdnyckelkontroll genom att `/etc/ansible/ansible.cfg` `~/.ansible.cfg`lägga till följande rad i eller:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Kör spelboken med följande kommando:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Utdata från att köra kommandot ansible-playbook anger att exempelt Java-programmet har installerats i värdgruppen för skalningsuppsättningen:

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

## <a name="verify-the-results"></a>Verifiera resultaten

Verifiera resultatet av ditt arbete genom att navigera till url:en för belastningsutjämnaren för skalningsuppsättningen:

![Java-app som körs i en skalningsuppsättning i Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Skalning av virtuella datorskalor i Azure med Ansible](./ansible-auto-scale-vmss.md)