---
title: Säkra experiment och härledning i virtuellt nätverk
titleSuffix: Azure Machine Learning
description: Lär dig att säkra experimentering/utbildnings jobb och jobb för jobbering och jobb för jobb utveckling i Azure Machine Learning i en Azure-Virtual Network.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 01/13/2020
ms.openlocfilehash: 6e5571604e6154408f2005ab4804b4270041e4cf
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444357"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Skydda Azure ML-experimentering och härlednings jobb i en Azure-Virtual Network
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig att säkra experimentering/utbildnings jobb och jobb för att lägga till jobb för jobbering i Azure Machine Learning i ett Azure-Virtual Network (VNet).

Ett **virtuellt nätverk** fungerar som en säkerhets gränser som isolerar dina Azure-resurser från det offentliga Internet. Du kan också ansluta ett virtuellt Azure-nätverk till ditt lokala nätverk. Genom att ansluta till nätverk kan du på ett säkert sätt träna dina modeller och komma åt dina distribuerade modeller för att få en mer härledning.

Azure Machine Learning använder andra Azure-tjänster för beräknings resurser. Beräknings resurser, eller [beräknings mål](concept-compute-target.md), används för att träna och distribuera modeller. Målen kan skapas i ett virtuellt nätverk. Du kan till exempel använda Microsoft Data Science Virtual Machine för att träna en modell och sedan distribuera modellen till Azure Kubernetes service (AKS). Mer information om virtuella nätverk finns i [Översikt över Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Den här artikeln innehåller även detaljerad information om *avancerade säkerhets inställningar*, information som inte behövs för grundläggande eller experiment användnings fall. Vissa delar av den här artikeln innehåller konfigurations information för olika scenarier. Du behöver inte slutföra instruktionerna i ordning eller i sin helhet.

> [!TIP]
> Om det inte uttryckligen kallas att använda resurser, till exempel lagrings konton eller beräknings mål i ett virtuellt nätverk, kommer de att fungera med både Machine Learning-pipeliner och icke-pipeline-arbetsflöden som skript körningar.

> [!WARNING]
> Microsoft stöder inte användning av Azure Machine Learning designer eller Automatisk maskin inlärning (från Studio) med resurser i ett virtuellt nätverk.

## <a name="prerequisites"></a>Förutsättningar

+ En Azure Machine Learning- [arbetsyta](how-to-manage-workspace.md).

+ Allmänt fungerande kunskap om både [Azure Virtual Network-tjänsten](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) och [IP-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Ett befintligt virtuellt nätverk och undernät som ska användas med dina beräknings resurser.

## <a name="use-a-storage-account-for-your-workspace"></a>Använd ett lagrings konto för din arbets yta

Använd följande steg för att använda ett Azure Storage-konto för arbets ytan i ett virtuellt nätverk:

1. Skapa en beräknings resurs (till exempel en Machine Learning beräknings instans eller kluster) bakom ett virtuellt nätverk eller Anslut en beräknings resurs till arbets ytan (till exempel ett HDInsight-kluster, en virtuell dator eller ett Azure Kubernetes service-kluster). Beräknings resursen kan vara till för experimentering eller modell distribution.

   Mer information finns i avsnittet [använda en Machine Learning Compute](#amlcompute), [använda en virtuell dator eller HDInsight-kluster](#vmorhdi)och [använda Azure Kubernetes service-](#aksvnet) avsnitten i den här artikeln.

1. I Azure Portal går du till den lagring som är kopplad till din arbets yta.

   [![lagrings utrymmet som är kopplat till arbets ytan Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. På sidan **Azure Storage** väljer du __brand väggar och virtuella nätverk__.

   ![Avsnittet "brand väggar och virtuella nätverk" på sidan Azure Storage i Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. På sidan __brand väggar och virtuella nätverk__ utför du följande åtgärder:
    - Välj __Valda nätverk__.
    - Under __virtuella nätverk__väljer du länken __Lägg till befintligt virtuellt nätverk__ . Den här åtgärden lägger till det virtuella nätverk där din beräkning finns (se steg 1).

        > [!IMPORTANT]
        > Lagrings kontot måste finnas i samma virtuella nätverk som de beräknings instanser eller-kluster som används för utbildning eller härledning.

    - Markera kryss rutan __Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot__ .

    > [!IMPORTANT]
    > När du arbetar med Azure Machine Learning SDK måste utvecklings miljön kunna ansluta till Azure Storage-kontot. När lagrings kontot finns i ett virtuellt nätverk måste brand väggen tillåta åtkomst från utvecklings miljöns IP-adress.
    >
    > Om du vill aktivera åtkomst till lagrings kontot går du till __brand väggarna och de virtuella nätverken__ för lagrings kontot *från en webbläsare på utvecklings klienten*. Använd sedan kryss rutan __Lägg till din klient-IP-adress__ för att lägga till KLIENTens IP-adress i __adress intervallet__. Du kan också använda fältet __adress intervall__ för att manuellt ange IP-adressen för utvecklings miljön. När IP-adressen för klienten har lagts till kan den komma åt lagrings kontot med hjälp av SDK.

   [![rutan "brand väggar och virtuella nätverk" i Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Du kan placera både _standard lagrings kontot_ för Azure Machine Learning eller _andra lagrings konton som inte är standard_ i ett virtuellt nätverk.
>
> Standard lagrings kontot tillhandahålls automatiskt när du skapar en arbets yta.
>
> För lagrings konton som inte är standard kan du ange ett anpassat lagrings konto per Azure-resurs-ID i `storage_account`-parametern i [`Workspace.create()`-funktionen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) .

## <a name="use-azure-data-lake-storage-gen-2"></a>Använd Azure Data Lake Storage gen 2

Azure Data Lake Storage gen 2 är en uppsättning funktioner för stor data analys som bygger på Azure Blob Storage. Den kan användas för att lagra data som används för att träna modeller med Azure Machine Learning. 

Använd följande steg för att använda Data Lake Storage gen 2 inuti det virtuella nätverket på arbets ytan Azure Machine Learning:

1. Skapa ett konto för Azure Data Lake Storage gen 2. Mer information finns i [skapa ett Azure Data Lake Storage Gen2 lagrings konto](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Använd steg 2-4 i föregående avsnitt och [Använd ett lagrings konto för din arbets yta för](#use-a-storage-account-for-your-workspace)att lagra kontot i det virtuella nätverket.

Använd följande vägledning när du använder Azure Machine Learning med Data Lake Storage gen 2 i ett virtuellt nätverk:

* Om du använder __SDK för att skapa en data uppsättning__och systemet som kör koden __inte finns i det virtuella nätverket__, använder du parametern `validate=False`. Den här parametern hoppar över verifiering, vilket Miss lyckas om systemet inte finns i samma virtuella nätverk som lagrings kontot. Mer information finns i metoden [from_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) .

* När du använder Azure Machine Learning beräknings instans eller beräknings kluster för att träna en modell med data uppsättningen måste den finnas i samma virtuella nätverk som lagrings kontot.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Använd en Key Vault-instans med din arbets yta

Nyckel valvs instansen som är kopplad till arbets ytan används av Azure Machine Learning för att lagra följande autentiseringsuppgifter:
* Den associerade anslutnings strängen för lagrings kontot
* Lösen ord till Azure Container databas instanser
* Anslutnings strängar till data lager

Använd följande steg för att använda Azure Machine Learning experiment funktioner med Azure Key Vault bakom ett virtuellt nätverk:

1. Gå till nyckel valvet som är kopplat till arbets ytan.

   [![nyckel valvet som är associerat med arbets ytan Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. På sidan **Key Vault** i det vänstra fönstret väljer du __brand väggar och virtuella nätverk__.

   ![Avsnittet "brand väggar och virtuella nätverk" i fönstret Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. På sidan __brand väggar och virtuella nätverk__ utför du följande åtgärder:
    - Under __Tillåt åtkomst från__väljer du __valda nätverk__.
    - Under __virtuella nätverk__väljer du __Lägg till befintliga virtuella nätverk__ för att lägga till det virtuella nätverk där din experiment beräkning finns.
    - Under __Tillåt att betrodda Microsoft-tjänster kringgår den här brand väggen väljer du__ __Ja__.

   [![avsnittet "brand väggar och virtuella nätverk" i Key Vaults fönstret](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="compute-instance"></a>Använd en Machine Learning-beräkning

Om du vill använda en Azure Machine Learning beräknings instans eller beräknings kluster i ett virtuellt nätverk måste följande nätverks krav uppfyllas:

> [!div class="checklist"]
> * Det virtuella nätverket måste finnas i samma prenumeration och region som Azure Machine Learning-arbetsytan.
> * Under nätet som anges för beräknings instansen eller klustret måste ha tillräckligt många otilldelade IP-adresser för att rymma antalet virtuella datorer som är riktade. Om under nätet inte har tillräckligt med otilldelade IP-adresser, tilldelas ett beräknings kluster delvis.
> * Kontrol lera om dina säkerhets principer eller lås på det virtuella nätverkets prenumeration eller resurs grupp begränsar behörigheter för hantering av det virtuella nätverket. Om du planerar att skydda det virtuella nätverket genom att begränsa trafiken lämnar du vissa portar öppna för beräknings tjänsten. Mer information finns i avsnittet [nödvändiga portar](#mlcports) .
> * Om du ska lagra flera beräknings instanser eller kluster i ett virtuellt nätverk kan du behöva begära en kvot ökning för en eller flera av dina resurser.
> * Om Azure Storage kontona för arbets ytan också är skyddade i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som Azure Machine Learning beräknings instans eller kluster. 

> [!TIP]
> Machine Learning beräknings instans eller kluster allokerar automatiskt ytterligare nätverks resurser i resurs gruppen som innehåller det virtuella nätverket. För varje beräknings instans eller kluster allokerar tjänsten följande resurser:
> 
> * En nätverks säkerhets grupp
> * En offentlig IP-adress
> * En belastningsutjämnare
> 
> Dessa resurser begränsas av prenumerationens [resurskvoter](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


### <a id="mlcports"></a>Portar som krävs

Machine Learning-beräkning använder för närvarande tjänsten Azure Batch för att etablera virtuella datorer i det angivna virtuella nätverket. Under nätet måste tillåta inkommande kommunikation från batch-tjänsten. Du använder den här kommunikationen för att schemalägga körningar på Machine Learning-beräkning noder och för att kommunicera med Azure Storage och andra resurser. Batch-tjänsten lägger till nätverks säkerhets grupper (NSG: er) på nivån nätverks gränssnitt (NIC) som är anslutna till virtuella datorer. De här NSG:erna konfigurerar automatiskt regler för inkommande och utgående trafik för att tillåta följande trafik:

- Inkommande TCP-trafik på portarna 29876 och 29877 från en __service tag__ i __BatchNodeManagement__.

    ![En regel för inkommande trafik som använder BatchNodeManagement-tjänst tag gen](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Valfritt Inkommande TCP-trafik på port 22 för att tillåta fjärråtkomst. Använd bara den här porten om du vill ansluta med SSH på den offentliga IP-adressen.

- Utgående trafik på vilken port som helst till det virtuella nätverket.

- Utgående trafik på vilken port som helst till Internet.

- För Compute instance inkommande TCP-trafik på port 44224 från en __service tag__ i __AzureMachineLearning__.

Var försiktig om du ändrar eller lägger till regler för inkommande eller utgående trafik i Batch-konfigurerade NSG:er. Om en NSG blockerar kommunikation till datornoderna, anger beräknings tjänsten status för datornoderna till oanvändbar.

Du behöver inte ange NSG: er på under näts nivån, eftersom Azure Batch tjänsten konfigurerar sin egen NSG: er. Men om det angivna under nätet har en associerad NSG: er eller en brand vägg, konfigurerar du de inkommande och utgående säkerhets reglerna enligt ovan.

Regel konfigurationen för NSG i Azure Portal visas i följande avbildningar:

[![regler för inkommande NSG för Machine Learning-beräkning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![Utgående NSG-regler för Machine Learning-beräkning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Begränsa utgående anslutning från det virtuella nätverket

Använd följande steg om du inte vill använda de utgående standard reglerna och du vill begränsa den utgående åtkomsten för ditt virtuella nätverk:

- Neka utgående Internet anslutning med NSG-reglerna.

- För en __beräknings instans__ eller ett __beräknings kluster__begränsar du utgående trafik till följande objekt:
   - Azure Storage med hjälp av __tjänst tag gen__ för __Storage. RegionName__. Där `{RegionName}` är namnet på en Azure-region.
   - Azure Container Registry med hjälp av __service tag gen__ för __AzureContainerRegistry. RegionName__. Där `{RegionName}` är namnet på en Azure-region.
   - Azure Machine Learning med hjälp av __service tag gen__ för __AzureMachineLearning__
   
- För en __beräknings instans__lägger du också till följande objekt:
   - Azure Resource Manager med hjälp av __service tag gen__ för __AzureResourceManager__
   - Azure Active Directory med hjälp av __service tag gen__ för __AzureActiveDirectory__

Regel konfigurationen för NSG i Azure Portal visas i följande bild:

[![utgående NSG-regler för Machine Learning-beräkning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Om du planerar att använda standard Docker-avbildningar som tillhandahålls av Microsoft och aktiverar hanterade beroenden, måste du också använda en __service tag__ - __MicrosoftContainerRegistry. Region_Name__ (till exempel MicrosoftContainerRegistry. öster).
>
> Den här konfigurationen krävs när du har kod som liknar följande kodfragment som en del av dina utbildnings skript:
>
> __RunConfig-utbildning__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Uppskattnings utbildning__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Användardefinierade vägar för Tvingad tunnel trafik

Om du använder Tvingad tunnel trafik med Machine Learning-beräkning lägger du till [användardefinierade vägar (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) i det undernät som innehåller beräknings resursen.

* Upprätta en UDR för varje IP-adress som används av tjänsten Azure Batch i den region där dina resurser finns. Dessa UDR gör att batch-tjänsten kan kommunicera med datornoder för schemaläggning av aktiviteter. Använd någon av följande metoder för att hämta en lista över IP-adresser för batch-tjänsten:

    * Hämta [Azure IP-intervall och service märken](https://www.microsoft.com/download/details.aspx?id=56519) och sök efter `BatchNodeManagement.<region>`i filen, där `<region>` är din Azure-region.

    * Använd [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att hämta informationen. I följande exempel hämtas IP-adress informationen och filtreras bort informationen för regionen USA, östra 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Utgående trafik till Azure Storage får inte blockeras av den lokala nätverks enheten. Mer specifikt är webb adresserna i formatet `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`och `<account>.blob.core.windows.net`.

När du lägger till UDR definierar du vägen för varje relaterat batch-IP-adressprefix och anger __nästa hopp typ__ till __Internet__. Följande bild visar ett exempel på den här UDR i Azure Portal:

![Exempel på en UDR för ett adressprefix](./media/how-to-enable-virtual-network/user-defined-route.png)

Mer information finns i [skapa en Azure Batch pool i ett virtuellt nätverk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Skapa ett beräknings kluster i ett virtuellt nätverk

Använd följande steg för att skapa ett Machine Learning-beräkning kluster:

1. I [Azure Portal](https://portal.azure.com)väljer du Azure Machine Learning arbets ytan.

1. Välj __Compute__i __program__ avsnittet och välj sedan __Lägg till beräkning__.

1. Gör så här för att konfigurera beräknings resursen för att använda ett virtuellt nätverk:

    a. För __nätverks konfiguration__väljer du __Avancerat__.

    b. I list rutan __resurs grupp__ väljer du den resurs grupp som innehåller det virtuella nätverket.

    c. I list rutan __virtuellt nätverk__ väljer du det virtuella nätverk som innehåller under nätet.

    d. I list rutan __undernät__ väljer du det undernät som ska användas.

   ![Inställningarna för virtuella nätverk för Machine Learning-beräkning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Du kan också skapa ett Machine Learning-beräkning-kluster med hjälp av Azure Machine Learning SDK. Följande kod skapar ett nytt Machine Learning-beräkning-kluster i `default` under nätet för ett virtuellt nätverk med namnet `mynetwork`:

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
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

När du har skapat processen tränar du din modell genom att använda klustret i ett experiment. Mer information finns i [Välj och använda ett beräknings mål för utbildning](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Använd Azure Databricks

Om du vill använda Azure Databricks i ett virtuellt nätverk med din arbets yta måste följande krav uppfyllas:

> [!div class="checklist"]
> * Det virtuella nätverket måste finnas i samma prenumeration och region som Azure Machine Learning-arbetsytan.
> * Om Azure Storage kontona för arbets ytan också är skyddade i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som Azure Databricks-klustret.
> * Förutom de __databricks-privata__ och __databricks-offentliga__ undernät som används av Azure Databricks, krävs även det __standard__ -undernät som skapats för det virtuella nätverket.

För detaljerad information om hur du använder Azure Databricks med ett virtuellt nätverk, se [distribuera Azure Databricks i Azure-Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Använda en virtuell dator eller HDInsight-kluster

> [!IMPORTANT]
> Azure Machine Learning stöder bara virtuella datorer som kör Ubuntu.

Använd följande steg om du vill använda en virtuell dator eller ett Azure HDInsight-kluster i ett virtuellt nätverk med din arbets yta:

1. Skapa ett virtuellt dator kluster eller HDInsight-kluster med hjälp av Azure Portal eller Azure CLI och Lägg klustret i ett virtuellt Azure-nätverk. Mer information finns i följande artiklar:
    * [Skapa och hantera virtuella Azure-nätverk för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Utöka HDInsight med ett virtuellt Azure-nätverk](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Om du vill tillåta Azure Machine Learning att kommunicera med SSH-porten på den virtuella datorn eller klustret konfigurerar du en käll post för nätverks säkerhets gruppen. SSH-porten är vanligt vis port 22. Utför följande åtgärder för att tillåta trafik från den här källan:

    * I list rutan __källa__ väljer du __service tag__.

    * I list rutan __käll tjänst tag__ väljer du __AzureMachineLearning__.

    * I list rutan __käll port intervall__ väljer du __*__ .

    * I list rutan __mål__ väljer du __valfri__.

    * I list rutan __mål Port intervall__ väljer du __22__.

    * Under __protokoll__väljer du __valfri__.

    * Under __åtgärd__väljer du __Tillåt__.

   ![Regler för inkommande trafik för att utföra experimentering i ett virtuellt nätverk eller HDInsight-kluster i ett virtuellt nätverk](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Behåll standard reglerna för utgående trafik för nätverks säkerhets gruppen. Mer information finns i standard säkerhets regler i [säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Om du inte vill använda de utgående standard reglerna och du vill begränsa den utgående åtkomsten för ditt virtuella nätverk kan du läsa avsnittet [begränsa utgående anslutningar från det virtuella nätverket](#limiting-outbound-from-vnet) .

1. Anslut den virtuella datorn eller HDInsight-klustret till din Azure Machine Learning-arbetsyta. Mer information finns i [Konfigurera beräknings mål för modell träning](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Använda Azure Kubernetes service (AKS)

Använd följande steg för att lägga till AKS i ett virtuellt nätverk till din arbets yta:

> [!IMPORTANT]
> Innan du påbörjar följande procedur följer du kraven i avsnittet [Konfigurera avancerade nätverksfunktioner i Azure Kubernetes service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) How-to och planera IP-adressering för klustret.
>
> AKS-instansen och det virtuella Azure-nätverket måste finnas i samma region. Om du skyddar de Azure Storage-konton som används av arbets ytan i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som AKS-instansen.

1. I [Azure Portal](https://portal.azure.com)ser du till att den NSG som styr det virtuella nätverket har en regel för inkommande trafik som är aktive rad för Azure Machine Learning med __AzureMachineLearning__ som **källa**.

    [![Azure Machine Learning Lägg till beräknings fönster](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png#lightbox)

1. Välj din Azure Machine Learning-arbetsyta.

1. Välj __Compute__i __program__ avsnittet och välj sedan __Lägg till beräkning__.

1. Gör så här för att konfigurera beräknings resursen för att använda ett virtuellt nätverk:

    - För __nätverks konfiguration__väljer du __Avancerat__.

    - I list rutan __resurs grupp__ väljer du den resurs grupp som innehåller det virtuella nätverket.

    - I list rutan __virtuellt nätverk__ väljer du det virtuella nätverk som innehåller under nätet.

    - I list rutan __undernät__ väljer du under nätet.

    - I rutan __Kubernetes service-adressintervall__ anger du tjänst adress intervallet för Kubernetes. Det här adress intervallet använder en klass lös CIDR-notation (Inter-Domain routing) för att definiera de IP-adresser som är tillgängliga för klustret. Det får inte överlappa några IP-adressintervall för undernät (till exempel 10.0.0.0/16).

    - I rutan __IP-adress för KUBERNETES DNS-tjänst__ anger du IP-adressen för DNS-tjänsten Kubernetes. Den här IP-adressen är tilldelad DNS-tjänsten Kubernetes. Det måste ligga inom Kubernetes-tjänstens adress intervall (till exempel 10.0.0.10).

    - I rutan __Docker Bridge-adress__ anger du Docker-bryggans adress. Den här IP-adressen är tilldelad Docker-bryggan. Det får inte finnas i några IP-adressintervall för undernät eller Kubernetes-tjänstens adress intervall (till exempel 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning-beräkning inställningar för virtuellt nätverk](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Kontrol lera att NSG-gruppen som styr det virtuella nätverket har en inkommande säkerhets regel aktive rad för poäng slut punkten så att den kan anropas utanför det virtuella nätverket.
   > [!IMPORTANT]
   > Behåll de utgående standard reglerna för NSG. Mer information finns i standard säkerhets regler i [säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![en inkommande säkerhets regel](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Du kan också använda Azure Machine Learning SDK för att lägga till Azure Kubernetes-tjänsten i ett virtuellt nätverk. Om du redan har ett AKS-kluster i ett virtuellt nätverk ansluter du det till arbets ytan enligt beskrivningen i [så här distribuerar du till AKS](how-to-deploy-and-where.md). Följande kod skapar en ny AKS-instans i `default` under nätet för ett virtuellt nätverk med namnet `mynetwork`:

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
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

När processen har skapats kan du köra en härledning eller modell bedömning i ett AKS-kluster bakom ett virtuellt nätverk. Mer information finns i [så här distribuerar du till AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Använda privata IP-adresser med Azure Kubernetes-tjänsten

Som standard tilldelas en offentlig IP-adress till AKS-distributioner. När du använder AKS i ett virtuellt nätverk kan du använda en privat IP-adress i stället. Privata IP-adresser är bara tillgängliga inifrån det virtuella nätverket eller anslutna nätverk.

En privat IP-adress är aktive rad genom att konfigurera AKS för att använda en _intern belastningsutjämnare_. 

> [!IMPORTANT]
> Du kan inte aktivera privat IP när du skapar Azure Kubernetes service-klustret. Den måste vara aktive rad som en uppdatering av ett befintligt kluster.

Följande kodfragment visar hur du **skapar ett nytt AKS-kluster**och sedan uppdaterar det så att det använder en privat IP/intern belastningsutjämnare:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = "default"
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = “myaks”, provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", "default")
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

Innehållet i den `body.json`-fil som kommandot refererar till liknar följande JSON-dokument:

```json
{ 
    "location": “<region>”, 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> För närvarande kan du inte konfigurera belastningsutjämnaren när du utför en __kopplings__ åtgärd i ett befintligt kluster. Du måste först ansluta klustret och sedan utföra en uppdaterings åtgärd för att ändra belastningsutjämnaren.

Mer information om hur du använder den interna belastningsutjämnaren med AKS finns i [använda intern belastningsutjämnare med Azure Kubernetes service](/azure/aks/internal-lb).

## <a name="use-azure-firewall"></a>Använd Azure-brandvägg

När du använder Azure-brandväggen måste du konfigurera en nätverks regel för att tillåta trafik till och från följande adresser:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

När du lägger till regeln ställer du in __protokollet__ på valfri, och portarna som ska `*`.

Mer information om hur du konfigurerar en nätverks regel finns i [distribuera och konfigurera Azure-brandväggen](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="use-azure-container-registry"></a>Använda Azure Container Registry

När du använder ett virtuellt nätverk med Azure Machine Learning ska du __inte__ flytta Azure Container Registry för arbets ytan i det virtuella nätverket. Konfigurationen stöds inte.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera utbildnings miljöer](how-to-set-up-training-targets.md)
* [Distribuera modeller](how-to-deploy-and-where.md)
* [Distribuera modeller på ett säkert sätt med SSL](how-to-secure-web-service.md)
