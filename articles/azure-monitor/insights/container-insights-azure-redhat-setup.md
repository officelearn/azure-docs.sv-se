---
title: Konfigurera Azure Red Hat OpenShift-kluster med Azure Monitor för behållare | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera Azure Monitor för behållare för att övervaka Kubernetes-kluster som finns i Azure Red Hat OpenShift.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 26477eeb00fe7616a8d2f2be343e586042c0d130
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279672"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Konfigurera Azure Red Hat OpenShift-kluster med Azure Monitor för behållare

Azure Monitor for containers innehåller omfattande övervaknings funktioner för AKS-och AKS-motorns kluster. Den här artikeln beskriver hur du aktiverar övervakning av Kubernetes-kluster som finns i [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) för att uppnå en liknande övervaknings upplevelse.

>[!NOTE]
>Stöd för Red Hat OpenShift är en funktion i offentlig för hands version för tillfället.
>

Azure Monitor för behållare kan aktive ras för nya eller en eller flera befintliga distributioner av Azure Red Hat OpenShift med följande metoder som stöds:

- För ett befintligt kluster från Azure Portal eller genom att använda Azure Resource Manager mall
- För ett nytt kluster med Azure Resource Manager mall 

## <a name="supported-and-unsupported-features"></a>Funktioner som stöds och som inte stöds

Azure Monitor for containers stöder övervakning av Azure Red Hat OpenShift enligt beskrivningen i [översikts](container-insights-overview.md) artikeln, förutom följande funktioner:

- Real tids data
- Prometheus mått
- Samla in mått från klusternoder och poddar och skriva dem till lagrings platsen Azure Monitor mått
- Hälso funktion

## <a name="prerequisites"></a>Krav

