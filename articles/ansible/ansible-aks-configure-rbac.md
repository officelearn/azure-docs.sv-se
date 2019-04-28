---
title: Självstudie – Konfigurera roller för rollbaserad åtkomstkontroll (RBAC) i Azure Kubernetes Service (AKS) med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att konfigurera RBAC i Azure Kubernetes Service(AKS) kluster
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: bdf3d719627673bfb2c6b25957abb849c46d4e9e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767266"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Självstudier: Konfigurera roller för rollbaserad åtkomstkontroll (RBAC) i Azure Kubernetes Service (AKS) med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS kan konfigureras för att använda [Azure Active Directory (AD)](/azure/active-directory/) för autentisering av användare. När du konfigurerat använder din Azure AD-Autentiseringstoken för att logga in på AKS-klustret. RBAC kan baseras på en användares identitet eller gruppmedlemskap för katalogen.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa en Azure AD-aktiverade AKS-kluster
> * Konfigurera en RBAC-roll i klustret

## <a name="prerequisites"></a>Nödvändiga komponenter

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Installera biblioteket RedHat OpenShift** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Konfigurera Azure AD för AKS-autentisering

När du konfigurerar Azure AD för AKS-autentisering kan konfigureras två Azure AD-program. Den här åtgärden måste utföras av en administratör för Azure-klient. Mer information finns i [integrera Azure Active Directory med AKS](/azure/aks/aad-integration#create-server-application). 

Från Azure-klient-administratör får du följande värden:

- Server apphemlighet
- Server app-ID
- Klientapp-ID 
- Klient-ID:t

Dessa värden krävs för att köra exemplet spelbok.  

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

I det här avsnittet skapar du ett AKS med den [Azure AD-program](#configure-azure-ad-for-aks-authentication).

Här följer kommentarer att tänka på när du arbetar med exempel-strategibok:

- Spelboken har lästs in `ssh_key` från `~/.ssh/id_rsa.pub`. Om du ändrar den använda enradigt format - börjar med ”ssh-rsa” (utan citattecken).
- Den `client_id` och `client_secret` värden har lästs in från `~/.azure/credentials`, vilket är standardfilen för autentiseringsuppgifter. Du kan ange dessa värden till tjänstens huvudnamn eller läsa in dessa värden från miljövariabler:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Spara följande spelbok som `aks-create.yml`:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Hämta Azure AD-objekt-ID

Om du vill skapa en RBAC-bindning måste du först hämta Azure AD-objekt-ID 

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. I sökfältet högst upp på sidan Ange `Azure Active Directory`. 

1. Klicka på `Enter`.

1. I den **hantera** menyn och välj **användare**.

1. Sök efter ditt konto i namnfältet.

1. I den **namn** kolumn, klicka på länken till ditt konto.

1. I den **identitet** avsnittet, kopiera den **objekt-ID**.

    ![Kopiera Azure AD-objekt-ID.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Skapa RBAC-bindning

I det här avsnittet ska skapa du en rollen bindning eller kluster roll bindningen i AKS. 

Spara följande spelbok som `kube-role.yml`:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Ersätt den `&lt;your-aad-account>` med Azure AD-klienten [objekt-ID](#get-the-azure-ad-object-id).

Spara följande spelboken - som distribuerar din nya roll till AKS - som `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Köra exemplet spelbok

Det här avsnittet innehåller komplett exempel spelboken som anropar aktiviteterna i den här artikeln. 

Spara följande spelbok som `aks-rbac.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

I den `vars` avsnittet, Ersätt följande platshållarna med din Azure AD-information:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Kör en fullständig spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Kontrollera resultatet

I det här avsnittet använder du kubectl lista de noder som skapas i den här artikeln.

Ange följande kommando i Kommandotolken terminal:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Kommandot omdirigerar dig till en autentiseringssida. Logga in med ditt Azure-konto.

När autentiseringen är klar visar kubectl noder på liknande sätt till följande resultat:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort de resurser som skapades i den här artikeln. 

Spara följande kod som `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansible i Azure](/azure/ansible/)