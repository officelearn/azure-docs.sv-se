---
title: Kör Linux på beräkningsnoder för virtuella datorer – Azure Batch | Microsoft-dokument
description: Lär dig hur du bearbetar dina parallella beräkningsarbetsbelastningar på pooler av virtuella Linux-datorer i Azure Batch.
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
ms.openlocfilehash: 977504f41e93e37ae2c5ce9bdb1182a1cfe0a3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252290"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Etablera Linux-beräkningsnoder i batchpooler

Du kan använda Azure Batch för att köra parallella beräkningsarbetsbelastningar på både Virtuella Linux- och Windows-datorer. I den här artikeln beskrivs hur du skapar pooler av Linux-beräkningsnoder i batch-tjänsten med hjälp av både [batch python-][py_batch_package] och [batch-KLIENTBIBLIOTEK.][api_net]

> [!NOTE]
> Programpaket kan användas för alla Batch-pooler som skapats efter 5 juli 2017. De kan användas för Batch-pooler som skapats mellan 10 mars 2016 och 5 juli 2017, men endast om poolen skapades med en molntjänstkonfiguration. Programpaket kan inte användas för Batch-pooler som har skapats före 10 mars 2016. Mer information om hur du använder programpaket för att distribuera program till dina Batch-noder finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).
>
>

## <a name="virtual-machine-configuration"></a>Konfiguration av virtuella datorer
När du skapar en pool med beräkningsnoder i Batch har du två alternativ för att välja nodstorlek och operativsystem: Konfiguration av molntjänster och konfiguration av virtuella datorer.

En **Cloud Services-konfiguration** tillhandahåller *endast*Windows-beräkningsnoder. Tillgängliga beräkningsnodstorlekar visas i [Storlekar för molntjänster](../cloud-services/cloud-services-sizes-specs.md)och tillgängliga operativsystem visas i [Azure Guest OS-versionerna och SDK-kompatibilitetsmatrisen](../cloud-services/cloud-services-guestos-update-matrix.md). När du skapar en pool som innehåller Azure Cloud Services-noder anger du nodstorleken och OS-familjen, som beskrivs i de tidigare nämnda artiklarna. För pooler av Windows-beräkningsnoder används molntjänster oftast.

**Konfiguration av virtuella** datorer tillhandahåller både Linux- och Windows-avbildningar för beräkningsnoder. Tillgängliga beräkningsnodstorlekar visas i [Storlekar för virtuella datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) och storlekar för virtuella datorer i [Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). När du skapar en pool som innehåller noder för konfiguration av virtuella datorer måste du ange storleken på noderna, avbildningsreferensen för den virtuella datorn och den batchnodagent SKU som ska installeras på noderna.

### <a name="virtual-machine-image-reference"></a>Bildreferens för virtuell dator

