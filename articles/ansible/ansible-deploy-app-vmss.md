---
title: Distribuera program till skalningsuppsättningar för virtuella datorer i Azure med Ansible
description: Läs om hur du använder Ansible för att konfigurera en skalningsuppsättning för virtuell dator och distribuera program på skalningsuppsättningen i Azure
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/11/2018
ms.openlocfilehash: 2214dd9505dff86ac26f01967a360140dee0069f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60396891"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Distribuera program till skalningsuppsättningar för virtuella datorer i Azure med Ansible
Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan distribuera program till Azure med Ansible. I den här artikeln får du se hur du distribuerar ett Java-program till en Azure VM-skalningsuppsättning (VMSS).

## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **VM-skalningsuppsättning** – Om du inte redan har en VM-skalningsuppsättning kan du [skapa en VM-skalningsuppsättning med Ansible](ansible-create-configure-vmss.md).
- **git** - [git](https://git-scm.com) används för att ladda ned ett Java-exempel som används in den här självstudien.
- **Java SE Development Kit (JDK)** – [JDK](https://aka.ms/azure-jdks) används till att skapa Java-exempelprojektet.
- **Genereringsverktyg för Apache Maven** –[Genereringsverktygen för Apache Maven](https://maven.apache.org/download.cgi) används för att skapa Java-exempelprojektet.

> [!Note]
> Ansible 2.6 krävs för att köra följande exempelspelböcker i den här självstudien.

## <a name="get-host-information"></a>Hämta värdinformation

I den här delen visas hur du använder Ansible för att hämta värdinformation för en grupp med virtuella Azure-datorer. Nedan finns ett Ansible-spelboksexempel. Koden hämtar de offentliga IP-adresserna och belastningsutjämnaren i den angivna resursgruppen och skapar en värdgrupp med namnet **scalesethosts** i inventeringen.

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

I det här avsnittet använder du git för att klona ett Java-exempelprojekt från GitHub och skapa projektet. Spara följande spelbok som `app.yml`:

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

Utdata från kommandot ansible-playbook liknar följande där du ser att det skapade exempelappen genom att klona från GitHub:

  ```Output
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

Följande avsnitt i en Ansible-spelbok installerar JRE (Java Runtime Environment) i en värdgrupp som heter **saclesethosts** och distribuerar Java-programmet till gruppen **saclesethosts**:

(Ändra `admin_password` till ditt eget lösenord.)

  ```yml
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

Du kan spara den föregående Ansible-exempelspelboken som `vmss-setup-deploy.yml` eller [ladda ned hela exempelspelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss).

Om du vill använda ssh-anslutningstypen med lösenord måste du installera sshpass-programmet.
  - För Ubuntu 16.04 kör du kommandot `apt-get install sshpass`.
  - För CentOS 7.4 kör du kommandot `yum install sshpass`.

Du kanske ser ett fel i stil med **Using an SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this. Add this host's fingerprint to your known_hosts file to manage this host** (Det går inte att använda ett SSH-lösenord istället för en nyckel eftersom värdnyckelkontroll är aktiverat och sshpass inte stöder det Lägg till värdens fingeravtryck till filen known_hosts för att hantera värden). Om du ser det här felet kan du inaktivera värdnyckelkontroll genom att lägga till följande rad till antingen filen `/etc/ansible/ansible.cfg` eller `~/.ansible.cfg`:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Kör spelboken med följande kommando:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Utdata från körning av kommandot ansible-playbook anger att Java-exempelprogrammet har installerats till värdgruppen i VM-skalningsuppsättningen:

  ```Output
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

Grattis! Programmet körs nu på Azure. Nu kan du gå till lastbalanserarens webbadress för din VM-skalningsuppsättning:

![Java-app som körs i en VM-skalningsuppsättning i Azure.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Skala en VM-skalningsuppsättning automatiskt med hjälp av Ansible](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss)
