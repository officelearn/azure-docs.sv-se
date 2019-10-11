---
title: Självstudie – Konfigurera rollbaserad åtkomst kontroll (RBAC) roller i Azure Kubernetes service (AKS) med hjälp av Ansible
description: Lär dig hur du använder Ansible för att konfigurera RBAC i Azure Kubernetes service (AKS)-kluster
keywords: Ansible, Azure, DevOps, bash, cloudshell, Spelbok, AKS, container, AKS, Kubernetes, Azure Active Directory, RBAC
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 36a6f5ade7a60a989d2e80f2405aaa2d1d50b756
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242342"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Självstudie: Konfigurera rollbaserad åtkomst kontroll (RBAC) roller i Azure Kubernetes service (AKS) med hjälp av Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS kan konfigureras för att använda [Azure Active Directory (AD)](/azure/active-directory/) för användarautentisering. När du har konfigurerat använder du Azure AD-autentiseringstoken för att logga in på AKS-klustret. RBAC kan baseras på användarens identitet eller katalog grupp medlemskap.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa ett Azure AD-aktiverat AKS-kluster
> * Konfigurera en RBAC-roll i klustret

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Installera RedHat OpenShift-biblioteket** -  @ no__t-2

## <a name="configure-azure-ad-for-aks-authentication"></a>Konfigurera Azure AD för AKS-autentisering

När du konfigurerar Azure AD för AKS-autentisering konfigureras två Azure AD-program. Den här åtgärden måste utföras av en Azure-innehavaradministratör. Mer information finns i [integrera Azure Active Directory med AKS](/azure/aks/aad-integration#create-the-server-application). 

Hämta följande värden från Azure-klientens administratör:

- Server App Secret
- Server App-ID
- App-ID för klient 
- Klient-ID:t

Dessa värden behövs för att köra exemplet Spelbok.  

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

I det här avsnittet skapar du en AKS med [Azure AD-programmet](#configure-azure-ad-for-aks-authentication).

Här följer några viktiga kommentarer att tänka på när du arbetar med exemplet Spelbok:

- Spelbok läser in `ssh_key` från `~/.ssh/id_rsa.pub`. Om du ändrar det använder du det enkla formatet – från och med "SSH-RSA" (utan citationstecken).
- Värdena `client_id` och `client_secret` läses in från `~/.azure/credentials`, vilket är standard filen för autentiseringsuppgifter. Du kan ange dessa värden till tjänstens huvud namn eller läsa in dessa värden från miljövariablerna:

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
  azure_rm_aksversion_facts:
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

Om du vill skapa en RBAC-bindning måste du först hämta Azure AD-objekt-ID: t. 

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Skriv `Azure Active Directory` i Sök fältet högst upp på sidan. 

1. Klicka på `Enter`.

1. I menyn **Hantera** väljer **du användare**.

1. Sök efter ditt konto i fältet namn.

1. I kolumnen **namn** väljer du länken till ditt konto.

1. I avsnittet **identitet** kopierar du **objekt-ID: t**.

    ![Kopiera objekt-ID för Azure AD.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Skapa RBAC-bindning

I det här avsnittet skapar du en roll bindning eller en kluster roll bindning i AKS. 

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

Ersätt plats hållaren `&lt;your-aad-account>` med ditt Azure AD [-klientens objekt-ID](#get-the-azure-ad-object-id).

Spara följande Spelbok – som distribuerar den nya rollen till AKS-as `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Kör exemplet Spelbok

I det här avsnittet visas den fullständiga Spelbok som anropar de uppgifter som skapas i den här artikeln. 

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

I avsnittet `vars` ersätter du följande plats hållare med din Azure AD-information:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Kör den fullständiga Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Verifiera resultaten

I det här avsnittet använder du kubectl för att visa en lista över noderna som skapas i den här artikeln.

Ange följande kommando i en Terminal-prompt:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Kommandot leder dig till en autentiserings sida. Logga in med ditt Azure-konto.

När autentiseringen har autentiserats visar kubectl noderna på samma sätt som följande resultat:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de resurser som skapats i den här artikeln när de inte längre behövs. 

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

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansible i Azure](/azure/ansible/)
