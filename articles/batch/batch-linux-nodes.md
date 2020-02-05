---
title: Kör Linux på Virtual Machine Compute-noder – Azure Batch | Microsoft Docs
description: Lär dig att bearbeta dina parallella beräknings arbets belastningar på pooler för virtuella Linux-datorer i Azure Batch.
services: batch
documentationcenter: python
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3691790b2e47ef43c6742fa912aff8d7777900f8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023709"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Etablera Linux Compute-noder i batch-pooler

Du kan använda Azure Batch för att köra parallella beräknings arbets belastningar på både virtuella Linux-och Windows-datorer. Den här artikeln beskriver hur du skapar pooler för Linux Compute-noder i batch-tjänsten med hjälp av både [batchen python][py_batch_package] och [batch .net][api_net] -klient bibliotek.

> [!NOTE]
> Programpaket kan användas för alla Batch-pooler som skapats efter 5 juli 2017. De kan användas för Batch-pooler som skapats mellan 10 mars 2016 och 5 juli 2017, men endast om poolen skapades med en molntjänstkonfiguration. Programpaket kan inte användas för Batch-pooler som har skapats före 10 mars 2016. Mer information om hur du använder programpaket för att distribuera program till dina Batch-noder finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).
>
>

## <a name="virtual-machine-configuration"></a>Konfiguration av virtuell dator
När du skapar en pool med datornoder i batch har du två alternativ för att välja Node-storlek och operativ system: Cloud Services konfiguration och konfiguration av virtuell dator.

En **Cloud Services-konfiguration** tillhandahåller *endast*Windows-beräkningsnoder. Tillgängliga Compute Node-storlekar visas i [storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md)och tillgängliga operativ system visas i [matrisen för Azure gäst operativ system och SDK-kompatibilitet](../cloud-services/cloud-services-guestos-update-matrix.md). När du skapar en pool som innehåller Azure Cloud Services noder, anger du nodens storlek och OS-familjen, som beskrivs i de tidigare nämnda artiklarna. För pooler med Windows Compute-noder används Cloud Services vanligt vis.

**Konfigurationen av den virtuella datorn** innehåller både Linux-och Windows-avbildningar för Compute-noder. Tillgängliga Compute Node-storlekar visas i [storlekar för virtuella datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) och [storlekar för virtuella datorer i Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). När du skapar en pool som innehåller noder för konfiguration av virtuell dator måste du ange storleken på noderna, avbildnings referensen för den virtuella datorn och den batch Node agent-SKU som ska installeras på noderna.

### <a name="virtual-machine-image-reference"></a>Referens för avbildning av virtuell dator

