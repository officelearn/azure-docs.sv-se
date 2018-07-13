---
title: Distribuera program till VM-skalningsuppsättningar i Azure med Ansible
description: Lär dig hur du använder Ansible för att konfigurera en VM-skalningsuppsättning och distribuera program i VM-skalningsuppsättning i Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, VM, VM-skalningsuppsättning, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: b9c8058606e13c0db4908530e98cddb69d2caf50
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008855"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Distribuera program till VM-skalningsuppsättningar i Azure med Ansible
Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att distribuera program till Azure. Den här artikeln visar hur du distribuerar en Java-program till en Azure VM-skalningsuppsättning (VMSS).  

## <a name="prerequisites"></a>Förutsättningar
- **Azure-prenumeration** – om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- **Konfigurera Ansible** - [skapa Azure-autentiseringsuppgifter och konfigurera Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible och Azure Python SDK-moduler** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Virtual machine scale Sets** – om du inte redan har en virtuell datorskalning har angetts kan du [skapa en VM-skalningsuppsättning med Ansible](ansible-create-configure-vmss.md). 
- **Git** - [git](https://git-scm.com) används för att hämta ett Java-exempel som används i den här självstudien.
- **Java SE Development Kit (JDK)** – The JDK används för att skapa Java exempelprojektet.
- **Apache Maven-byggverktygen** – [Apache Maven-byggverktygen](https://maven.apache.org/download.cgi) används för att skapa Java exempelprojektet.

> [!Note]
> Ansible 2.6 krävs för att köra följande exempel spelböcker i den här självstudien. 

## <a name="get-host-information"></a>Hämta värdinformation

Det här avsnittet visas hur du använder Ansible för att hämta värdinformation för en grupp med virtuella Azure-datorer. Nedan visas ett exempel Ansible-spelbok. Koden hämtar de offentliga IP-adresserna och belastningsutjämnare i angivna resursgruppen och skapar en värdgrupp med namnet **saclesethosts** i inventeringen. 

Spara följande exempel spelboken som `get-hosts-tasks.yml`: 

  ```yaml
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

I det här avsnittet använder du git för att klona en Java-exempelprojektet från GitHub och skapa projektet. Spara följande spelboken som `app.yml`:

  ```yaml
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

Kör exemplet Ansible-spelbok med följande kommando:

  ```bash
  ansible-playbook app.yml
  ```

Utdata från kommandot ansible-spelbok visar ut ungefär så här där du ser den byggd klonade exempelappen från GitHub:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>Distribuera programmet till VMSS

Följande avsnitt i en Ansible-spelbok installerar JRE (Java Runtime Environment) på en värdgrupp med namnet **saclesethosts**, och distribuerar Java-program till en värdgrupp med namnet **saclesethosts**: 

(Ändra den `admin_password` till ditt eget lösenord.)

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:   
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
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

Du kan spara de föregående exemplet Ansible-spelboken som `vmss-setup-deploy.yml`, eller [ladda ned hela exemplet spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss). 

Du använder den ssh anslutningstypen med lösenord, måste du installera sshpass-program. 
  - Kör kommandot för Ubunto 16.04 `apt-get install sshpass`.
  - Kör kommandot för CentOS 7.4 `yum install sshpass`.

Ett felmeddelande som kan visas **med ett SSH-lösenord i stället för en nyckel är inte möjligt eftersom Värdnyckeln är aktiverad och sshpass har inte stöd för detta.  Lägga till den här värden fingeravtryck i filen known_hosts att hantera den här värden.** Om du ser detta fel kan du inaktivera värdnyckeln kontroll genom att lägga till följande rad till antingen den `/etc/ansible/ansible.cfg` fil eller `~/.ansible.cfg` fil:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Kör spelboken med följande kommando:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Utdata från köra ansible-spelbok kommando anger att Java-exempelprogram har installerats för värdgruppen för virtuella datorns skalningsuppsättning:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

Congratulation! Programmet körs nu i Azure. Du kan nu gå till URL: en för belastningsutjämnaren för din VM-skalningsuppsättning:

![Java-app som körs i en VM-skalningsuppsättning i Azure.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible-spelbok exemplet för VMSS](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)