- Om du vill aktivera och komma åt funktionerna i Azure Monitor för behållare måste du minst vara medlem i Azure *Contributor* -rollen i Azure-prenumerationen och en medlem i rollen [*Log Analytics Contributor*](../platform/manage-access.md#manage-access-using-azure-permissions) för den Log Analytics arbets yta som kon figurer ATS med Azure Monitor för behållare.

- Om du vill visa övervaknings data är du medlem i behörighets rollen [*Log Analytics läsare*](../platform/manage-access.md#manage-access-using-azure-permissions) med den Log Analytics arbets yta som kon figurer ats med Azure Monitor för behållare.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Aktivera för ett nytt kluster med en Azure Resource Manager mall

Utför följande steg för att distribuera ett Azure Red Hat OpenShift-kluster med övervakning aktiverat. Innan du fortsätter bör du gå igenom självstudien [skapa ett Azure Red Hat OpenShift-kluster](../../openshift/tutorial-create-cluster.md#prerequisites) för att förstå de beroenden som du behöver konfigurera så att din miljö är korrekt konfigurerad.

Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att distribuera klustret med övervakning aktiverat, och det andra innehåller parameter värden som du konfigurerar för att ange följande:

- Resurs-ID för Azure Red Hat OpenShift-klustret. 

- Resurs gruppen som klustret har distribuerats i.

- [Azure Active Directory klient-ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) anges efter att du har utfört stegen för att skapa en eller ett redan skapat.

- [Azure Active Directory klient program-ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) anges efter att du har utfört stegen för att skapa en eller ett redan skapat.

- [Azure Active Directory klient hemligheten](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) som du angav när du utförde stegen för att skapa en eller en redan har skapats.

- [Azure AD-säkerhetsgruppen](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) har antecknats efter att ha utfört stegen för att skapa en eller en redan skapad.

- Resurs-ID för en befintlig Log Analytics-arbetsyta.

- Antalet huvud noder som ska skapas i klustret.

- Antalet Compute-noder i agentens profil för poolen.

- Antalet infrastruktur noder i agentens profil för poolen. 

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.65 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Log Analytics arbets ytan måste skapas innan du aktiverar övervakning med Azure PowerShell eller CLI. För att skapa arbetsytan, du kan konfigurera det via [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), eller i den [Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md).

1. Hämta och spara till en lokal mapp, Azure Resource Manager mall och parameter fil, för att skapa ett kluster med övervaknings tillägget med följande kommandon:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json` 

2. Logga in på Azure 

    ```azurecli
    az login    
    ```
    
    Om du har åtkomst till flera prenumerationer kör du `az account set -s {subscription ID}` ersätter `{subscription ID}` med den prenumeration som du vill använda.
 
3. Skapa en resurs grupp för klustret om du inte redan har en. En lista över Azure-regioner som stöder OpenShift på Azure finns i [regioner som stöds](../../openshift/supported-resources.md#azure-regions). 

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location> 
    ```

4. Redigera JSON-parameter filen **newClusterWithMonitoringParam. JSON** och uppdatera följande värden:

    - *Plats*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret* 
    - *aadCustomerAdminGroupId* 
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. I följande steg distribueras klustret med övervakning aktiverat med hjälp av Azure CLI. 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json 
    ```
 
    Resultatet ser ut ungefär så här:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Aktivera för ett befintligt kluster

Utför följande steg för att aktivera övervakning av ett Azure Red Hat OpenShift-kluster som distribuerats i Azure. Du kan göra detta från Azure Portal eller använda de angivna mallarna.

### <a name="from-the-azure-portal"></a>Från Azure Portal
 
1. Logga in på [Azure-portalen](https://portal.azure.com).

2. På Azure Portal-menyn eller på Start sidan väljer du **Azure Monitor**. Under den **Insights** väljer **behållare**. 

3. På den **skärm – behållare** väljer **icke-övervakas kluster**.

4. I listan över icke-övervakade kluster letar du reda på klustret i listan och klickar på **Aktivera**. Du kan identifiera resultaten i listan genom att leta efter värdet **Aro** under kolumn **kluster typen**.

5. På den **Kom igång med Azure Monitor för behållare** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration som klustret, markerar du den i den nedrullningsbara listan.  
    I listan förväljs standard arbets ytan och platsen som klustret distribueras till i prenumerationen. 

    ![Aktivera övervakning för icke-övervakade kluster](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från klustret, följer du anvisningarna i [skapa en Log Analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md). Se till att skapa arbets ytan i samma prenumeration som RedHat OpenShift-klustret distribueras till. 
 
När du har aktiverat övervakning, kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Aktivera med hjälp av en Azure Resource Manager mall

Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parametervärden som du konfigurerar för att ange följande:

- Resurs-ID för Azure RedHat OpenShift-klustret. 

- Resurs gruppen som klustret har distribuerats i.

- En Log Analytics-arbetsyta.

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.65 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Log Analytics arbets ytan måste skapas innan du aktiverar övervakning med Azure PowerShell eller CLI. För att skapa arbetsytan, du kan konfigurera det via [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), eller i den [Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md).

1. Hämta mallen och parameter filen för att uppdatera klustret med övervaknings tillägget med följande kommandon:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json` 

2. Logga in på Azure 

    ```azurecli
    az login    
    ```

    Om du har åtkomst till flera prenumerationer kör du `az account set -s {subscription ID}` ersätter `{subscription ID}` med den prenumeration som du vill använda.

3. Ange prenumerationen för ett OpenShift-kluster i Azure RedHat.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Kör följande kommando för att identifiera kluster platsen och resurs-ID:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName> 
    ```

5. Redigera JSON-parameter filen **existingClusterParam. JSON** och uppdatera värdena *araResourceId* och *araResoruceLocation*. Värdet för **workspaceResourceId** är fullständiga resurs-ID för Log Analytics-arbetsytan, som innehåller namnet på arbetsytan. 

6. Om du vill distribuera med Azure CLI kör du följande kommandon: 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json 
    ```

    Resultatet ser ut ungefär så här:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Nästa steg

När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för ditt RedHat OpenShift-kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) Azure Monitor för behållare.