Batch-tjänsten använder [skaluppsättningar för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för att tillhandahålla beräkningsnoder i konfigurationen för virtuella datorer. Du kan ange en avbildning från [Azure Marketplace][vm_marketplace]eller tillhandahålla en anpassad avbildning som du har förberett. Mer information om anpassade bilder finns i [Skapa en pool med det delade bildgalleriet](batch-sig-images.md).

När du konfigurerar en avbildningsreferens för virtuella datorer anger du egenskaperna för avbildningen för den virtuella datorn. Följande egenskaper krävs när du skapar en avbildningsreferens för virtuella datorer:

| **Egenskaper för bildreferens** | **Exempel** |
| --- | --- |
| Utgivare |Canonical |
| Erbjudande |UbuntuServer |
| SKU |18.04-LTS |
| Version |senaste |

> [!TIP]
> Du kan läsa mer om dessa egenskaper och hur du listar Marketplace-avbildningar i [Navigera och väljer Linux-avbildningar för virtuella datorer i Azure med CLI eller PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Observera att inte alla Marketplace-avbildningar för närvarande är kompatibla med Batch. Mer information finns i [Node agent SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Nodagent SKU
Batch-nodagenten är ett program som körs på varje nod i poolen och tillhandahåller kommando-och-kontroll-gränssnittet mellan noden och batch-tjänsten. Det finns olika implementeringar av nodagenten, skus, för olika operativsystem. När du skapar en konfiguration för virtuella datorer anger du först avbildningsreferensen för den virtuella datorn och anger sedan den nodagent som ska installeras på avbildningen. Vanligtvis är varje nodagent SKU kompatibel med flera avbildningar för virtuella datorer. Här är några exempel på nodagent SKU:er:

* batch.node.ubuntu 18,04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Alla avbildningar för virtuella datorer som är tillgängliga på Marketplace är inte kompatibla med de för närvarande tillgängliga batchnodagenterna. Använd batch-SDK:erna för att lista de tillgängliga nodagentSku:erna och de virtuella datoravbildningar som de är kompatibla med. Mer information och exempel på hur du hämtar en lista över giltiga bilder under körning finns i [listan över avbildningar](#list-of-virtual-machine-images) med virtuella datorer senare i den här artikeln.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Skapa en Linux-pool: Batch Python
Följande kodavsnitt visar ett exempel på hur du använder [Microsoft Azure Batch Client Library for Python för][py_batch_package] att skapa en pool med Ubuntu Server-beräkningsnoder. Referensdokumentation för Batch Python-modulen finns på [azure.batch-paketet][py_batch_docs] på Läs dokumenten.

Det här kodavsnittet skapar en [ImageReference][py_imagereference] uttryckligen och anger var och en av dess egenskaper (utgivare, erbjudande, SKU, version). I produktionskod rekommenderar vi dock att du använder [list_supported_images][py_list_supported_images] metod för att bestämma och välja från de tillgängliga bild- och nodagenten SKU-kombinationer vid körning.

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
vm_size = "STANDARD_D2_V3"
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
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Som tidigare nämnts rekommenderar vi att du i stället för att uttryckligen skapa [ImageReference-metoden][py_imagereference] använder [metoden list_supported_images][py_list_supported_images] för att dynamiskt välja från de nodagent/Marketplace-bildkombinationer som för närvarande stöds. Följande Python-kodavsnitt visar hur du använder den här metoden.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Skapa en Linux-pool: Batch .NET
Följande kodavsnitt visar ett exempel på hur du använder [batch -NET-klientbiblioteket][nuget_batch_net] för att skapa en pool med Ubuntu Server-beräkningsnoder. Du hittar [referensdokumentationen för batch .NET][api_net] på docs.microsoft.com.

Följande kodavsnitt använder [PoolOperations][net_pool_ops]. [ListSupportedImages][net_list_supported_images] metod för att välja från listan över för närvarande stöds Marketplace image och nod agent SKU kombinationer. Den här tekniken är önskvärd eftersom listan över kombinationer som stöds kan ändras från tid till annan. Oftast läggs kombinationer som stöds till.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

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

Även om föregående utdrag använder [PoolOperations][net_pool_ops]. [ListSupportedImages][net_list_supported_images] metod för att dynamiskt lista och välja från bild- och nodagent SKU-kombinationer som stöds (rekommenderas), du kan också konfigurera en [ImageReference][net_imagereference] explicit:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista över avbildningar för virtuella datorer
Om du vill hämta listan över alla Marketplace-avbildningar för virtuella datorer som stöds för batch-tjänsten och deras motsvarande nodagenter kan du utnyttja [list_supported_images][py_list_supported_images] (Python), [ListSupportedImages][net_list_supported_images] (Batch .NET) eller motsvarande API på respektive språk SDK som du väljer.

## <a name="connect-to-linux-nodes-using-ssh"></a>Ansluta till Linux-noder med SSH
Under utvecklingen eller under felsökningen kan det vara nödvändigt att logga in på noderna i poolen. Till skillnad från Windows-beräkningsnoder kan du inte använda RDP (Remote Desktop Protocol) för att ansluta till Linux-noder. I stället aktiverar batch-tjänsten SSH-åtkomst på varje nod för fjärranslutning.

Följande Python-kodavsnitt skapar en användare på varje nod i en pool, vilket krävs för fjärranslutning. Därefter skrivs den säkra SSH-anslutningsinformationen (Säkra skal) för varje nod.

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

Här är exempelutdata för den tidigare koden för en pool som innehåller fyra Linux-noder:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

I stället för ett lösenord kan du ange en allmän SSH-nyckel när du skapar en användare på en nod. I Python SDK använder **du parametern ssh_public_key** på [ComputeNodeUser][py_computenodeuser]. Använd [ComputeNodeUser][net_computenodeuser]i .NET . [SshPublicKey.][net_ssh_key]

## <a name="pricing"></a>Prissättning
Azure Batch bygger på Azure Cloud Services och Azure Virtual Machines-teknik. Själva batch-tjänsten erbjuds utan kostnad, vilket innebär att du debiteras endast för beräkningsresurser (och tillhörande kostnader som medför) som dina batch-lösningar förbrukar. När du väljer **Cloud Services-konfiguration**debiteras du baserat på [molntjänsternas prisstruktur.][cloud_services_pricing] När du väljer **Konfiguration för virtuella**datorer debiteras du baserat på [prisstrukturen för virtuella datorer.][vm_pricing]

Om du distribuerar program till dina batchnoder med hjälp av [programpaket](batch-application-packages.md)debiteras du också för de Azure Storage-resurser som programpaketen använder.

## <a name="next-steps"></a>Nästa steg

[Python-kodexemplen][github_samples_py] i [azure-batch-samples-databasen][github_samples] på GitHub innehåller skript som visar hur du utför vanliga Batch-åtgärder, till exempel pool, jobb och skapande av uppgifter. [Readme][github_py_readme] som medföljer Python-exemplen innehåller information om hur du installerar de nödvändiga paketen.

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
[net_list_supported_images]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
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
[py_list_supported_images]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