Batch-tjänsten använder [skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för att tillhandahålla Compute-noder i konfigurationen för den virtuella datorn. Du kan ange en avbildning från [Azure Marketplace][vm_marketplace]eller ange en anpassad avbildning som du har för berett. Mer information om anpassade avbildningar finns i [skapa en pool med det delade avbildnings galleriet](batch-sig-images.md).

När du konfigurerar en avbildnings referens för en virtuell dator anger du egenskaperna för avbildningen av den virtuella datorn. Följande egenskaper krävs när du skapar en avbildnings referens för en virtuell dator:

| **Egenskaper för bild referens** | **Exempel** |
| --- | --- |
| Utgivare |Canonical |
| Erbjudande |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |senaste |

> [!TIP]
> Du kan lära dig mer om dessa egenskaper och hur du listar Marketplace-avbildningar i [navigera och välja virtuella Linux-dator avbildningar i Azure med CLI eller PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Observera att inte alla Marketplace-avbildningar för närvarande är kompatibla med batch. Mer information finns i [SKU för Node-agenten](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Node agent-SKU
Batch Node agent är ett program som körs på varje nod i poolen och tillhandahåller kommando-och-Control-gränssnittet mellan noden och batch-tjänsten. Det finns olika implementeringar av Node-agenten, som kallas SKU: er, för olika operativ system. När du skapar en konfiguration för virtuell dator är det i princip att du först ange avbildnings referensen för den virtuella datorn och sedan ange vilken Node-agent som ska installeras på avbildningen. Normalt är varje nod-agent-SKU kompatibel med flera avbildningar av virtuella datorer. Här följer några exempel på Node agent-SKU: er:

* batch. Node. Ubuntu 14,04
* batch. Node. CentOS 7
* batch. Node. Windows amd64

> [!IMPORTANT]
> Alla avbildningar av virtuella datorer som är tillgängliga i Marketplace är inte kompatibla med de för närvarande tillgängliga batch Node-agenterna. Använd batch SDK: er för att lista tillgängliga Node agent-SKU: er och avbildningar av virtuella datorer som de är kompatibla med. Se [listan över avbildningar av virtuella datorer](#list-of-virtual-machine-images) längre fram i den här artikeln för mer information och exempel på hur du hämtar en lista över giltiga avbildningar vid körning.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Skapa en Linux-pool: batch python
Följande kodfragment visar ett exempel på hur du använder [Microsoft Azure Batch klient bibliotek för python][py_batch_package] för att skapa en pool med Ubuntu-serverns beräknings noder. Referens dokumentation för batch python-modulen hittar du på [Azure. batch-paketet][py_batch_docs] på läsa dokumenten.

Det här kodfragmentet skapar en [ImageReference][py_imagereference] explicit och anger var och en av dess egenskaper (utgivare, erbjudande, SKU, version). I produktions kod rekommenderar vi dock att du använder metoden [list_node_agent_skus][py_list_skus] för att fastställa och välja bland kombinationerna tillgängliga avbildnings-och Node agent-SKU vid körning.

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
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Som tidigare nämnts rekommenderar vi att du i stället för att skapa [ImageReference][py_imagereference] uttryckligen använder metoden [list_node_agent_skus][py_list_skus] för att dynamiskt välja bland kombinations avbildningar för just nu stöd för Node-agenten/Marketplace. Följande python-kodfragment visar hur du använder den här metoden.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Skapa en Linux-pool: batch .NET
Följande kodfragment visar ett exempel på hur du använder [batch .net][nuget_batch_net] -klient biblioteket för att skapa en pool med Ubuntu-serverns Compute-noder. Du hittar [batch .net Reference-dokumentationen][api_net] på docs.Microsoft.com.

I följande kodfragment används [PoolOperations][net_pool_ops]. [ListNodeAgentSkus][net_list_skus] -metoden för att välja från en lista över stödda Marketplace-avbildningar och Node agent-kombinationer som stöds för närvarande. Den här tekniken är önskvärd eftersom listan över kombinationer som stöds kan ändras från tid till tid. Oftast läggs de kombinationer som stöds till.

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

Även om det tidigare kodfragmentet använder [PoolOperations][net_pool_ops]. [ListNodeAgentSkus][net_list_skus] -metoden för att dynamiskt lista och välja från avbildningar som stöds och som stöds (rekommenderas), kan du också konfigurera en [ImageReference][net_imagereference] explicit:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista över avbildningar av virtuella datorer
I följande tabell visas avbildningarna för den virtuella Marketplace-datorn som är kompatibla med de tillgängliga batch Node-agenterna när den här artikeln senast uppdaterades. Det är viktigt att Observera att listan inte är definitiv, eftersom avbildningar och nods agenter kan läggas till eller tas bort när som helst. Vi rekommenderar att batch-program och-tjänster alltid använder [list_node_agent_skus][py_list_skus] (python) eller [ListNodeAgentSkus][net_list_skus] (batch .net) för att fastställa och välja bland de aktuella tillgängliga SKU: erna.

> [!WARNING]
> Följande lista kan ändras när som helst. Använd alltid de **SKU** -metoder för List-noden som finns i batch-API: erna för att visa en lista över kompatibla virtuella datorer och Node agent-SKU: er när du kör batch-jobb.
>
>

| **Utgivare** | **Erbjudande** | **Bild-SKU** | **Version** | **ID för Node-agent-SKU** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| batch | rendering-centos73 | Render | senaste | batch. Node. CentOS 7 |
| batch | rendering-windows2016 | Render | senaste | batch. Node. Windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | senaste | batch. Node. Ubuntu 16,04 |
| Canonical | UbuntuServer | 14.04.5-LTS | senaste | batch. Node. Ubuntu 14,04 |
| credativ | Debian | 9 | senaste | batch. Node. Debian 9 |
| credativ | Debian | 8 | senaste | batch. Node. Debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | senaste | batch. Node. CentOS 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | senaste | batch. Node. Windows amd64 |
| microsoft-azure-batch | CentOS-container | 7-4 | senaste | batch. Node. CentOS 7 |
| microsoft-azure-batch | CentOS-container-RDMA | 7-4 | senaste | batch. Node. CentOS 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04 – LTS | senaste | batch. Node. Ubuntu 16,04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04 – LTS | senaste | batch. Node. Ubuntu 16,04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | senaste | batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | senaste | batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Data Center-med-containers | senaste | batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | senaste | batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | senaste | batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | senaste | batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | senaste | batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | senaste | batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | senaste | batch. Node. Windows amd64 |
| OpenLogic | CentOS | 7.4 | senaste | batch. Node. CentOS 7 |
| OpenLogic | CentOS-HPC | 7.4 | senaste | batch. Node. CentOS 7 |
| OpenLogic | CentOS-HPC | 7.3 | senaste | batch. Node. CentOS 7 |
| OpenLogic | CentOS-HPC | 7.1 | senaste | batch. Node. CentOS 7 |
| Oracle | Oracle-Linux | 7.4 | senaste | batch. Node. CentOS 7 |
| SUSE | SLES – HPC | 12-SP2 | senaste | batch. Node. openSUSE 42,1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Ansluta till Linux-noder med SSH
Under utvecklingen eller under fel sökningen kanske du måste logga in på noderna i poolen. Till skillnad från Windows Compute-noder kan du inte använda Remote Desktop Protocol (RDP) för att ansluta till Linux-noder. I stället möjliggör batch-tjänsten SSH-åtkomst på varje nod för fjärr anslutning.

Följande python-kodfragment skapar en användare på varje nod i en pool, vilket krävs för fjärr anslutning. Den skriver sedan ut SSH-anslutningen (Secure Shell) för varje nod.

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

Här är exempel på utdata för föregående kod för en pool som innehåller fyra Linux-noder:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

I stället för ett lösen ord kan du ange en offentlig SSH-nyckel när du skapar en användare på en nod. Använd **ssh_public_key** -parametern på [COMPUTENODEUSER][py_computenodeuser]i python SDK. Använd [ComputeNodeUser][net_computenodeuser]i .net. [SshPublicKey][net_ssh_key] -egenskap.

## <a name="pricing"></a>Prissättning
Azure Batch bygger på Azure Cloud Services-och Azure Virtual Machines-teknik. Själva batch-tjänsten erbjuds utan kostnad, vilket innebär att du bara debiteras för de beräknings resurser som dina batch-lösningar använder. När du väljer **Cloud Services konfiguration**debiteras du baserat på [Cloud Services prissättnings][cloud_services_pricing] struktur. När du väljer **konfiguration för virtuell dator**debiteras du baserat på [Virtual Machines pris][vm_pricing] strukturen. 

Om du distribuerar program till dina batch-noder med hjälp av [programpaket](batch-application-packages.md), debiteras du också för de Azure Storage-resurser som dina program paket använder. I allmänhet är Azure Storage kostnader minimala. 

## <a name="next-steps"></a>Nästa steg

[Python-kod exemplen][github_samples_py] i [Azure-Batch-samples-][github_samples] lagringsplatsen på GitHub innehåller skript som visar hur du utför vanliga batch-åtgärder, t. ex. pool, jobb och aktivitets skapande. [Readme-filen][github_py_readme] som medföljer python-exemplen innehåller information om hur du installerar de nödvändiga paketen.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
