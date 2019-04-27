---
title: Kör experiment och inferens i ett virtuellt nätverk
titleSuffix: Azure Machine Learning service
description: Köra machine learning-experiment och inferens skydda i ett Azure-nätverk. Lär dig hur du skapar beräkningsmål för modellträning och hur du inferens inom ett virtuellt nätverk. Läs om krav för skyddade virtuella nätverk, till exempel kräver inkommande och utgående portarna.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: a83661a63f784f62bf46ce75b8b4f47c57c87b19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819721"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Kör säkert experiment och inferensjobb i Azure-nätverk

I den här artikeln lär du dig att köra dina experiment och inferensjobb i ett virtuellt nätverk. Ett virtuellt nätverk fungerar som en säkerhetsgräns isolera dina Azure-resurser från det offentliga internet. Du kan även ansluta Azure-nätverk till ditt lokala nätverk. Det kan du träna dina modeller och få åtkomst till dina distribuerade modeller för inferensjobb på ett säkert sätt.

Azure Machine Learning-tjänsten är beroende av andra Azure-tjänster för compute-resurser. Beräkningsresurser (beräkningsmål) används för att träna och distribuera modeller. Dessa beräkningsalternativ mål kan skapas i ett virtuellt nätverk. Du kan till exempel använda Microsoft Data Science Virtual Machine för att träna en modell och distribuerar sedan modellen till Azure Kubernetes Service (AKS). Mer information om virtuella nätverk finns i den [översikt över Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="prerequisites"></a>Nödvändiga komponenter

Det här dokumentet förutsätter att du är bekant med virtuella Azure-nätverk och IP-nätverk i allmänhet. Det här dokumentet förutsätter också att du har skapat ett virtuellt nätverk och undernät som ska användas med dina beräkningsresurser. Om du inte är bekant med Azure Virtual Networks kan du läsa följande artiklar för att lära dig om tjänsten:

* [IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Snabbstart: Skapa ett virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Filtrera nätverkstrafik](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>Storage-konto för din arbetsyta

När du skapar en arbetsyta för Azure Machine Learning-tjänsten kräver ett Azure Storage-konto. Inte aktivera brandväggsregler för det här lagringskontot. Azure Machine Learning-tjänsten kräver obegränsad åtkomst till lagringskontot.

Om du är osäker på om du har ändrat dessa inställningar eller inte, se __ändra standardregel för åtkomst av nätverket__ i [konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security). Använd steg för att tillåta åtkomst från alla nätverk.

## <a name="use-machine-learning-compute"></a>Använda Machine Learning beräkning

Använd följande information om krav för att använda beräkning av Azure Machine Learning i ett virtuellt nätverk:

- Det virtuella nätverket måste finnas i samma prenumeration och region som arbetsytan Azure Machine Learning-tjänsten.

- Undernätet som anges för beräkning av Machine Learning-klustret måste ha tillräckligt med lediga IP-adresser för antalet virtuella datorer som mål för klustret. Om undernätet inte har tillräckligt med lediga IP-adresser kan tilldelas klustret delvis.

- Om du planerar att skydda det virtuella nätverket genom att begränsa trafik, lämnar du vissa portar öppna för beräkning av Machine Learning-tjänsten. Mer information finns i [krävs portar](#mlcports).

- Kontrollera om dina säkerhetsprinciper eller lås på det virtuella nätverket prenumeration eller resursgrupp begränsa behörigheter för att hantera det virtuella nätverket.

- Om du ska placera flera beräkning av Machine Learning-kluster i ett virtuellt nätverk, kan du behöva begära en kvot för en eller flera av dina resurser.

    Beräkning av Machine Learning tilldelar automatiskt ytterligare nätverksresurser i resursgruppen som innehåller det virtuella nätverket. För varje beräkning av Machine Learning-kluster tilldelar Azure Machine Learning-tjänsten i följande resurser:

    - En nätverkssäkerhetsgrupp (NSG)

    - En offentlig IP-adress

    - En belastningsutjämnare

  Dessa resurser begränsas av prenumerationens [resurskvoter](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a id="mlcports"></a> Portar som krävs

Beräkning av Machine Learning använder för närvarande Azure Batch-tjänsten kan etablera virtuella datorer på det angivna virtuella nätverket. Undernätet måste tillåta inkommande kommunikation från Batch-tjänsten. Den här kommunikationen används för att schemalägga körs på beräkning av Machine Learning-noder och för att kommunicera med Azure Storage och andra resurser. Batch lägger till Nätverkssäkerhetsgrupper på nivån för nätverksgränssnitt (NIC) som är kopplade till virtuella datorer. De här NSG:erna konfigurerar automatiskt regler för inkommande och utgående trafik för att tillåta följande trafik:

- Inkommande TCP-trafik på portarna 29876 och 29877 från en __Tjänsttagg__ av __BatchNodeManagement__.

    ![Bild av Azure-portalen som visar en inkommande regel med hjälp av tjänsttaggen BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- (valfritt) Inkommande TCP-trafik på port 22 för att tillåta fjärråtkomst. Detta behövs bara om du vill ansluta med SSH på den offentliga IP-Adressen.
 
- Utgående trafik på vilken port som helst till det virtuella nätverket.

- Utgående trafik på vilken port som helst till Internet.

Var försiktig om du ändrar eller lägga till inkommande/utgående regler i Batch-konfigurerade NSG: er. Om en NSG block kommunikation till beräkningsnoderna anger beräkning av Machine Learning-tjänsterna tillståndet för beräkningsnoderna till oanvändbar.

Du behöver inte ange NSG på undernätverksnivån i eftersom Batch konfigurerar sin egen NSG: er. Om det angivna undernätet har associerade NSG: er och/eller en brandvägg måste konfigurera inkommande och utgående säkerhetsregler som vi nämnde tidigare. Följande skärmbilder visar hur regelkonfigurationen ser ut i Azure portal:

![Skärmbild av inkommande NSG-regler för beräkning av Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Skärmbild av utgående NSG-regler för beräkning av Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Skapa en beräkning av Machine Learning i ett virtuellt nätverk

Följ dessa steg om du vill skapa ett kluster för beräkning av Machine Learning med hjälp av Azure portal:

1. I den [Azure-portalen](https://portal.azure.com), Välj din arbetsyta för Azure Machine Learning-tjänsten.

1. I den __programmet__ väljer __Compute__. Välj sedan __Lägg till compute__. 

    ![Hur du lägger till en beräkning i Azure Machine Learning-tjänsten](./media/how-to-enable-virtual-network/add-compute.png)

1. Använd de här alternativen om du vill konfigurera den här beräkningsresurs för att använda ett virtuellt nätverk:

    - __Nätverkskonfigurationen__: Välj __Avancerat__.

    - __Resursgrupp__: Välj den resursgrupp som innehåller det virtuella nätverket.

    - __Virtuellt nätverk__: Välj det virtuella nätverket som innehåller undernätet.

    - __Undernät__: Välj undernätet du använder.

   ![En skärmbild som visar inställningar för virtuella nätverk för machine learning-beräkning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Du kan också skapa ett kluster för beräkning av Machine Learning med hjälp av Azure Machine Learning-SDK. Följande kod skapar ett nytt beräkning av Machine Learning-kluster i den `default` undernät i ett virtuellt nätverk med namnet `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")
    
    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

När processen är klar kan du träna din modell med hjälp av klustret. Mer information finns i [Använd beräkningsmål för utbildning](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Använda en virtuell dator eller ett HDInsight-kluster

Om du vill använda en virtuell dator eller ett Azure HDInsight-kluster i ett virtuellt nätverk med din arbetsyta, Följ dessa steg:

> [!IMPORTANT]
> Azure Machine Learning-tjänsten stöder bara virtuella datorer som kör Ubuntu.

1. Skapa en virtuell dator eller HDInsight-kluster med hjälp av Azure-portalen eller Azure CLI och placera den i en Azure-nätverk. Mer information finns i följande dokument:
    * [Skapa och hantera virtuella Azure-nätverk för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Utöka HDInsight med hjälp av Azure-nätverk](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Om du vill att Azure Machine Learning-tjänsten kan kommunicera med SSH-porten på den virtuella datorn eller klustret, måste du konfigurera en käll-post för NSG: N. SSH-porten är vanligtvis port 22. Använd följande information för att tillåta trafik från den här källan:

    * __Källa__: Välj __Service Tag__ (Tjänsttagg).

    * __Källtjänsttagg__: Select __AzureMachineLearning__.

    * __Käll-portintervall__: Välj __*__.

    * __Mål__: Välj __alla__.

    * __Målportintervall__: Välj __22__.

    * __Protokoll__: Välj __alla__.

    * __Åtgärd__: Välj __Tillåt__.

   ![Skärmbild av regler för inkommande trafik för att göra experimentering på en virtuell dator eller HDInsight-kluster i ett virtuellt nätverk](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Behåll standardvärdet utgående regler för Nätverkssäkerhetsgruppen. Mer information finns i standardsäkerhetsregler i [säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
    
1. Koppla virtuell dator eller HDInsight-klustret till din arbetsyta för Azure Machine Learning-tjänsten. Mer information finns i [konfigurera beräkningsmål för modellträning](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service"></a>Använda Azure Kubernetes-tjänst

> [!IMPORTANT]
> Kontrollera krav och planera IP-adresser för ditt kluster innan du fortsätter med stegen. Mer information finns i [konfigurera avancerade nätverk i Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
> Behåll standardvärdet utgående regler för Nätverkssäkerhetsgruppen. Mer information finns i standardsäkerhetsregler i [säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Azure Kubernetes Service och Azure-nätverket bör finnas i samma region.

Följ dessa steg i Azure-portalen för att lägga till Azure Kubernetes Service i ett virtuellt nätverk till din arbetsyta:

1. I den [Azure-portalen](https://portal.azure.com), Välj din arbetsyta för Azure Machine Learning-tjänsten.

1. I den __programmet__ väljer __Compute__. Välj sedan __Lägg till compute__. 

    ![Hur du lägger till en beräkning i Azure Machine Learning-tjänsten](./media/how-to-enable-virtual-network/add-compute.png)

1. Använd de här alternativen om du vill konfigurera den här beräkningsresurs för att använda ett virtuellt nätverk:

    - __Nätverkskonfigurationen__: Välj __Avancerat__.

    - __Resursgrupp__: Välj den resursgrupp som innehåller det virtuella nätverket.

    - __Virtuellt nätverk__: Välj det virtuella nätverket som innehåller undernätet.

    - __Undernät__: Välj undernätet.

    - __Kubernetes Service-adressintervall__: Välj Kubernetes service-adressintervall. Den här adressintervall använder ett CIDR-notation IP-adressintervall för att definiera de IP-adresserna som är tillgängliga för klustret. Det får inte överlappa eventuella IP-intervall för undernät. Exempel: 10.0.0.0/16.

    - __IP-adress för Kubernetes DNS-tjänsten__: Välj IP-adress för Kubernetes DNS-tjänsten. Den här IP-adress tilldelas till Kubernetes DNS-tjänsten. Det måste finnas inom Kubernetes service-adressintervall. Exempel: 10.0.0.10.

    - __Docker bridge-adress__: Välj Docker bridge-adress. Den här IP-adress tilldelas till Docker Bridge. Det får inte vara i alla undernät IP-adressintervall eller Kubernetes service-adressintervall. Exempel: 172.17.0.1/16.

   ![Azure Machine Learning-tjänsten: Machine Learning Compute virtuella nätverksinställningar](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > Om du redan har ett AKS-kluster i ett virtuellt nätverk kan koppla du den till arbetsytan. Mer information finns i [hur du distribuerar till AKS](how-to-deploy-to-aks.md).

Du kan också använda den **Azure Machine Learning SDK** lägger du till Azure Kubernetes Service i ett virtuellt nätverk. Följande kod skapar en ny Azure Kubernetes Service-instans i den `default` undernät i ett virtuellt nätverk med namnet `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

När processen är slutförd kan göra du inferensjobb på ett AKS-kluster bakom ett virtuellt nätverk. Mer information finns i [hur du distribuerar till AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera miljöer för utbildning](how-to-set-up-training-targets.md)
* [Var du vill distribuera modeller](how-to-deploy-and-where.md)
* [På ett säkert sätt distribuera modeller med SSL](how-to-secure-web-service.md)

