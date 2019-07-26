---
title: Köra experiment och härledning i ett virtuellt nätverk
titleSuffix: Azure Machine Learning service
description: Kör Machine Learning-experiment och-härledning som skyddar i ett virtuellt Azure-nätverk. Lär dig hur du skapar beräknings mål för modell utbildning och hur du kan lägga till en härledning i ett virtuellt nätverk. Lär dig mer om krav för skyddade virtuella nätverk, till exempel Kräv inkommande och utgående portar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 07/10/2019
ms.openlocfilehash: 412eaac2f82a6d09761dcac53192916df215831f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358786"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>Köra experiment och härledning på ett säkert sätt i ett virtuellt Azure-nätverk

I den här artikeln får du lära dig hur du kör experiment och härledning i ett virtuellt nätverk. Ett virtuellt nätverk fungerar som en säkerhets gränser som isolerar dina Azure-resurser från det offentliga Internet. Du kan också ansluta ett virtuellt Azure-nätverk till ditt lokala nätverk. Med den kan du på ett säkert sätt träna dina modeller och få åtkomst till dina distribuerade modeller. Härlednings-eller modell poängsättning är den fas där den distribuerade modellen används för förutsägelse, oftast på produktions data.

Tjänsten Azure Machine Learning använder andra Azure-tjänster för beräknings resurser. Beräknings resurser (beräknings mål) används för att träna och distribuera modeller. Dessa beräknings mål kan skapas i ett virtuellt nätverk. Du kan till exempel använda Microsoft Data Science Virtual Machine för att träna en modell och sedan distribuera modellen till Azure Kubernetes service (AKS). Mer information om virtuella nätverk finns i [Översikt över Azure-Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="prerequisites"></a>Förutsättningar

Det här dokumentet förutsätter att du är bekant med virtuella Azure-nätverk och IP-nätverk i allmänhet. Det här dokumentet förutsätter också att du har skapat ett virtuellt nätverk och ett undernät som ska användas med dina beräknings resurser. Om du inte är bekant med virtuella Azure-nätverk kan du läsa följande artiklar för att lära dig om tjänsten:

* [IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Snabbstart: Skapa ett virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Filtrera nätverkstrafik](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>Lagrings konto för din arbets yta

> [!IMPORTANT]
> __Standard lagrings kontot__ för Azure Machine Learning-tjänsten kan bara placeras i ett virtuellt nätverk __vid experimentering__.
>
> För __lagrings konton som inte är standard för experimentering__, eller om du använder ett lagrings konto för att få en __härledning__, måste du ha __obegränsad åtkomst till lagrings kontot__.
> 
> Om du inte är säker på om du har ändrat de här inställningarna eller inte, se __ändra standard regeln för nätverks åtkomst__ i [Konfigurera Azure Storage brand väggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security). Använd stegen för att tillåta åtkomst från alla nätverk under härledningen eller modell poängen.

Använd följande steg för att använda placera standard Azure Storages kontot för arbets ytan i ett virtuellt nätverk:

1. Skapa en beräkning av experimentet. Machine Learning-beräkning bakom ett virtuellt nätverk eller koppla ett experiment till arbets ytan till exempel. HDInsight-kluster eller virtuell dator. Mer information finns i [använda Machine Learning-beräkning](#use-machine-learning-compute) och [använda ett kluster avsnitt för virtuella datorer eller HDInsight](#use-a-virtual-machine-or-hdinsight-cluster) i det här dokumentet
2. Gå till det lagrings utrymme som är kopplat till arbets ytan. ![Bild av Azure Portal som visar Azure Storage som är kopplad till Azure Machine Learning service-arbetsytan](./media/how-to-enable-virtual-network/workspace-storage.png)
3. På sidan Azure Storage väljer du __brand väggar och virtuella nätverk__. ![Bild av avsnittet Azure Portal visar brand väggar och virtuella nätverk på Azure Storage sida](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. På sidan __brand väggar och virtuella nätverk__ väljer du följande poster:
    - Välj __Valda nätverk__.
    - Under __virtuella nätverk__väljer du __Lägg till befintligt virtuellt nätverk__ för att lägga till det virtuella nätverk där din experiment beräkning finns. (Se steg 1.)
    - Välj __Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot__.
![Bild av sidan Azure Portal som visar brand väggar och virtuella nätverk under Azure Storage](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. När du kör experimentet ändrar du kör konfigurationen till Använd Blob Storage i experiment-koden:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>Nyckel valv för din arbets yta
Key Vault instans som är associerad med arbets ytan används av Azure Machine Learning tjänst för att lagra autentiseringsuppgifter av olika typer:
* Den associerade anslutnings strängen för lagrings kontot
* Lösen ord till Azure Container databas instanser
* Anslutnings strängar till data lager. 

Följ stegen nedan om du vill använda Azure Machine Learning experiment funktioner med Key Vault bakom ett virtuellt nätverk:
1. Gå till den Key Vault som är kopplad till arbets ytan. ![Bild av Azure Portal som visar Key Vault som är kopplad till Azure Machine Learning service-arbetsytan](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. På sidan Key Vault väljer du __brand väggar och virtuella nätverk__ . ![Bild av avsnittet Azure Portal visar brand väggar och virtuella nätverk på Key Vault sida](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. På sidan __brand väggar och virtuella nätverk__ väljer du följande poster:
    - Välj __Valda nätverk__.
    - Under de __virtuella nätverken__väljer du __Lägg till befintliga virtuella nätverk__ för att lägga till det virtuella nätverk där din experiment beräkning finns.
    - Välj __Tillåt att betrodda Microsoft-tjänster kringgår den här brand väggen__.
![Bild av sidan Azure Portal som visar brand väggar och virtuella nätverk under Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>Använd Machine Learning-beräkning

Om du vill använda Azure Machine Learning Compute i ett virtuellt nätverk använder du följande information om nätverks krav:

- Det virtuella nätverket måste finnas i samma prenumeration och region som Azure Machine Learning service-arbetsytan.

- Det undernät som har angetts för Machine Learning-beräkning-klustret måste ha tillräckligt många otilldelade IP-adresser för att rymma antalet virtuella datorer som är mål för klustret. Om under nätet inte har tillräckligt med otilldelade IP-adresser, kommer klustret att tilldelas delvis.

- Om du planerar att skydda det virtuella nätverket genom att begränsa trafiken lämnar du några öppna portar för tjänsten Machine Learning-beräkning. Mer information finns i [obligatoriska portar](#mlcports).

- Kontrol lera om dina säkerhets principer eller lås på det virtuella nätverkets prenumeration eller resurs grupp begränsar behörigheter för hantering av det virtuella nätverket.

- Om du ska ange flera Machine Learning-beräkning kluster i ett virtuellt nätverk kan du behöva begära en kvot ökning för en eller flera av dina resurser.

    Machine Learning-beräkning tilldelar automatiskt ytterligare nätverks resurser i resurs gruppen som innehåller det virtuella nätverket. För varje Machine Learning-beräkning-kluster allokerar Azure Machine Learnings tjänsten följande resurser:

    - En nätverks säkerhets grupp (NSG)

    - En offentlig IP-adress

    - En belastningsutjämnare

  Dessa resurser begränsas av prenumerationens [resurskvoter](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a id="mlcports"></a>Portar som krävs

Machine Learning-beräkning använder för närvarande tjänsten Azure Batch för att etablera virtuella datorer i det angivna virtuella nätverket. Under nätet måste tillåta inkommande kommunikation från batch-tjänsten. Den här kommunikationen används för att schemalägga körningar på Machine Learning-beräkning noder och för att kommunicera med Azure Storage och andra resurser. Batch lägger till NSG: er på de nätverks gränssnitt (NIC) som är anslutna till virtuella datorer. De här NSG:erna konfigurerar automatiskt regler för inkommande och utgående trafik för att tillåta följande trafik:

- Inkommande TCP-trafik på portarna 29876 och 29877 från en __service tag__ i __BatchNodeManagement__.

    ![Bild av Azure Portal som visar en regel för inkommande trafik med BatchNodeManagement-tjänst tag gen](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- valfritt Inkommande TCP-trafik på port 22 för att tillåta fjärråtkomst. Den här porten behövs bara om du vill ansluta med SSH på den offentliga IP-adressen.
 
- Utgående trafik på vilken port som helst till det virtuella nätverket.

- Utgående trafik på vilken port som helst till Internet. 

Var försiktig om du ändrar eller lägger till regler för inkommande/utgående trafik i batch-konfigurerade NSG: er. Om en NSG blockerar kommunikation till Compute-noderna, ställer Machine Learning-beräkning-tjänsterna till att det inte går att använda de beräknade nodernas status.

Du behöver inte ange NSG: er på under näts nivån eftersom batch konfigurerar sin egen NSG: er. Men om det angivna under nätet har en associerad NSG: er och/eller en brand vägg, konfigurerar du de inkommande och utgående säkerhets reglerna enligt ovan. 

Följande skärm bild visar hur NSG-regelns konfiguration ser ut i Azure Portal:

![Skärm bild av inkommande NSG-regler för Machine Learning-beräkning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Skärm bild av utgående NSG-regler för Machine Learning-beräkning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Begränsa utgående anslutning från det virtuella nätverket

Om du inte vill använda de utgående standard reglerna och vill begränsa den utgående åtkomsten för ditt virtuella nätverk följer du stegen nedan:

- Neka utgående Internet anslutning med NSG-reglerna 

- Begränsa utgående trafik till Azure Storage (med __tjänst tag gen__ för __Storage. Region_Name__ ex. Storage. öster) Azure Container Registry (med __tjänst tag gen__ för __AzureContainerRegistry. Region_Name__ ex. AzureContainerRegistry. öster) och Azure Machine Learning tjänst (med __Service Tag-numret__ __AzureMachineLearning__)

Följande skärm bild visar hur NSG-regelns konfiguration ser ut i Azure Portal:

![Skärm bild av utgående NSG-regler för Machine Learning-beräkning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Användardefinierade vägar för Tvingad tunnel trafik

Om du använder Tvingad tunnel trafik med Azure Machine Learning Compute måste du lägga till [användardefinierade vägar (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) till det undernät som innehåller beräknings resursen.

* En användardefinierad väg för varje IP-adress som används av Azure Batch tjänsten i den region där dina resurser finns. Dessa UDR gör att batch-tjänsten kan kommunicera med datornoder för schemaläggning av aktiviteter. Kontakta Azure-supporten om du vill ha en lista över IP-adresserna för batch-tjänsten.

* Utgående trafik till Azure Storage (särskilt URL-adresser i formuläret `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`och `<account>.blob.core.windows.net`) får inte blockeras av den lokala nätverks enheten.

När du lägger till användardefinierade vägar definierar du vägen för varje relaterat kommando-IP-adressprefix och anger __nästa hopp typ__ till __Internet__. Följande bild visar ett exempel på den här UDR i Azure Portal:

![Exempel på användardefinierad väg för ett adressprefix](./media/how-to-enable-virtual-network/user-defined-route.png)

Mer information finns i [skapa en Azure Batch pool i en artikel för virtuella nätverk](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling) .

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Skapa Machine Learning-beräkning i ett virtuellt nätverk

Följ dessa steg om du vill skapa ett Machine Learning-beräkning kluster med hjälp av Azure Portal:

1. I [Azure Portal](https://portal.azure.com)väljer du Azure Machine Learning service-arbetsytan.

1. I avsnittet __program__ väljer du __Compute__. Välj sedan __Lägg till beräkning__. 

    ![Så här lägger du till en beräkning i Azure Machine Learning-tjänsten](./media/how-to-enable-virtual-network/add-compute.png)

1. Använd följande alternativ för att konfigurera beräknings resursen för att använda ett virtuellt nätverk:

    - __Nätverks konfiguration__: Välj __Avancerat__.

    - __Resursgrupp__: Välj den resurs grupp som innehåller det virtuella nätverket.

    - __Virtuellt nätverk__: Välj det virtuella nätverk som innehåller under nätet.

    - __Undernät__: Välj det undernät som ska användas.

   ![En skärm bild som visar inställningar för virtuella nätverk för Machine Learning-beräkning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Du kan också skapa ett Machine Learning-beräkning-kluster med hjälp av Azure Machine Learning SDK. Följande kod skapar ett nytt Machine Learning-beräkning-kluster i `default` under nätet för ett virtuellt nätverk med namnet: `mynetwork`

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

    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

När skapande processen har slutförts kan du träna din modell med hjälp av klustret. Mer information finns i [Välj och använda ett beräknings mål för utbildning](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Använda en virtuell dator eller HDInsight-kluster

Följ dessa steg om du vill använda en virtuell dator eller ett Azure HDInsight-kluster i ett virtuellt nätverk med din arbets yta:

> [!IMPORTANT]
> Tjänsten Azure Machine Learning stöder bara virtuella datorer som kör Ubuntu.

1. Skapa ett virtuellt dator kluster eller HDInsight-kluster med hjälp av Azure Portal eller Azure CLI och Lägg det i ett virtuellt Azure-nätverk. Mer information finns i följande dokument:
    * [Skapa och hantera virtuella Azure-nätverk för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Utöka HDInsight med ett virtuellt Azure-nätverk](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Om du vill tillåta att Azure Machine Learning tjänsten kommunicerar med SSH-porten på den virtuella datorn eller klustret måste du konfigurera en käll post för NSG. SSH-porten är vanligt vis port 22. Använd följande information för att tillåta trafik från den här källan:

    * __Källa__: Välj __Service Tag__ (Tjänsttagg).

    * __Källtjänsttagg__: Välj __AzureMachineLearning__.

    * __Käll port intervall__: Välj __*__ .

    * __Mål__: Välj __valfri__.

    * __Målportintervall__: Välj __22__.

    * __Protokoll__: Välj __valfri__.

    * __Åtgärd__: Välj __Tillåt__.

   ![Skärm bild av regler för inkommande trafik för att utföra experimentering på en virtuell dator eller HDInsight-kluster i ett virtuellt nätverk](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Behåll de utgående standard reglerna för NSG. Mer information finns i standard säkerhets regler i [säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Om du inte vill använda de utgående standard reglerna och vill begränsa den utgående åtkomsten för ditt virtuella nätverk, se [begränsa utgående anslutning från det virtuella nätverket](#limiting-outbound-from-vnet)
    
1. Anslut den virtuella datorn eller HDInsight-klustret till din Azure Machine Learning service-arbetsyta. Mer information finns i [Konfigurera beräknings mål för modell träning](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service"></a>Använd Azure Kubernetes-tjänsten

> [!IMPORTANT]
> Kontrol lera kraven och planera IP-adresser för klustret innan du fortsätter med stegen. Mer information finns i [Konfigurera avancerat nätverk i Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
>
> Behåll de utgående standard reglerna för NSG. Mer information finns i standard säkerhets regler i [säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Azure Kubernetes-tjänsten och det virtuella Azure-nätverket bör finnas i samma region.

Om du vill lägga till Azure Kubernetes-tjänsten i ett virtuellt nätverk i arbets ytan följer du de här stegen i Azure Portal:

1. Kontrol lera att NSG-gruppen som styr det virtuella nätverket har en inkommande regel aktive rad för Azure Machine Learning tjänst med __service tag-__ __AzureMachineLearning__

    ![Så här lägger du till en beräkning i Azure Machine Learning-tjänsten](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. I [Azure Portal](https://portal.azure.com)väljer du Azure Machine Learning service-arbetsytan.

1. I avsnittet __program__ väljer du __Compute__. Välj sedan __Lägg till beräkning__. 

    ![Så här lägger du till en beräkning i Azure Machine Learning-tjänsten](./media/how-to-enable-virtual-network/add-compute.png)

1. Använd följande alternativ för att konfigurera beräknings resursen för att använda ett virtuellt nätverk:

    - __Nätverks konfiguration__: Välj __Avancerat__.

    - __Resursgrupp__: Välj den resurs grupp som innehåller det virtuella nätverket.

    - __Virtuellt nätverk__: Välj det virtuella nätverk som innehåller under nätet.

    - __Undernät__: Välj under nätet.

    - __Adress intervall för Kubernetes-tjänst__: Välj Kubernetes-tjänstens adress intervall. Det här adress intervallet använder ett CIDR-format för IP-intervall för att definiera IP-adresserna som är tillgängliga för klustret. Det får inte överlappa några IP-intervall för undernät. Exempel: 10.0.0.0/16.

    - __IP-adress för KUBERNETES DNS-tjänst__: Välj IP-adress för Kubernetes DNS-tjänst. Den här IP-adressen är tilldelad DNS-tjänsten Kubernetes. Det måste ligga inom adress intervallet för Kubernetes-tjänsten. Exempel: 10.0.0.10.

    - __Docker Bridge-adress__: Välj Docker-bryggans adress. Den här IP-adressen är tilldelad Docker-bryggan. Det får inte finnas i några IP-adressintervall för undernät eller Kubernetes-tjänstens adress intervall. Exempel: 172.17.0.1/16.

   ![Azure Machine Learning tjänst: Machine Learning-beräkning inställningar för virtuellt nätverk](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Kontrol lera att NSG-gruppen som styr det virtuella nätverket har en inkommande regel aktive rad för poäng slut punkten så att den kan anropas från utanför det virtuella nätverket

    ![Så här lägger du till en beräkning i Azure Machine Learning-tjänsten](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > Om du redan har ett AKS-kluster i ett virtuellt nätverk kan du koppla det till arbets ytan. Mer information finns i [så här distribuerar du till AKS](how-to-deploy-to-aks.md).

Du kan också använda **Azure Machine Learning SDK** för att lägga till Azure Kubernetes-tjänsten i ett virtuellt nätverk. Följande kod skapar en ny Azure Kubernetes-tjänstinstans i `default` under nätet för ett virtuellt nätverk med namnet: `mynetwork`

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

När processen har skapats kan du skapa en härledning/poäng på ett AKS-kluster bakom ett virtuellt nätverk. Mer information finns i [så här distribuerar du till AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera utbildnings miljöer](how-to-set-up-training-targets.md)
* [Var du vill distribuera modeller](how-to-deploy-and-where.md)
* [Distribuera modeller på ett säkert sätt med SSL](how-to-secure-web-service.md)

