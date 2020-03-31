---
title: Konfigurera Azure Red Hat OpenShift-kluster med Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs hur du konfigurerar övervakning av ett Kubernetes-kluster med Azure Monitor som finns på Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275521"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Konfigurera Azure Red Hat OpenShift-kluster med Azure Monitor för behållare

Azure Monitor för behållare ger omfattande övervakningsupplevelse för AKS-kluster (Azure Kubernetes Service) och AKS Engine. I den här artikeln beskrivs hur du aktiverar övervakning av Kubernetes-kluster som finns på [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) för att uppnå en liknande övervakningsupplevelse.

>[!NOTE]
>Stöd för Azure Red Hat OpenShift är en funktion i offentlig förhandsversion just nu.
>

Azure Monitor för behållare kan aktiveras för nya eller en eller flera befintliga distributioner av Azure Red Hat OpenShift med följande metoder som stöds:

- För ett befintligt kluster från Azure-portalen eller med Azure Resource Manager-mallen.
- För ett nytt kluster med Azure Resource Manager-mall eller när du skapar ett nytt kluster med [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Funktioner som stöds och inte stöds

Azure Monitor for containers stöder övervakning av Azure Red Hat OpenShift enligt beskrivningen i [översiktsartikeln,](container-insights-overview.md) förutom följande funktioner:

- Live-data (förhandsgranskning)
- [Samla in mått](container-insights-update-metrics.md) från klusternoder och poddar och lagra dem i Azure Monitor-måttdatabasen

## <a name="prerequisites"></a>Krav

- För att aktivera och komma åt funktionerna i Azure Monitor för behållare måste du åtminstone vara medlem i Azure *Contributor-rollen* i Azure-prenumerationen och en medlem av rollen [*Log Analytics Contributor*](../platform/manage-access.md#manage-access-using-azure-permissions) för log Analytics-arbetsytan som konfigurerats med Azure Monitor för behållare.

- Om du vill visa övervakningsdata är du medlem i rollbehörigheten [*Log Analytics-läsare*](../platform/manage-access.md#manage-access-using-azure-permissions) med arbetsytan Log Analytics konfigurerad med Azure Monitor för behållare.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Aktivera för ett nytt kluster med hjälp av en Azure Resource Manager-mall

Utför följande steg för att distribuera ett Azure Red Hat OpenShift-kluster med övervakning aktiverad. Innan du fortsätter bör du granska självstudien [Skapa ett Azure Red Hat OpenShift-kluster](../../openshift/tutorial-create-cluster.md#prerequisites) för att förstå de beroenden som du behöver konfigurera så att din miljö är korrekt konfigurerad.

Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att distribuera klustret med övervakning aktiverad och den andra innehåller parametervärden som du konfigurerar för att ange följande:

- Azure Red Hat OpenShift-klusterresurs-ID.

- Resursgruppen som klustret distribueras i.

- [Azure Active Directory-klient-ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) noterades efter att ha utfört stegen för att skapa ett eller en redan skapat.

- [Azure Active Directory-klientprogram-ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) noterades efter att ha utfört stegen för att skapa ett eller ett som redan har skapats.

- [Azure Active Directory Client hemlighet](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) noteras efter att ha utfört stegen för att skapa en eller en redan skapat.

- [Azure AD-säkerhetsgrupp](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) noterades efter att ha utfört stegen för att skapa en eller en som redan har skapats.

- Resurs-ID för en befintlig Log Analytics-arbetsyta.

- Antalet huvudnoder som ska skapas i klustret.

- Antalet beräkningsnoder i agentpoolprofilen.

- Antalet infrastrukturnoder i agentpoolprofilen.

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall läser du:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.65 eller senare. Om du vill `az --version`identifiera din version kör du . Om du behöver installera eller uppgradera Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Log Analytics-arbetsytan måste skapas innan du aktiverar övervakning med Azure PowerShell eller CLI. Om du vill skapa arbetsytan kan du konfigurera den via [Azure Resource Manager,](../../azure-monitor/platform/template-workspace-configuration.md)via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md).

1. Hämta och spara i en lokal mapp, Mallen och parameterfilen i Azure Resource Manager för att skapa ett kluster med övervakningstillägget med följande kommandon:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Logga in på Azure

    ```azurecli
    az login    
    ```

    Om du har åtkomst till `az account set -s {subscription ID}` flera `{subscription ID}` prenumerationer kör du ersättning med den prenumeration du vill använda.

3. Skapa en resursgrupp för klustret om du inte redan har en. En lista över Azure-regioner som stöder OpenShift på Azure finns i [Regioner som stöds](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Redigera JSON-parameterfilen **newClusterWithMonitoringParam.json** och uppdatera följande värden:

    - *Plats*
    - *clusterName (klusternamn)*
    - *aadTenantId (på nytt)*
    - *aadClientId (på)*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *beräknaNodeCount*
    - *infraNodeCount (infraNodeCount)*

5. Följande steg distribuerar klustret med övervakning aktiverad med hjälp av Azure CLI.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    Utdata liknar följande:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Aktivera för ett befintligt kluster

Utför följande steg för att aktivera övervakning av ett Azure Red Hat OpenShift-kluster som distribueras i Azure. Du kan utföra detta från Azure-portalen eller med hjälp av de medföljande mallarna.

### <a name="from-the-azure-portal"></a>Från Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Azure Monitor**på Azure Portal-menyn eller på startsidan . Under avsnittet **Insikter** väljer du **Behållare**.

3. På sidan **Bildskärm - behållare** väljer du Kluster som inte **övervakas**.

4. Leta reda på klustret i listan över kluster som inte övervakas och klicka på **Aktivera**. Du kan identifiera resultaten i listan genom att leta efter värdet **ARO** under kolumnen **KLUSTERTYP**.

5. Om du har en befintlig Log Analytics-arbetsyta i samma prenumeration som klustret på sidan **Onboarding to Azure Monitor för behållare** väljer du den i listrutan.  
    Listan förväljs till standardarbetsytan och platsen som klustret distribueras till i prenumerationen.

    ![Aktivera övervakning för kluster som inte övervakas](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för lagring av övervakningsdata från klustret följer du instruktionerna i [Skapa en log analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md). Var noga med att skapa arbetsytan i samma prenumeration som RedHat OpenShift-klustret distribueras till.

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret.

### <a name="enable-using-an-azure-resource-manager-template"></a>Aktivera med hjälp av en Azure Resource Manager-mall

Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parametervärden som du konfigurerar för att ange följande:

- Azure RedHat OpenShift-klusterresurs-ID.

- Resursgruppen som klustret distribueras i.

- En Log Analytics-arbetsyta.

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall läser du:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.65 eller senare. Om du vill `az --version`identifiera din version kör du . Om du behöver installera eller uppgradera Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Log Analytics-arbetsytan måste skapas innan du aktiverar övervakning med Azure PowerShell eller CLI. Om du vill skapa arbetsytan kan du konfigurera den via [Azure Resource Manager,](../../azure-monitor/platform/template-workspace-configuration.md)via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md).

1. Hämta mall- och parameterfilen för att uppdatera klustret med övervakningstillägget med följande kommandon:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Logga in på Azure

    ```azurecli
    az login    
    ```

    Om du har åtkomst till `az account set -s {subscription ID}` flera `{subscription ID}` prenumerationer kör du ersättning med den prenumeration du vill använda.

3. Ange prenumerationen för Azure RedHat OpenShift-klustret.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Kör följande kommando för att identifiera klusterplatsen och resurs-ID:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Redigera JSON-parameterfilen **befintligaClusterParam.json** och uppdatera värdena *araResourceId* och *araResoruceLocation*. Värdet för **workspaceResourceId** är det fullständiga resurs-ID:n för din Log Analytics-arbetsyta, som innehåller arbetsytans namn.

6. Om du vill distribuera med Azure CLI kör du följande kommandon:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    Utdata liknar följande:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Nästa steg

- När övervakning är aktiverad för att samla in hälso- och resursanvändning för ditt RedHat OpenShift-kluster och arbetsbelastningar som körs på dem kan du lära dig [hur du använder](container-insights-analyze.md) Azure Monitor för behållare.

- Mer information om hur du slutar övervaka klustret med Azure Monitor för behållare finns i [Så här slutar du övervaka ditt Azure Red Hat OpenShift-kluster](container-insights-optout-openshift.md).
