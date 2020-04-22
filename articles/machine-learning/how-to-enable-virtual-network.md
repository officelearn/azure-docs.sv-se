---
title: Säkra experiment och slutsatser i virtuellt nätverk
titleSuffix: Azure Machine Learning
description: lär dig hur du skyddar experiment-/utbildningsjobb och inferens-/bedömningsjobb i Azure Machine Learning i ett virtuellt Azure-nätverk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/17/2020
ms.openlocfilehash: f94136ca6bfcb7e33415f2f44fdf4c44ef9f6a6f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682785"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Skydda Azure ML-experiment och slutledningsjobb i ett virtuellt Azure-nätverk
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skyddar experiment-/utbildningsjobb och inferens-/bedömningsjobb i Azure Machine Learning i ett virtuellt Azure-nätverk (vnet).

Ett **virtuellt nätverk** fungerar som en säkerhetsgräns och isolerar dina Azure-resurser från det offentliga internet. Du kan också ansluta till ett virtuellt Azure-nätverk till ditt lokala nätverk. Genom att gå med i nätverk kan du träna dina modeller på ett säkert sätt och komma åt dina distribuerade modeller för slutledning.

Azure Machine Learning är beroende av andra Azure-tjänster för beräkningsresurser. Beräkningsresurser, eller [beräkningsmål,](concept-compute-target.md)används för att träna och distribuera modeller. Målen kan skapas i ett virtuellt nätverk. Du kan till exempel använda Microsoft Data Science Virtual Machine för att träna en modell och sedan distribuera modellen till Azure Kubernetes Service (AKS). Mer information om virtuella nätverk finns i [Översikt över Virtuella Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Den här artikeln innehåller också detaljerad information om *avancerade säkerhetsinställningar*, information som inte är nödvändig för grundläggande eller experimentella användningsfall. Vissa avsnitt i den här artikeln innehåller konfigurationsinformation för en mängd olika scenarier. Du behöver inte slutföra instruktionerna i ordning eller i sin helhet.

> [!TIP]
> Om inte specifikt ansöks fungerar resurser som lagringskonton eller beräkningsmål i ett virtuellt nätverk med både machine learning-pipelines och arbetsflöden som inte är pipeline-pipeline, till exempel skriptkörningar.

> [!WARNING]
> Microsoft stöder inte användning av Azure Machine Learning Studio-funktioner som Automated ML, Datasets, Datalabeling, Designer och Notebooks om det underliggande lagringsutrymmet har aktiverat virtuellt nätverk.

## <a name="prerequisites"></a>Krav

+ En Azure Machine [Learning-arbetsyta](how-to-manage-workspace.md).

+ Allmän arbetskunskap om både [Azure Virtual Network-tjänsten](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) och [IP-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Ett befintligt virtuellt nätverk och ett befintligt virtuellt nätverk och ett befintligt befintligt virtuellt nätverk som ska användas med beräkningsresurserna.

## <a name="use-a-storage-account-for-your-workspace"></a>Använda ett lagringskonto för din arbetsyta

> [!WARNING]
> Om du har datavetare som använder Azure Machine Learning-designern visas ett felmeddelande när data visualiseras från ett lagringskonto i ett virtuellt nätverk. Följande text är det fel som de får:
>
> __Fel: Det gick inte att profilera den här datauppsättningen. Det kan bero på att dina data lagras bakom ett virtuellt nätverk eller att dina data inte stöder profilen.__

Så här använder du ett Azure-lagringskonto för arbetsytan i ett virtuellt nätverk:

1. Skapa en beräkningsresurs (till exempel en Machine Learning-beräkningsinstans eller ett kluster) bakom ett virtuellt nätverk, eller koppla en beräkningsresurs till arbetsytan (till exempel ett HDInsight-kluster, en virtuell dator eller Azure Kubernetes-tjänstkluster). Beräkningsresursen kan vara för experiment eller modelldistribution.

   Mer information finns i avsnitten [Använda en machine learning-beräkning](#amlcompute), [Använd en virtuell dator eller HDInsight-kluster](#vmorhdi)och [Använd Azure Kubernetes-tjänstavsnitt](#aksvnet) i den här artikeln.

1. Gå till lagringen som är kopplad till arbetsytan i Azure-portalen.

   [![Lagringen som är kopplad till arbetsytan Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. På sidan **Azure Storage** väljer du __Brandväggar och virtuella nätverk__.

   ![Området "Brandväggar och virtuella nätverk" på sidan Azure Storage i Azure-portalen](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Gör följande på sidan __Brandväggar och virtuella nätverk:__
    - Välj __Valda nätverk__.
    - Under __Virtuella nätverk__väljer du länken Lägg till __befintligt virtuellt nätverk.__ Den här åtgärden lägger till det virtuella nätverket där beräkningen finns (se steg 1).

        > [!IMPORTANT]
        > Lagringskontot måste finnas i samma virtuella nätverk och undernät som beräkningsinstanserna eller kluster som används för utbildning eller slutledning.

    - Markera kryssrutan __Tillåt betrodda Microsoft-tjänster för att komma åt det här lagringskontot.__

    > [!IMPORTANT]
    > När du arbetar med Azure Machine Learning SDK måste din utvecklingsmiljö kunna ansluta till Azure Storage-kontot. När lagringskontot finns i ett virtuellt nätverk måste brandväggen tillåta åtkomst från utvecklingsmiljöns IP-adress.
    >
    > Om du vill aktivera åtkomst till lagringskontot besöker du __brandväggar och virtuella nätverk__ för lagringskontot *från en webbläsare på utvecklingsklienten*. Använd sedan kryssrutan __Lägg till klientens IP-adress__ för att lägga till klientens IP-adress i __ADRESSINTERVALLET__. Du kan också använda fältet __ADRESSINTERVALL__ för att manuellt ange IP-adressen för utvecklingsmiljön. När IP-adressen för klienten har lagts till kan den komma åt lagringskontot med hjälp av SDK.

   [![Fönstret "Brandväggar och virtuella nätverk" i Azure-portalen](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Du kan placera både _standardlagringskontot_ för Azure Machine Learning eller _icke-standardlagringskonton_ i ett virtuellt nätverk.
>
> Standardlagringskontot etableras automatiskt när du skapar en arbetsyta.
>
> För lagringskonton som `storage_account` inte är standard kan du ange ett anpassat lagringskonto med Azure-resurs-ID med parametern i [ `Workspace.create()` funktionen.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)

## <a name="use-azure-data-lake-storage-gen-2"></a>Använda Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen 2 är en uppsättning funktioner för stordataanalys, byggd på Azure Blob-lagring. Den kan användas för att lagra data som används för att träna modeller med Azure Machine Learning. 

Så här använder du Data Lake Storage Gen 2 i det virtuella nätverket på arbetsytan Azure Machine Learning:

1. Skapa ett Azure Data Lake Storage gen 2-konto. Mer information finns i [Skapa ett Azure Data Lake Storage Gen2-lagringskonto](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Följ stegen 2-4 i föregående avsnitt, [Använd ett lagringskonto för din arbetsyta](#use-a-storage-account-for-your-workspace)för att placera kontot i det virtuella nätverket.

När du använder Azure Machine Learning med Data Lake Storage Gen 2 i ett virtuellt nätverk använder du följande vägledning:

* Om du använder __SDK för att skapa en datauppsättning__och systemet som kör `validate=False` koden inte finns i det virtuella __nätverket__använder du parametern. Den här parametern hoppar över valideringen, vilket misslyckas om systemet inte finns i samma virtuella nätverk som lagringskontot. Mer information finns i metoden [from_files().](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)

* När du använder Azure Machine Learning Compute Instance eller beräkningskluster för att träna en modell med hjälp av datauppsättningen måste den finnas i samma virtuella nätverk som lagringskontot.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Använda en nyckelvalvsinstans med arbetsytan

Nyckelvalvsinstansen som är associerad med arbetsytan används av Azure Machine Learning för att lagra följande autentiseringsuppgifter:
* Anslutningssträngen för associerat lagringskonto
* Lösenord till Azure Container Repository-instanser
* Anslutningssträngar till datalager

Så här använder du azure machine learning-experiment med Azure Key Vault bakom ett virtuellt nätverk:

1. Gå till nyckelvalvet som är associerat med arbetsytan.

   [![Nyckelvalvet som är associerat med arbetsytan Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Välj __Brandväggar och virtuella nätverk__i den vänstra rutan på sidan **Nyckelvalv** .

   ![Avsnittet "Brandväggar och virtuella nätverk" i fönstret Nyckelvalv](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Gör följande på sidan __Brandväggar och virtuella nätverk:__
    - Under __Tillåt åtkomst från__väljer du Markerade __nätverk__.
    - Under __Virtuella nätverk__väljer du Lägg till befintliga virtuella __nätverk__ för att lägga till det virtuella nätverket där experimentberäkningen finns.
    - Under __Tillåt betrodda Microsoft-tjänster att kringgå den här brandväggen__väljer du __Ja__.

   [![Avsnittet "Brandväggar och virtuella nätverk" i fönstret Nyckelvalv](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="use-a-machine-learning-compute"></a><a name="compute-instance"></a>Använda en machine learning-beräkning

Om du vill använda en Azure Machine Learning-beräkningsinstans eller beräkningskluster i ett virtuellt nätverk måste följande nätverkskrav uppfyllas:

> [!div class="checklist"]
> * Det virtuella nätverket måste finnas i samma prenumeration och region som arbetsytan Azure Machine Learning.
> * Undernätet som har angetts för beräkningsinstansen eller klustret måste ha tillräckligt med otilldelade IP-adresser för att rymma antalet virtuella datorer som är riktade. Om undernätet inte har tillräckligt med otilldelade IP-adresser, kommer ett beräkningskluster delvis att allokeras.
> * Kontrollera om dina säkerhetsprinciper eller låsningar i det virtuella nätverkets prenumeration eller resursgrupp begränsar behörigheterna för att hantera det virtuella nätverket. Om du planerar att skydda det virtuella nätverket genom att begränsa trafiken lämnar du vissa portar öppna för beräkningstjänsten. Mer information finns i avsnittet [Obligatoriska portar.](#mlcports)
> * Om du ska placera flera beräkningsinstanser eller kluster i ett virtuellt nätverk kan du behöva begära en kvotökning för en eller flera av dina resurser.
> * Om Azure Storage-konton för arbetsytan också är skyddade i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som Azure Machine Learning-beräkningsinstansen eller klustret. 

> [!TIP]
> Beräkningsinstansen för Maskininlärning eller -klustret allokerar automatiskt ytterligare nätverksresurser __i resursgruppen som innehåller det virtuella nätverket__. För varje beräkningsinstans eller kluster allokerar tjänsten följande resurser:
> 
> * En nätverkssäkerhetsgrupp
> * En offentlig IP-adress
> * En belastningsutjämnare
> 
> När det gäller kluster dessa resurser tas bort (och återskapas) varje gång klustret skalas ner till 0 noder, men för en instans resurserna hålls på tills instansen är helt borttagen (stoppa inte ta bort resurserna). 
> Dessa resurser begränsas av prenumerationens [resurskvoter](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


### <a name="required-ports"></a><a id="mlcports"></a>Obligatoriska portar

Machine Learning Compute använder för närvarande Azure Batch-tjänsten för att etablera virtuella datorer i det angivna virtuella nätverket. Undernätet måste tillåta inkommande kommunikation från batch-tjänsten. Du använder den här kommunikationen för att schemalägga körningar på Machine Learning Compute-noderna och för att kommunicera med Azure Storage och andra resurser. Batch-tjänsten lägger till nätverkssäkerhetsgrupper (NSG) på nivån för nätverksgränssnitt (NAC) som är kopplade till virtuella datorer. De här NSG:erna konfigurerar automatiskt regler för inkommande och utgående trafik för att tillåta följande trafik:

- Inkommande TCP-trafik på portarna 29876 och 29877 från ett __servicemärke för__ __BatchNodeManagement__.

    ![En inkommande regel som använder servicetaggen BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Valfritt) Inkommande TCP-trafik på port 22 för att tillåta fjärråtkomst. Använd endast den här porten om du vill ansluta med hjälp av SSH på den offentliga IP-adressen.

- Utgående trafik på vilken port som helst till det virtuella nätverket.

- Utgående trafik på vilken port som helst till Internet.

- För beräkningsinstans inkommande TCP-trafik på port 44224 från ett __servicetag__ för __AzureMachineLearning__.

Var försiktig om du ändrar eller lägger till regler för inkommande eller utgående trafik i Batch-konfigurerade NSG:er. Om en NSG blockerar kommunikation till beräkningsnoderna anger beräkningstjänsten tillståndet för beräkningsnoderna till obrukbara.

Du behöver inte ange NSG:er på undernätsnivå, eftersom Azure Batch-tjänsten konfigurerar sina egna NSG:er. Om det angivna undernätet har associerade NSGs eller en brandvägg konfigurerar du dock de inkommande och utgående säkerhetsreglerna som tidigare nämnts.

NSG-regelkonfigurationen i Azure-portalen visas i följande avbildningar:

[![De inkommande NSG-reglerna för Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![De utgående NSG-reglerna för Machine Learning Compute](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>Begränsa utgående anslutning från det virtuella nätverket

Om du inte vill använda standardreglerna för utgående och vill begränsa den utgående åtkomsten för det virtuella nätverket gör du så här:

- Neka utgående internetanslutning med hjälp av NSG-reglerna.

- För en __beräkningsinstans__ eller ett __beräkningskluster__begränsar du utgående trafik till följande objekt:
   - Azure Storage, med hjälp av __Service Tag__ of __Storage.RegionName__. Var `{RegionName}` är namnet på en Azure-region.
   - Azure Container Registry, med hjälp av __Service Tag__ i __AzureContainerRegistry.RegionName__. Var `{RegionName}` är namnet på en Azure-region.
   - Azure Machine Learning, med hjälp av __Service Tag__ i __AzureMachineLearning__
   - Azure Resource Manager, med hjälp av __Service Tag__ i __AzureResourceManager__
   - Azure Active Directory, med hjälp av __Service Tag__ of __AzureActiveDirectory__

NSG-regelkonfigurationen i Azure-portalen visas i följande avbildning:

[![De utgående NSG-reglerna för Machine Learning Compute](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Om du planerar att använda standardavbildningar från Docker från Microsoft och aktiverar användarhanterade beroenden måste du också använda en __Service Tag of__ __MicrosoftContainerRegistry.Region_Name__ (till exempel MicrosoftContainerRegistry.EastUS).
>
> Den här konfigurationen behövs när du har kod som liknar följande utdrag som en del av dina utbildningsskript:
>
> __RunConfig utbildning__
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
> __Utbildning i skattningar__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Användardefinierade rutter för påtvingad tunnelning

Om du använder tvångstunneler med Machine Learning Compute lägger du till [användardefinierade vägar (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) i undernätet som innehåller beräkningsresursen.

* Upprätta en UDR för varje IP-adress som används av Azure Batch-tjänsten i den region där dina resurser finns. Dessa UDR-enheter gör det möjligt för batch-tjänsten att kommunicera med beräkningsnoder för schemaläggning av aktiviteter. Om du vill hämta en lista över IP-adresser för batch-tjänsten använder du någon av följande metoder:

    * Ladda ned [Azure IP Ranges och Service Tags](https://www.microsoft.com/download/details.aspx?id=56519) och sök i filen efter `BatchNodeManagement.<region>`, där `<region>` är din Azure-region.

    * Använd [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att hämta informationen. I följande exempel hämtas IP-adressinformationen och informationen för regionen Östra USA 2 filtreras bort:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Utgående trafik till Azure Storage får inte blockeras av den lokala nätverksinstallationen. Webbadresserna finns i formuläret `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`och `<account>.blob.core.windows.net`.

När du lägger till UDR:erna definierar du vägen för varje relaterat batch-IP-adressprefix och anger __Nästa hopptyp__ på __Internet__. Följande bild visar ett exempel på den här UDR-datatjänsten i Azure-portalen:

![Exempel på en UDR för ett adressprefix](./media/how-to-enable-virtual-network/user-defined-route.png)

Mer information finns i [Skapa en Azure Batch-pool i ett virtuellt nätverk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Skapa ett beräkningskluster i ett virtuellt nätverk

Så här skapar du ett Machine Learning Compute-kluster:

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com/)och välj sedan din prenumeration och arbetsyta.

1. Välj __Beräkna__ till vänster.

1. Välj __Utbildningskluster__ från mitten __+__ och välj sedan .

1. Expandera avsnittet __Avancerade inställningar__ i dialogrutan __Nytt träningskluster.__

1. Om du vill konfigurera den här beräkningsresursen så att den använder ett virtuellt nätverk utför du följande åtgärder i avsnittet __Konfigurera virtuellt nätverk:__

    1. I listrutan __Resursgrupp__ väljer du den resursgrupp som innehåller det virtuella nätverket.
    1. I listrutan __Virtuellt nätverk__ väljer du det virtuella nätverk som innehåller undernätet.
    1. Markera det undernät som ska användas i listrutan __Undernät.__

   ![De virtuella nätverksinställningarna för Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Du kan också skapa ett Machine Learning Compute-kluster med hjälp av Azure Machine Learning SDK. Följande kod skapar ett nytt Machine Learning `default` Compute-kluster i `mynetwork`undernätet i ett virtuellt nätverk med namnet :

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

När skapandeprocessen är klar tränar du din modell med hjälp av klustret i ett experiment. Mer information finns i [Välj och använda ett beräkningsmål för utbildning](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Använda Azure Databricks

Om du vill använda Azure Databricks i ett virtuellt nätverk med din arbetsyta måste följande krav uppfyllas:

> [!div class="checklist"]
> * Det virtuella nätverket måste finnas i samma prenumeration och region som arbetsytan Azure Machine Learning.
> * Om Azure Storage-konton för arbetsytan också är skyddade i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som Azure Databricks-klustret.
> * Förutom de __databricks-privata__ och __databricks-offentliga__ undernät som används av Azure Databricks, __standardundernät__ som skapats för det virtuella nätverket krävs också.

Mer information om hur du använder Azure Databricks med ett virtuellt nätverk finns [i Distribuera Azure Databricks i ditt virtuella Azure-nätverk](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Använda en virtuell dator eller HDInsight-kluster

> [!IMPORTANT]
> Azure Machine Learning stöder endast virtuella datorer som kör Ubuntu.

Så här använder du en virtuell dator eller Azure HDInsight-kluster i ett virtuellt nätverk med arbetsytan:

1. Skapa ett VM- eller HDInsight-kluster med hjälp av Azure-portalen eller Azure CLI och placera klustret i ett virtuellt Azure-nätverk. Mer information finns i följande artiklar:
    * [Skapa och hantera virtuella Azure-nätverk för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Utöka HDInsight med hjälp av ett virtuellt Azure-nätverk](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Om du vill att Azure Machine Learning ska kunna kommunicera med SSH-porten på den virtuella datorn eller klustret konfigurerar du en källpost för nätverkssäkerhetsgruppen. SSH-porten är vanligtvis port 22. Så här tillåter du trafik från den här källan:

    * Välj __Service Tag__i listrutan __Källa__ .

    * Välj __AzureMachineLearning__i listrutan __Källtjänsttagg__ .

    * Välj i listrutan __Källportintervall__ __*__.

    * Välj __Alla__i listrutan __Mål.__

    * Välj __22__i listrutan __Målportintervall.__

    * Under __Protokoll__väljer du __Alla__.

    * Under __Åtgärd__väljer du __Tillåt__.

   ![Inkommande regler för att utföra experiment på ett VM- eller HDInsight-kluster i ett virtuellt nätverk](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Behåll standardregler för utgående trafik för nätverkssäkerhetsgruppen. Mer information finns i standardsäkerhetsreglerna i [säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Om du inte vill använda standardreglerna för utgående och vill begränsa den utgående åtkomsten för det virtuella nätverket läser du [begränsa utgående anslutning från avsnittet virtuellt nätverk.](#limiting-outbound-from-vnet)

1. Koppla VM- eller HDInsight-klustret till din Azure Machine Learning-arbetsyta. Mer information finns i [Ställ in beräkningsmål för modellutbildning](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Använda Azure Kubernetes-tjänsten (AKS)

Så här lägger du till AKS i ett virtuellt nätverk på arbetsytan:

> [!IMPORTANT]
> Innan du påbörjar följande procedur följer du förutsättningarna i hur du [konfigurerar avancerade nätverk i Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) instruktioner och planerar IP-adressering för klustret.
>
> AKS-instansen och det virtuella Azure-nätverket måste finnas i samma region. Om du skyddar de Azure Storage-konton som används av arbetsytan i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som AKS-instansen.

> [!WARNING]
> Azure Machine Learning stöder inte användning av en Azure Kubernetes-tjänst som har privat länk aktiverad.

1. Logga in på [Azure Machine Learning studio](https://ml.azure.com/)och välj sedan din prenumeration och arbetsyta.

1. Välj __Beräkna__ till vänster.

1. Välj __Inferenskluster__ från mitten __+__ och välj sedan .

1. I dialogrutan __Nytt inferenskluster__ väljer du __Avancerat__ under __Nätverkskonfiguration__.

1. Så här konfigurerar du den här beräkningsresursen så att den använder ett virtuellt nätverk:

    1. I listrutan __Resursgrupp__ väljer du den resursgrupp som innehåller det virtuella nätverket.
    1. I listrutan __Virtuellt nätverk__ väljer du det virtuella nätverk som innehåller undernätet.
    1. Markera undernätet i listrutan __Undernät.__
    1. I rutan __Kubernetes Service-adressintervall__ anger du serviceadressintervallet För Kubernetes. Det här adressintervallet använder ett CIDR-notations-IP-intervall (Classless Inter-Domain Routing) för att definiera de IP-adresser som är tillgängliga för klustret. Den får inte överlappa med några IP-intervall i undernätet (till exempel 10.0.0.0/16).
    1. I rutan __Kubernetes DNS-tjänst IP-adress__ anger du KUBernetes DNS-tjänst IP-adress. Den här IP-adressen tilldelas kubernetes DNS-tjänst. Det måste finnas inom Kubernetes serviceadressintervall (till exempel 10.0.0.10).
    1. Ange Docker-bryggans adress i rutan __Docker-brygga.__ Den här IP-adressen har tilldelats Docker Bridge. Det får inte finnas i något IP-intervall i undernät eller kubernetes tjänstadressintervall (till exempel 172.17.0.1/16).

   ![Azure Machine Learning: Virtuella nätverksinställningar för Machine Learning Compute](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Kontrollera att NSG-gruppen som styr det virtuella nätverket har en inkommande säkerhetsregel aktiverad för bedömningsslutpunkten så att den kan anropas utanför det virtuella nätverket.
   > [!IMPORTANT]
   > Behåll standardregler för utgående trafik för NSG. Mer information finns i standardsäkerhetsreglerna i [säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![En inkommande säkerhetsregel](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Du kan också använda Azure Machine Learning SDK för att lägga till Azure Kubernetes-tjänsten i ett virtuellt nätverk. Om du redan har ett AKS-kluster i ett virtuellt nätverk ansluter du det till arbetsytan enligt beskrivningen i [Så här distribueras du till AKS](how-to-deploy-and-where.md). Följande kod skapar en ny AKS-förekomst i `default` undernätet i ett virtuellt nätverk med namnet `mynetwork`:

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

När skapandeprocessen är klar kan du köra inferens, eller modellbedömning, på ett AKS-kluster bakom ett virtuellt nätverk. Mer information finns i [Så här distribuerar du till AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Använda privata IPs med Azure Kubernetes-tjänsten

Som standard tilldelas en offentlig IP-adress till AKS-distributioner. När du använder AKS i ett virtuellt nätverk kan du använda en privat IP-adress i stället. Privata IP-adresser är endast tillgängliga inifrån det virtuella nätverket eller anslutna nätverk.

En privat IP-adress aktiveras genom att konfigurera AKS för att använda en _intern belastningsutjämnare_. 

> [!IMPORTANT]
> Du kan inte aktivera privat IP när du skapar Azure Kubernetes Service-klustret. Den måste aktiveras som en uppdatering till ett befintligt kluster.

Följande kodavsnitt visar hur du **skapar ett nytt AKS-kluster**och uppdaterar det för att använda en privat IP/intern belastningsutjämnare:

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

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

Innehållet i `body.json` filen som refereras av kommandot liknar följande JSON-dokument:

```json
{ 
    "location": "<region>", 
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
> För närvarande kan du inte konfigurera belastningsutjämnaren när du utför en __bifogad__ åtgärd på ett befintligt kluster. Du måste först koppla klustret och sedan utföra en uppdateringsåtgärd för att ändra belastningsutjämnaren.

Mer information om hur du använder den interna belastningsutjämnaren med AKS finns i [Använda intern belastningsutjämnare med Azure Kubernetes Service](/azure/aks/internal-lb).

## <a name="use-azure-container-instances-aci"></a>Använda Azure Container Instances (ACI)

Azure Container Instances skapas dynamiskt när du distribuerar en modell. Om du vill att Azure Machine Learning ska kunna skapa ACI i det virtuella nätverket måste du aktivera __undernätsdelegering__ för undernätet som används av distributionen.

Så här använder du ACI i ett virtuellt nätverk på arbetsytan:

1. Om du vill aktivera undernätsdelegering i det virtuella nätverket använder du informationen i artikeln [Lägg till eller ta bort en undernätsdelegering.](../virtual-network/manage-subnet-delegation.md) Du kan aktivera delegering när du skapar ett virtuellt nätverk eller lägga till det i ett befintligt nätverk.

    > [!IMPORTANT]
    > När du aktiverar `Microsoft.ContainerInstance/containerGroups` delegering använder du som __undernät delegera till servicevärdet.__

2. Distribuera modellen med [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)använder du `vnet_name` `subnet_name` parametrarna och. Ställ in dessa parametrar på det virtuella nätverksnamnet och undernätet där du aktiverade delegeringen.



## <a name="use-azure-firewall"></a>Använda Azure-brandväggen

När du använder Azure-brandväggen måste du konfigurera en nätverksregel för att tillåta trafik till och från följande adresser:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

När du lägger __Protocol__ till regeln anger du `*`protokollet på alla och portarna till .

Mer information om hur du konfigurerar en nätverksregel finns i [Distribuera och konfigurera Azure-brandväggen](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="use-azure-container-registry"></a>Använda Azure Container Registry

> [!IMPORTANT]
> Acr (Azure Container Registry) kan placeras i ett virtuellt nätverk, men du måste uppfylla följande förutsättningar:
>
> * Din Azure Machine Learning-arbetsyta måste vara Enterprise Edition. Information om hur du uppgraderar finns i [Uppgradera till Enterprise-utgåvan](how-to-manage-workspace.md#upgrade).
> * Ditt Azure-behållarregister måste vara Premium-version . Mer information om uppgradering finns i [Ändra SKU: er](/azure/container-registry/container-registry-skus#changing-skus).
> * Ditt Azure Container-register måste finnas i samma virtuella nätverk och undernät som lagringskontot och beräkningsmålen som används för utbildning eller slutledning.
> * Din Azure Machine Learning-arbetsyta måste innehålla ett [Azure Machine Learning-beräkningskluster](how-to-set-up-training-targets.md#amlcompute).
>
>     När ACR ligger bakom ett virtuellt nätverk kan Azure Machine Learning inte använda det för att direkt skapa Docker-avbildningar. I stället används beräkningsklustret för att skapa avbildningarna.

1. Om du vill hitta namnet på Azure Container Registry för din arbetsyta använder du någon av följande metoder:

    __Azure Portal__

    Från översiktsavsnittet på arbetsytan länkar __registervärdet__ till Azure Container Registry.

    ![Azure Container Registry för arbetsytan](./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png)

    __Azure CLI__

    Om du har [installerat machine learning-tillägget för Azure CLI](reference-azure-machine-learning-cli.md)kan du använda `az ml workspace show` kommandot för att visa arbetsytan information.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Det här kommandot returnerar ett värde som liknar `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. Den sista delen av strängen är namnet på Azure Container Registry för arbetsytan.

1. Om du vill begränsa åtkomsten till det virtuella nätverket använder du stegen i [Konfigurera nätverksåtkomst för registret](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). När du lägger till det virtuella nätverket väljer du det virtuella nätverket och undernätet för dina Azure Machine Learning-resurser.

1. Använd Azure Machine Learning Python SDK för att konfigurera ett beräkningskluster för att skapa dockeravbildningar. Följande kodavsnitt visar hur du gör detta:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Ditt lagringskonto, beräkningskluster och Azure Container-registret måste alla finnas i samma undernät i det virtuella nätverket.
    
    Mer information finns i metodreferensen [update().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-)

1. Om du använder Privat länk för din Azure Machine Learning-arbetsyta och placerar Azure Container-registret för din arbetsyta i ett virtuellt nätverk, måste du också använda följande Azure Resource Manager-mall. Med den här mallen kan arbetsytan kommunicera med ACR via den privata länken.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="next-steps"></a>Nästa steg

* [Konfigurera miljöer för utbildning](how-to-set-up-training-targets.md)
* [Välj var du vill distribuera modeller](how-to-deploy-and-where.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
