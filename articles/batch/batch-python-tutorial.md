---
title: "Självstudiekurs – Använda Azure Batch SDK för Python | Microsoft Docs"
description: "Lär dig de grundläggande principerna för Azure Batch och skapa en enkel lösning med Python."
services: batch
documentationcenter: python
author: tamram
manager: timlt
editor: 
ms.assetid: 42cae157-d43d-47f8-88f5-486ccfd334f4
ms.service: batch
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d1c327e90ca3343703784c22aba93280d4599723
ms.lasthandoff: 04/03/2017


---
# <a name="get-started-with-the-batch-sdk-for-python"></a>Kom igång med Batch SDK för Python

> [!div class="op_single_selector"]
> * [NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
>
>

Lär dig grunderna om [Azure Batch][azure_batch] och [Batch Python][py_azure_sdk]-klienten när vi går igenom ett litet Batch-program som skrivits i Python. Vi tittar på hur två exempelskript utnyttjar Batch-tjänsten för att bearbeta en parallell arbetsbelastning på Linux-datorer i molnet och hur de interagerar med [Azure Storage](../storage/storage-introduction.md) för mellanlagring och hämtning av filer. Du får lära dig hur ett vanligt Batch-programarbetsflöde ser ut och får en grundläggande förståelse för de viktigaste komponenterna i Batch, t.ex. jobb, aktiviteter, pooler och beräkningsnoder.

![Arbetsflöde för Batch-lösning (grundläggande)][11]<br/>

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har kunskaper om Python och att du är bekant med Linux. Vi förutsätter också att du uppfyller kontokraven som anges nedan för Azure och Batch- och Storage-tjänsterna.

### <a name="accounts"></a>Konton
* **Azure-konto**: Om du inte redan har en Azure-prenumeration kan du [skapa ett kostnadsfritt Azure-konto][azure_free_account].
* **Batch-konto**: När du har skaffat en Azure-prenumeration [skapar du ett Azure Batch-konto](batch-account-create-portal.md).
* **Lagringskonto**: Se [Skapa ett lagringskonto](../storage/storage-create-storage-account.md#create-a-storage-account) i [Om Azure-lagringskonton](../storage/storage-create-storage-account.md).

### <a name="code-sample"></a>Kodexempel
[Kodexemplet][github_article_samples] i Python-självstudiekursen är ett av de många Batch-kodexemplen i [azure-batch-samples][github_samples]-databasen på GitHub. Du kan ladda ned alla exempel genom att klicka på **Klona eller ladda ned > Ladda ned ZIP** på startsidan för databasen eller genom att klicka på direktnedladdningslänken för [azure-batch-samples-master.zip][github_samples_zip]. När du har extraherat innehållet i ZIP-filen hittar du de två skripten för den här självstudiekursen i katalogen `article_samples`:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Python-miljö
För att köra *python_tutorial_client.py*-exempelskriptet på den lokala arbetsstationen behöver du en **Python-tolk** som är kompatibel med version **2.7** eller **3.3+**. Skriptet har testats på både Linux och Windows.

### <a name="cryptography-dependencies"></a>kryptografiberoenden
Du måste installera beroendena för det [kryptografibibliotek][crypto] som krävs av Python-paketen `azure-batch` och `azure-storage`. Utför någon av följande åtgärder, beroende på din plattform, eller läs avsnittet med information om [kryptografiinstallationen][crypto_install]:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`
* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`
* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`
* Windows

    `pip install cryptography`

> [!NOTE]
> Om du installerar för Python 3.3 + på Linux ska du använda motsvarigheterna till python3 för Python-beroenden. Till exempel på Ubuntu: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`
>
>

### <a name="azure-packages"></a>Azure-paket
Installera Python-paketen för **Azure Batch** och **Azure Storage**. Du kan installera båda paketen med hjälp av **pip**, och *requirements.txt* hittar du här:

`/azure-batch-samples/Python/Batch/requirements.txt`

Skicka följande **pip**-kommando för att installera Batch- och Storage-paketen:

`pip install -r requirements.txt`

Du kan installera Python-paketen [azure batch][pypi_batch] och [azure-lagring][pypi_storage] manuellt:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [!TIP]
> Om du använder ett konto utan privilegier kan du behöva lägga till prefixet `sudo` till dina kommandon. Till exempel `sudo pip install -r requirements.txt`. Mer information om hur du installerar Python-paket finns i [Install Packages][pypi_install] (Installera paket) på python.org.
>
>

## <a name="batch-python-tutorial-code-sample"></a>Kodexempel från självstudiekursen om Python i Batch
Kodexemplet från självstudiekursen om Python i Batch består av två Python-skript och några datafiler.

* **python_tutorial_client.PY**: Interagerar med Batch- och Storage-tjänsterna för att köra en parallell arbetsbelastning på beräkningsnoder (virtuella datorer). Skriptet *python_tutorial_client.py* körs på den lokala arbetsstationen.
* **python_tutorial_task.PY**: Skript som körs på beräkningsnoder i Azure för att utföra själva arbetet. I exemplet parsar *python_tutorial_task.py* texten i en fil som hämtats från Azure Storage (indatafilen). Därefter genererar den en textfil (utdatafilen) som innehåller en lista över de tre översta orden som förekommer i indatafilen. När utdatafilen har skapats laddar *python_tutorial_task.py* upp filen till Azure Storage. Detta gör att filen är tillgänglig för nedladdning till klientskriptet som körs på din arbetsstation. *python_tutorial_task.py*-skriptet körs parallellt på flera beräkningsnoder i Batch-tjänsten.
* **./data/taskdata\*.txt**: Dessa tre textfiler tillhandahåller indata för uppgifterna som körs på beräkningsnoderna.

Följande diagram illustrerar de primära åtgärderna som utförs av klient- och uppgiftsskripten. Detta grundläggande arbetsflöde är typiskt för många beräkningslösningar som skapas med Batch. Det illustrerar inte alla funktioner som är tillgängliga i Batch-tjänsten, men delar av det här arbetsflödet ingår i nästan alla Batch-scenarier.

![Batch-exempelarbetsflöde][8]<br/>

[**Steg 1.**](#step-1-create-storage-containers) Skapa **behållare** i Azure Blob Storage.<br/>
[**Steg 2.**](#step-2-upload-task-script-and-data-files) Ladda upp skript och indatafiler till behållare.<br/>
[**Steg 3.**](#step-3-create-batch-pool) Skapa en Batch-**pool**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Poolens **StartTask** laddar ned uppgiftsskriptet (python_tutorial_task.py) till noder när de läggs till i poolen.<br/>
[**Steg 4.**](#step-4-create-batch-job) Skapa ett Batch-**jobb**.<br/>
[**Steg 5.**](#step-5-add-tasks-to-job) Lägg till **aktiviteter** till jobbet.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Aktiviteterna schemaläggs att köras på noder.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Varje aktivitet hämtar sina indata från Azure Storage och börjar sedan köra.<br/>
[**Steg 6.**](#step-6-monitor-tasks) Övervaka aktiviteter.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Allt eftersom aktiviteterna slutförs överför de sina utdata till Azure Storage.<br/>
[**Step 7.**](#step-7-download-task-output) Hämta aktiviteternas utdata från Storage.

Som vi nämnt utför inte alla Batch-lösningar exakt dessa steg, och många kan innehålla fler, men det här exemplet demonstrerar vanliga processer i en Batch-lösning.

## <a name="prepare-client-script"></a>Förbereda klientskriptet
Innan du kör exemplet lägger du till autentiseringsuppgifterna för dina Batch- och Storage-konton i *python_tutorial_client.py*. Om du inte redan har gjort det öppnar du filen i din favoritredigerare och uppdaterar följande rader med dina autentiseringsuppgifter.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

Du hittar dina autentiseringsuppgifter för Batch och Storage på kontobladet för respektive tjänst på [Azure Portal][azure_portal]:

![Batch-autentiseringsuppgifter på portalen][9]
![Storage-autentiseringsuppgifter på portalen][10]<br/>

I följande avsnitt analyserar vi de steg som utförs av skripten för att bearbeta en arbetsbelastning i Batch-tjänsten. Vi rekommenderar att du följer med i skripten i redigeraren medan du gå igenom resten av artikeln.

Navigera till följande rad i **python_tutorial_client.py** för att börja med steg 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Steg 1: Skapa Storage-behållare
![Skapa behållare i Azure Storage][1]
<br/>

Batch har inbyggt stöd för integrering med Azure Storage. Behållare i ditt Storage-konto tillhandahåller de filer som behövs av aktiviteterna som körs i ditt Batch-konto. Behållarna tillhandahåller även en plats för att lagra utdata som genereras av aktiviteterna. Det första som *python_tutorial_client.py*-skriptet gör är att skapa tre behållare i [Azure Blob Storage](../storage/storage-introduction.md#blob-storage):

* **application**: Den här behållaren lagrar Python-skriptet som körs av aktiviteterna, *python_tutorial_task.py*.
* **input**: Aktiviteterna hämtar datafilerna som ska bearbetas från *input*-behållaren.
* **output**: När aktiviteterna har bearbetat indatafilen laddas resultatet upp till *output*-behållaren.

För att interagera med ett Storage-konto och skapa behållare använder vi [azure-storage][pypi_storage]-paketet för att skapa ett [BlockBlobService][py_blockblobservice]-objekt – ”blobklienten”. Därefter skapar vi tre behållare i Storage-kontot med hjälp av blobbklienten.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

När behållarna har skapats kan programmet ladda upp filerna som ska användas av aktiviteterna.

> [!TIP]
> [Använda Azure Blob Storage från Python](../storage/storage-python-how-to-use-blob-storage.md) innehåller en bra översikt över hur du arbetar med behållare och blobbar i Azure. Du hittar avsnittet nästan längst upp i din läslista när du börjar arbeta med Batch.
>
>

## <a name="step-2-upload-task-script-and-data-files"></a>Steg 2: Ladda upp aktivitetsskript och datafiler
![Ladda upp filer för aktivitetsprogram och indata till behållare][2]
<br/>

I filuppladdningsåtgärden definierar *python_tutorial_client.py* först samlingar med sökvägar till **program-** och **indatafiler** på den lokala datorn. Därefter laddas dessa filer upp till behållarna som du skapade i föregående steg.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

Med hjälp av en listkonstruktion (”list comprehension”) anropas funktionen `upload_file_to_container` för varje fil i samlingarna och två [ResourceFile][py_resource_file]-samlingar fylls. `upload_file_to_container`-funktionen visas nedan:

```python
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles
En [ResourceFile][py_resource_file] definierar aktiviteter i Batch med URL-adressen till en fil i Azure Storage som laddas ned till en beräkningsnod innan aktiviteten körs. Egenskapen [ResourceFile][py_resource_file].**blob_source** anger den fullständiga URL:en för filen i Azure Storage. URL-adressen kan även innehålla en signatur för delad åtkomst (SAS) som ger säker åtkomst till filen. De flesta aktivitetstyper i Batch har en *ResourceFiles*-egenskap, inklusive:

* [CloudTask][py_task]
* [StartTask][py_starttask]
* [JobPreparationTask][py_jobpreptask]
* [JobReleaseTask][py_jobreltask]

I det här exemplet används inte aktivitetstyperna JobPreparationTask och JobReleaseTask, men du kan läsa mer om dem i [Köra jobbförberedelse- och jobbpubliceringsaktiviteter på Azure Batch-beräkningsnoder](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Signatur för delad åtkomst (SAS)
Signaturer för delad åtkomst är strängar som ger säker åtkomst till behållare och blobbar i Azure Storage. Skriptet *python_tutorial_client.py* använder både signaturer för delad åtkomst för blobbar och behållare och visar hur du skaffar dessa strängar för signaturer för delad åtkomst från Storage-tjänsten.

* **Signaturer för delad åtkomst för blobbar**: Poolens StartTask använder signaturer för delad åtkomst för blobbar när den laddar ned aktivitetsskriptet och indatafilerna från Storage (se [steg 3](#step-3-create-batch-pool) nedan). Funktionen `upload_file_to_container` i *python_tutorial_client.py* innehåller koden som hämtar signaturen för delad åtkomst för varje blobb. Detta sker genom att [BlockBlobService.make_blob_url][py_make_blob_url] anropas i Storage-modulen.
* **Signaturer för delad åtkomst för behållare**: När aktiviteterna har utfört sitt arbete laddar de upp sina utdatafiler till *utdatabehållaren* i Azure Storage. För att göra det använder *python_tutorial_task.py* en signatur för delad åtkomst för behållare som ger skrivåtkomst till behållaren. Funktionen `get_container_sas_token` i *python_tutorial_client.py* hämtar behållarens signatur för delad åtkomst, som sedan skickas som ett kommandoradsargument till aktiviteterna. Steg 5, [Lägga till aktiviteter till ett jobb](#step-5-add-tasks-to-job), beskriver hur behållarens signatur för delad nyckel (SAS) används.

> [!TIP]
> Mer information om hur du upprättar säker åtkomst till data i ditt Storage-konto finns i serien i två delar om signaturer för delad åtkomst: [Del 1: Förstå SAS-modellen](../storage/storage-dotnet-shared-access-signature-part-1.md) och [Del 2: Skapa och använda en SAS med Blob service](../storage/storage-dotnet-shared-access-signature-part-2.md).
>
>

## <a name="step-3-create-batch-pool"></a>Steg 3: Skapa en Batch-pool
![Skapa en Batch-pool][3]
<br/>

En Batch-**pool** är en samling beräkningsnoder (virtuella datorer) där Batch utför aktiviteterna i ett jobb.

När aktivitetsskriptet och datafilerna har laddats upp till Storage-kontot börjar *python_tutorial_client.py* interagera med Batch-tjänsten genom att använda Python-modulen för Batch. För att göra det skapas en [BatchServiceClient][py_batchserviceclient]:

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

Därefter skapas en pool med beräkningsnoder i Batch-kontot med ett anrop till `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

När du skapar en pool definierar du en [PoolAddParameter][py_pooladdparam] som anger flera egenskaper för poolen:

* **ID** för adresspoolen (*id* – krävs)<p/>Som med de flesta entiteter i Batch måste din nya pool ha ett unikt ID i Batch-kontot. Din kod refererar till den här poolen med dess ID och du använder också ID:t för att identifiera poolen på Azure-[portalen][azure_portal].
* **Antal beräkningsnoder** (*target_dedicated* – krävs)<p/>Denna egenskap anger hur många virtuella datorer som ska distribueras i poolen. Det är viktigt att notera att alla Batch-konton har en **standardkvot** som begränsar antalet **kärnor** (och därför antalet beräkningsnoder) i ett Batch-konto. Du hittar standardkvoterna och instruktioner om hur du [ökar en kvot](batch-quota-limit.md#increase-a-quota) (t.ex det högsta antalet kärnor i Batch-kontot) i [Kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md). Om du undrar varför din pool inte når mer än X noder kan orsaken vara kärnkvoten.
* **Operativsystem** för noder (*virtual_machine_configuration* **eller** *cloud_service_configuration* – krävs)<p/>I *python_tutorial_client.py* skapar vi en pool med Linux-noder med en [VirtualMachineConfiguration][py_vm_config]. Funktionen `select_latest_verified_vm_image_with_node_agent_sku` i `common.helpers` gör det enklare att arbeta med avbildningar från [Azure Virtual Machines Marketplace][vm_marketplace]. Mer information om Marketplace-avbildningar finns i [Etablera Linux-beräkningsnoder i Azure Batch-pooler](batch-linux-nodes.md).
* **Storlek på beräkningsnoder** (*vm_size* – krävs)<p/>Eftersom vi anger Linux-noder för vår [VirtualMachineConfiguration][py_vm_config] anger vi en VM-storlek (`STANDARD_A1` i det här exemplet) från [Storlekar för virtuella datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Mer information finns i [Etablera Linux-beräkningsnoder i Azure Batch-pooler](batch-linux-nodes.md).
* **Startaktivitet** (*start_task* – krävs inte)<p/>Tillsammans med ovanstående fysiska nodegenskaper kan du även ange en [StartTask][py_starttask] för poolen (det är inte obligatoriskt). StartTask körs på varje nod när noden ansluter till poolen och varje gång en nod startas om. StartTask är särskilt användbart för att förbereda beräkningsnoder för körningen av aktiviteter, till exempel installationen av de program som dina aktiviteter ska köra.<p/>I det här exempelprogrammet kopierar StartTask filerna som den hämtat från Storage (som anges med startaktivitetens **resource_files**-egenskap) från StartTask-*arbetskatalogen* till den *delade* katalogen som alla aktiviteter som körs på noden kan komma åt. Vad som sker är att `python_tutorial_task.py` kopieras till den delade katalogen på varje nod när noden läggs till i poolen, så att alla aktiviteter som körs på noden kan komma åt den.

Du kanske noterar anropet till `wrap_commands_in_shell`-hjälpfunktionen. Den här funktionen använder en samling med separata kommandon och skapar en enda kommandorad för en aktivitets kommandoradsegenskap.

Något annat att notera i kodfragmentet ovan är användningen av två miljövariabler i egenskapen **command_line** för StartTask: `AZ_BATCH_TASK_WORKING_DIR` och `AZ_BATCH_NODE_SHARED_DIR`. Varje beräkningsnod i en Batch-pool konfigureras automatiskt med flera miljövariabler som är specifika för Batch. En process som körs av en aktivitet har åtkomst till dessa miljövariabler.

> [!TIP]
> Mer information om miljövariablerna som är tillgängliga på beräkningsnoder i en Batch-pool, samt information om arbetskataloger för uppgifter, finns i avsnitten **Miljöinställningar för uppgifter** och **Filer och kataloger** i [Översikt över Azure Batch-funktioner](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Steg 4: Skapa ett Batch-jobb
![Skapa ett Batch-jobb][4]<br/>

Ett Batch-**jobb** är en samling aktiviteter och associeras med en pool av beräkningsnoder. Aktiviteterna i ett jobb körs på den associerade poolens beräkningsnoder.

Förutom att använda ett jobb för att ordna och spåra uppgifter i relaterade arbetsbelastningar kan du använda det för att tillämpa vissa begränsningar, t.ex. jobbets (och därmed även aktiviteternas) maximala körtid, samt jobbprioritet i förhållande till andra jobb i Batch-kontot. I det här exemplet är jobbet dock associerat endast med poolen som skapades i steg 3. Inga ytterligare egenskaper har konfigurerats.

Alla Batch-jobb är associerade med en specifik pool. Den här associationen anger vilka noder som jobbets aktiviteter ska köras på. Du anger poolen med egenskapen [PoolInformation][py_poolinfo], som du ser i kodfragmentet nedan.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Nu när jobbet har skapats är det dags att lägga till aktiviteterna som ska utföra arbetet.

## <a name="step-5-add-tasks-to-job"></a>Steg 5: Lägga till aktiviteter till ett jobb
![Lägga till aktiviteter till ett jobb][5]<br/>
*(1) Aktiviteterna läggs till i jobbet, (2) aktiviteterna schemaläggs för att köras på noder och (3) aktiviteterna hämtar datafilerna som ska bearbetas*

Batch-**aktiviteter** är enskilda arbetsenheter som körs på beräkningsnoderna. En aktivitet har en kommandorad och kör skripten eller de körbara filer som du anger på kommandoraden.

För att arbetet ska utföras måste aktiviteter läggas till i ett jobb. Varje [CloudTask][py_task] konfigureras med hjälp av en kommandoradsegenskap och [ResourceFiles][py_resource_file] (som med poolens StartTask) som aktiviteten hämtar till noden innan dess kommandorad körs automatiskt. I exemplet bearbetar varje aktivitet endast en fil. Det betyder att dess ResourceFiles-samling innehåller ett enda element.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [!IMPORTANT]
> När de får åtkomst till miljövariabler som `$AZ_BATCH_NODE_SHARED_DIR` eller kör ett program som inte finns i nodens `PATH`, måste kommandoraderna för aktiviteter anropa gränssnittet explicit, som med `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Det här kravet är inte nödvändigt om aktiviteterna kör ett program i nodens `PATH` och inte refererar till några miljövariabler.
>
>

I loopen `for` i kodfragmentet ovan ser du att kommandoraden för aktiviteten har konstruerats med fem kommandoradsargument som skickas till *python_tutorial_task.py*:

1. **filepath**: Det här är den lokala sökvägen till filen på noden. När ResourceFile-objektet i `upload_file_to_container` skapades i steg 2 ovan användes filnamnet för den här egenskapen (`file_path`-parametern i ResourceFile-konstruktorn). Detta anger att filen finns i samma katalog på noden som *python_tutorial_task.py*.
2. **numwords**: De första *N* orden ska skrivas till utdatafilen.
3. **storageaccount**: Namnet på Storage-kontot som äger behållaren som aktivitetsresultatet ska överföras till.
4. **storagecontainer**: Namnet på Storage-behållaren som utdatafilerna ska överföras till.
5. **sastoken**: Signaturen för delad åtkomst (SAS) som ger skrivåtkomst till **utdatabehållaren** i Azure Storage. *python_tutorial_task.py*-skriptet använder den här signaturen för delad åtkomst när BlockBlobService-referensen skapas. Detta ger skrivåtkomst till behållaren utan att någon åtkomstnyckel krävs för lagringskontot.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Steg 6: Övervaka aktiviteter
![Övervaka aktiviteter][6]<br/>
*Skriptet (1) övervakar aktiviteterna tills de slutförts och (2) aktiviteterna överför resultatdata till Azure Storage*

När aktiviteter läggs till i ett jobb placeras de automatiskt i kö och schemaläggs för körning på beräkningsnoder i poolen som är associerad med jobbet. Baserat på de inställningar du anger sköter Batch all köhantering, all schemaläggning, alla omförsök och all annan aktivitetsadministration åt dig.

Du kan övervaka aktivitetskörningen på många sätt. Funktionen `wait_for_tasks_to_complete` i *python_tutorial_client.py* innehåller ett enkelt exempel på hur du övervakar aktiviteter med en viss status, i detta fall statusen [Slutförd][py_taskstate].

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Steg 7: Hämta aktivitetsutdata
![Hämta aktivitetsutdata från Storage][7]<br/>

Nu när jobbet har slutförts kan du hämta aktivietsutdata från Azure Storage. Detta görs med ett anrop till `download_blobs_from_container` i *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [!NOTE]
> Anropet till `download_blobs_from_container` i *python_tutorial_client.py* anger att filerna ska laddas ned till din hemkatalog. Ändra platsen för utdata om du vill.
>
>

## <a name="step-8-delete-containers"></a>Steg 8: Ta bort behållare
Eftersom du debiteras för data som finns i Azure Storage är det alltid en bra idé att ta bort alla blobbar som inte längre behövs för dina Batch-jobb. I *python_tutorial_client.py* görs detta med tre anrop till [BlockBlobService.delete_container][py_delete_container]:

```python
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Steg 9: Ta bort jobbet och poolen
I det sista steget uppmanas du att ta bort jobbet och poolen som skapades med *python_tutorial_client.py*-skriptet. Även om du inte debiteras för själva jobben och aktiviteterna debiteras *du* för beräkningsnoder. Vi rekommenderar därför att du endast allokerar noder efter behov. Borttagning av oanvända pooler kan ingå i din underhållsrutin.

BatchServiceClients [JobOperations][py_job] och [PoolOperations][py_pool] har båda tillhörande borttagningsmetoder, som anropas om du bekräftar borttagningen:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [!IMPORTANT]
> Tänk på att du debiteras för beräkningsresurser – du kan minimera kostnaden genom att ta bort oanvända pooler. Tänk på att om du tar bort en pool så tas alla beräkningsnoder i poolen bort. Tänk också på att data på noderna inte kan återställas när poolen har tagits bort.
>
>

## <a name="run-the-sample-script"></a>Kör exempelskriptet
När du kör *python_tutorial_client.py*-skriptet från [kodexemplet][github_article_samples] i självstudiekursen liknar konsolens utdata följande. Det är en paus vid `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` när poolens beräkningsnoder skapas, startas och när kommandona i poolens startuppgift körs. Använd [Azure Portal][azure_portal] när du vill övervaka poolen, beräkningsnoderna, jobbet och aktiviteterna under och efter körningen. Använd [Azure Portal][azure_portal] eller [Microsoft Azure Storage Explorer][storage_explorer] för att visa de lagringsresurser (behållare och blobar) som skapas av programmet.

> [!TIP]
> Kör skriptet *python_tutorial_client.py* från katalogen `azure-batch-samples/Python/Batch/article_samples`. Skriptet använder en relativ sökväg för importen av modulen `common.helpers`, så du kan eventuellt se `ImportError: No module named 'common'` om du kör skriptet från något annat ställe än den här katalogen.
>
>

Körningen tar normalt **5–7 minuter** när du kör exemplet med standardkonfigurationen.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Nästa steg
Experimentera gärna med olika beräkningsscenarier genom att göra ändringar i *python_tutorial_client.py* och *python_tutorial_task.py*. Prova till exempel att lägga till en körningsfördröjning till *python_tutorial_task.py* för att simulera långvariga aktiviteter och övervaka dem i portalen. Prova att lägga till fler aktiviteter eller att justera antalet beräkningsnoder. Lägg till logik för att söka efter och tillåta användningen av en befintlig pool som kan påskynda körningen.

Nu när du har bekantat dig med det grundläggande arbetsflödet i en Batch-lösning är det dags att titta närmare på de andra funktionerna i Batch-tjänsten.

* Läs artikeln [Översikt över Azure Batch-funktioner](batch-api-basics.md), som vi  rekommenderar om du inte har använt tjänsten.
* Sätt igång med de andra Batch-utvecklingsartiklarna under **Utveckling på djupet** i [utbildningsvägen för Batch][batch_learning_path].
* Ta en titt på en annan implementering av arbetsbelastningen ”översta N orden” med hjälp av Batch i [TopNWords][github_topnwords]-exemplet.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage
[pypi_install]: https://packaging.python.org/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Skapa behållare i Azure Storage"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Ladda upp filer för aktivitetsprogram och indata till behållare"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Skapa en Batch-pool"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Skapa ett Batch-jobb"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Lägga till aktiviteter till ett jobb"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Övervaka aktiviteter"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Hämta aktivitetsutdata från Storage"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Arbetsflödet i en Batch-lösning (fullständigt diagram)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Batch-autentiseringsuppgifter på portalen"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Storage-autentiseringsuppgifter på portalen"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Arbetsflödet i en Batch-lösning (minimalt diagram)"

