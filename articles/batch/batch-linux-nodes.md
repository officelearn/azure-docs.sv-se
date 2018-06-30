---
title: Kör Linux på den virtuella datorn datornoder - Azure Batch | Microsoft Docs
description: Lär dig mer om att bearbeta din parallella beräkning av arbetsbelastningar på pooler för Linux-datorer i Azure Batch.
services: batch
documentationcenter: python
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3faa9e811216cc930354b76903519a66f3d3587
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128819"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Etablera Linux compute-noder i Batch-pooler

Du kan använda Azure Batch för att köra parallella beräkning av arbetsbelastningar på Linux- och Windows-datorer. Den här artikeln beskriver hur du skapar pooler för Linux compute-noder i Batch-tjänsten med både den [Batch Python] [ py_batch_package] och [Batch .NET] [ api_net]klientbibliotek.

> [!NOTE]
> Programpaket kan användas för alla Batch-pooler som skapats efter 5 juli 2017. De kan användas för Batch-pooler som skapats mellan 10 mars 2016 och 5 juli 2017, men endast om poolen skapades med en molntjänstkonfiguration. Programpaket kan inte användas för Batch-pooler som har skapats före 10 mars 2016. Mer information om hur du använder programpaket för att distribuera program till dina Batch-noder finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).
>
>

## <a name="virtual-machine-configuration"></a>Konfiguration av virtuell dator
När du skapar en pool med compute-noder i Batch har du två alternativ att välja noden storleken och operativsystemet: moln-tjänsternas konfiguration och konfiguration av virtuell dator.

En **Cloud Services-konfiguration** tillhandahåller *endast*Windows-beräkningsnoder. Tillgängliga compute-nod storlekar listas i [storlekar för molntjänster](../cloud-services/cloud-services-sizes-specs.md), och tillgängliga operativsystem visas i den [Azure gäst-OS-versioner och SDK-kompatibilitetsmatris](../cloud-services/cloud-services-guestos-update-matrix.md). När du skapar en pool som innehåller Azure Cloud Services noder kan ange du nodstorlek och OS-familj som beskrivs i de tidigare nämnda artiklarna. Molntjänster används oftast för pooler för Windows-beräkningsnoder.

**Konfiguration av virtuell dator** innehåller både Linux och Windows-avbildningar för compute-noder. Tillgängliga compute-nod storlekar listas i [storlekar för virtuella datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) och [storlekar för virtuella datorer i Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). När du skapar en pool som innehåller konfiguration av virtuell dator noder måste du ange storleken på noderna, bildreferens virtuell dator och Batch nod agenten SKU installeras på noderna.

### <a name="virtual-machine-image-reference"></a>Bildreferens för virtuell dator
Batch-tjänsten används [skalningsuppsättningar i virtuella](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) att tillhandahålla beräkningsnoder i konfigurationen av den virtuella datorn. Du kan ange en bild från den [Azure Marketplace][vm_marketplace], eller ange en anpassad avbildning som du har förberett. Mer information om anpassade avbildningar finns [skapa en pool med en anpassad avbildning](batch-custom-images.md).

När du konfigurerar en virtuell dator en bildreferens kan ange du egenskaperna för den virtuella datoravbildningen. När du skapar en virtuell dator bildreferens krävs följande egenskaper:

| **Referens för Bildegenskaper** | **Exempel** |
| --- | --- |
| Utgivare |Canonical |
| Erbjudande |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |senaste |

