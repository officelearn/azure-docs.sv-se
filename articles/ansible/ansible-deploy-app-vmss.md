---
title: Självstudie – distribuera appar till skalnings uppsättningar för virtuella datorer i Azure med Ansible
description: Lär dig hur du använder Ansible för att konfigurera skalnings uppsättningar för virtuella Azure-datorer och distribuera program på skalnings uppsättningen
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.openlocfilehash: d638ae3f0c33734b42ef5456772fcd2bc62e35a4
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940865"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Självstudie: distribuera appar till skalnings uppsättningar för virtuella datorer i Azure med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Hämta värd information för en grupp med virtuella Azure-datorer
> * Klona och bygg exempel appen
> * Installera JRE (Java Runtime Environment) på en skalnings uppsättning
> * Distribuera Java-programmet till en skalnings uppsättning

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) används för att ladda ned ett Java-exempel som används in den här självstudien.
- **Java SE Development Kit (JDK)** – [JDK](https://aka.ms/azure-jdks) används till att skapa Java-exempelprojektet.
- **Apache Maven** - [Apache maven](https://maven.apache.org/download.cgi) används för att bygga exempel-Java-projektet.

## <a name="get-host-information"></a>Hämta värdinformation

Spelbok-koden i det här avsnittet hämtar värd information för en grupp virtuella datorer. Koden hämtar de offentliga IP-adresserna och belastningsutjämnaren i en angiven resurs grupp och skapar en värd grupp med namnet `scalesethosts` i inventeringen.

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

Spelbok-koden i det här avsnittet använder `git` för att klona ett Java-exempel projekt från GitHub och skapar projektet. 

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

När du har kört Spelbok visas utdata som liknar följande resultat:

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

## <a name="deploy-the-application-to-a-scale-set"></a>Distribuera programmet till en skalnings uppsättning

Spelbok-koden i det här avsnittet används för att:

* Installera JRE på en värd grupp med namnet `saclesethosts`
* Distribuera Java-programmet till en värd grupp med namnet `saclesethosts`

Det finns två sätt att hämta exempel Spelbok:

* [Ladda ned Spelbok](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) och spara den till `vmss-setup-deploy.yml`.
* Skapa en ny fil med namnet `vmss-setup-deploy.yml` och kopiera den till följande innehåll:

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

Se följande anmärkningar innan du kör Spelbok:

* I avsnittet `vars` ersätter du plats hållaren för `{{ admin_password }}` med ditt eget lösen ord.
* Om du vill använda SSH-anslutnings typen med lösen ord installerar du sshpass-programmet:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* I vissa miljöer kan du se ett fel meddelande om att använda ett SSH-lösenord i stället för en nyckel. Om du får ett fel meddelande kan du inaktivera värd nyckel kontrollen genom att lägga till följande rad för att `/etc/ansible/ansible.cfg` eller `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Kör spelboken med följande kommando:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Utdata från att köra kommandot Ansible-Spelbok anger att Java-exemplet i Java-programmet har installerats till värd gruppen i skalnings uppsättningen:

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

Verifiera resultatet av ditt arbete genom att gå till URL: en för belastningsutjämnaren för din skalnings uppsättning:

![Java-app som körs i en skalnings uppsättning i Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skala skalnings uppsättningar för virtuella datorer i Azure med Ansible](./ansible-auto-scale-vmss.md)