> [!TIP]
> Du kan lära dig mer om de här egenskaperna och visa en lista över Marketplace-bilder i [navigera och välj Linux virtuella avbildningar i Azure med CLI eller PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Observera att inte alla Marketplace-bilder är kompatibelt med Batch. Mer information finns i [nod agent SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Noden agent SKU
Agent för Batch-nod är ett program som körs på varje nod i poolen och ger och kommandokontroll gränssnittet mellan noden och Batch-tjänsten. Det finns olika implementeringar av agenten nod kallas SKU: er, för olika operativsystem. I princip när du skapar en konfiguration av virtuell dator du första ange bildreferens virtuella datorn och ange sedan noden agenten att installera avbildningen. Normalt varje nod agent SKU är kompatibel med flera virtuella avbildningar. Här följer några exempel på noden agent SKU: er:

* batch.node.Ubuntu 14.04
* batch.node.centos 7
* batch.node.Windows amd64

> [!IMPORTANT]
> Inte alla virtuella avbildningar som är tillgängliga i Marketplace är kompatibla med de tillgängliga Batch nod agenterna. Använda Batch-SDK: er för att visa tillgängliga noden agenten SKU: er och virtuella avbildningar som de är kompatibla. Finns det [lista virtuella avbildningar](#list-of-virtual-machine-images) senare i den här artikeln för mer information och exempel på hur du hämtar en lista över giltiga bilder vid körning.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Skapa en pool med Linux: Batch Python
Följande kodavsnitt visar ett exempel på hur du använder den [Microsoft Azure Batch-klientbibliotek för Python] [ py_batch_package] att skapa en pool med Ubuntu Server compute-noder. I referensdokumentationen för Batch Python-modulen finns på [azure.batch paketet] [ py_batch_docs] på läsa dokumenten.

Den här kodutdrag skapar en [ImageReference] [ py_imagereference] explicit och anger var och en av dess egenskaper (utgivare, erbjudande, SKU och version). I produktionskod, men vi rekommenderar att du använder den [list_node_agent_skus] [ py_list_skus] metod för att avgöra och välja bland de tillgängliga avbildning och noden agent SKU kombinationerna vid körning.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Som tidigare nämnts, rekommenderar vi att i stället för att skapa den [ImageReference] [ py_imagereference] explicit, använder du den [list_node_agent_skus] [ py_list_skus] metod för att dynamiskt Välj nod som för närvarande stöds agent/Marketplace avbildningen kombinationer. Python följande utdrag visar hur du använder den här metoden.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Skapa en pool med Linux: Batch .NET
Följande kodavsnitt visar ett exempel på hur du använder den [Batch .NET] [ nuget_batch_net] klientbiblioteket för att skapa en pool med Ubuntu Server compute-noder. Du hittar den [Batch .NET referensdokumentationen] [ api_net] på docs.microsoft.com.

I följande kod fragment används den [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metoden att välja från listan över för närvarande stöds Marketplace-avbildning och noden agent SKU kombinationer. Den här tekniken är önskvärt eftersom listan över kombinationer som stöds kan då förändras. Oftast läggs kombinationer som stöds.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Även om utdraget använder den [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metod för att dynamiskt och väljer från stöds avbildningen och noden agent SKU kombinationer (rekommenderas), du kan också konfigurera en [ImageReference] [ net_imagereference] explicit:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista över avbildningar av virtuella datorer
I följande tabell visas de virtuella datorn Marketplace-bilder som är kompatibla med de tillgängliga Batch nod agenterna när den här artikeln senast uppdaterades. Det är viktigt att Observera att den här listan inte är slutgiltig eftersom bilder och nod-agenter kan läggas till eller tas bort när som helst. Vi rekommenderar att dina Batch-program och tjänster alltid använder [list_node_agent_skus] [ py_list_skus] (Python) eller [ListNodeAgentSkus] [ net_list_skus] () Batch .NET) vill fastställa och välja bland tillgängliga SKU: er.

> [!WARNING]
> I följande lista kan ändras när som helst. Använd alltid den **lista nod agent SKU** metoder som är tillgängliga i Batch-API: er för lista över kompatibla virtuella datorn och noden agent SKU: er när du kör Batch-jobb.
>
>

| **Utgivare** | **Erbjudande** | **Bild SKU** | **Version** | **Noden agent SKU-ID** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| batch | återgivning centos73 | återgivning | senaste | batch.node.centos 7 |
| batch | återgivning windows2016 | återgivning | senaste | batch.node.Windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | senaste | batch.node.Ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | senaste | batch.node.Ubuntu 14.04 |
| credativ | Debian | 9 | senaste | batch.node.debian 9 |
| credativ | Debian | 8 | senaste | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | senaste | batch.node.centos 7 |
| microsoft-ads | standard-data-vetenskap-vm | standard-data-vetenskap-vm | senaste | batch.node.Windows amd64 |
| Microsoft azure batch | centos-behållare | 7-4 | senaste | batch.node.centos 7 |
| Microsoft azure batch | rdma-centos-behållare | 7-4 | senaste | batch.node.centos 7 |
| Microsoft azure batch | Ubuntu server behållare | 16-04-lts | senaste | batch.node.Ubuntu 16.04 |
| Microsoft azure batch | Ubuntu-server-behållaren-rdma | 16-04-lts | senaste | batch.node.Ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016 Datacenter | senaste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | senaste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016 Datacenter med behållare | senaste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | senaste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | senaste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | senaste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | senaste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | senaste | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2 SP1-smalldisk | senaste | batch.node.Windows amd64 |
| OpenLogic | CentOS | 7.4 | senaste | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | senaste | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | senaste | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | senaste | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | senaste | batch.node.centos 7 |
| SUSE | SLES HPC | 12-SP2 | senaste | batch.node.OpenSuSE 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Anslut till Linux-noder via SSH
Under utveckling eller vid felsökning kan det vara nödvändigt att logga in på noderna i din pool. Du kan inte använda Remote Desktop Protocol (RDP) till skillnad från Windows compute-noder för att ansluta till Linux-noder. Batch-tjänsten kan i stället SSH-åtkomst på varje nod för fjärranslutning.

Följande kodavsnitt i Python skapar en användare på varje nod i en programpool, vilket krävs för anslutning. Secure shell (SSH) anslutningsinformationen för varje nod skriver sedan ut.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Här är exempel på utdata till föregående kod för en pool som innehåller fyra Linux noder:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Du kan ange en offentlig SSH-nyckel när du skapar en användare på en nod i stället för ett lösenord. I Python SDK kan du använda den **ssh_public_key** parameter på [ComputeNodeUser][py_computenodeuser]. I .NET, använda den [ComputeNodeUser][net_computenodeuser].[ SshPublicKey] [ net_ssh_key] egenskapen.

## <a name="pricing"></a>Prissättning
Azure Batch bygger på Azure-molntjänster och virtuella datorer i Azure-teknik. Själva Batch-tjänsten erbjuds utan kostnad, vilket innebär att debiteras du bara för beräkningsresurserna att använda Batch-lösningar. När du väljer **Cloud Services-konfiguration**, debiteras du baserat på de [molntjänster priser] [ cloud_services_pricing] struktur. När du väljer **konfiguration av virtuell dator**, debiteras du baserat på de [virtuella datorer priser] [ vm_pricing] struktur. 

Om du distribuerar program till din Batch-noder som använder [programpaket](batch-application-packages.md), även debiteras du för Azure Storage-resurser att använda din programpaket. I allmänhet är kostnader för Azure Storage minimal. 

## <a name="next-steps"></a>Nästa steg
### <a name="batch-python-tutorial"></a>Batch Python-självstudier
För en mer detaljerad vägledning om hur du arbetar med Batch med hjälp av Python kolla [Kom igång med Azure Batch Python klienten](batch-python-tutorial.md). Dess tillhörande [kodexemplet] [ github_samples_pyclient] innehåller en hjälpfunktion `get_vm_config_for_distro`, som visar en annan metod för att hämta konfigurationen för en virtuell dator.

### <a name="batch-python-code-samples"></a>Batch Python-kodexempel
Den [Python kodexempel] [ github_samples_py] i den [azure-batch-samples] [ github_samples] databasen på GitHub innehåller skript som visar hur du utför vanliga Batch-åtgärder, till exempel pool, jobb och skapa aktiviteter. Den [viktigt] [ github_py_readme] som medföljer Python exempel innehåller information om hur du installerar de nödvändiga paketen.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: https://docs.microsoft.com/python/api/azure.batch.models.computenodeuser
[py_imagereference]: https://docs.microsoft.com/python/api/azure.mgmt.batch.models.imagereference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
