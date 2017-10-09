---
title: Skapa en Azure-datafabrik med Python | Microsoft Docs
description: "Skapa en Azure-datafabrik för att kopiera data från en plats i Azure Blob Storage till en annan plats i samma Blob Storage."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9c6f7c8e052aed2ab5aba2ee54065bf40e81a022
ms.contentlocale: sv-se
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-data-factory-and-pipeline-using-python"></a>Skapa en datafabrik och pipeline med Python
Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda. 

Den här snabbstarten beskriver hur du använder Python till att skapa en Azure-datafabrik. Pipeline i den här datafabriken kopierar data från en mapp till en annan i Azure Blob Storage.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* **Azure Storage-konto**. Du kan använda blob-lagringen som **källa** och **mottagare** för datalagringen. Om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) . 
* **Skapa en app i Azure Active Directory** med hjälp av [den här instruktionen](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Notera följande värden som du använder i senare steg: **program-ID**, **autentiseringsnyckel** och **klient-ID**. Tilldela program till rollen **Deltagare** enligt instruktionerna i samma artikel. 

### <a name="create-and-upload-an-input-file"></a>Skapa och ladda upp en indatafil

1. Öppna Anteckningar. Kopiera följande text och spara den som **input.txt** på disken.
    
    ```
    John|Doe
    Jane|Doe
    ```
2.  Använd verktyg som t.ex. [Azure Lagringsutforskaren](http://storageexplorer.com/) till att skapa behållaren **adfv2tutorial** och för att ladda upp filen input.txt till behållaren. 

## <a name="install-the-python-package"></a>Installera Python-paketet
1. Öppna en terminal eller kommandotolk med administratörsbehörighet.  
2. Om du vill installera Python-paketet för Data Factory kör du följande kommando:

    ```
    pip install azure-mgmt-datafactory
    ```

    [Python SDK för Data Factory](https://github.com/Azure/azure-sdk-for-python) stöder Python 2.7, 3.3, 3.4, 3.5 och 3.6.
## <a name="create-a-data-factory-client"></a>Skapa en datafabriksklient

1. Skapa en fil med namnet **datafactory.py**. Lägg till följande instruktioner för att lägga till referenser till namnområden.
    
    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from msrestazure.azure_cloud import Cloud, CloudEndpoints, CloudSuffixes
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    import json
    import time    
    ```
2. Lägg till följande funktioner som skriver ut information. 

    ```python
    def print_item(group):
      """Print an Azure object instance."""
      print("\tName: {}".format(group.name))
      print("\tId: {}".format(group.id))
      if hasattr(group, 'location'):
          print("\tLocation: {}".format(group.location))
      if hasattr(group, 'tags'):
          print("\tTags: {}".format(group.tags))
      if hasattr(group, 'properties'):
          print_properties(group.properties)
    
    def print_properties(props):
      """Print a ResourceGroup properties instance."""
      if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
          print("\tProperties:")
          print("\t\tProvisioning State: {}".format(props.provisioning_state))
      print("\n\n")    
    ```
3. Lägg till följande kod till **Main**-metoden som skapar en instans av klassen DataFactoryManagementClient. Du använde det är objektet till att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Du kan också använda det här objektet för att övervaka information om pipelinekörning.

    ```python   
    subscription_id = '<your subscription ID where the factory resides>'
    credentials = ServicePrincipalCredentials(
            client_id='<yourClientId>',
            secret='<YourPassword>',
            tenant='<YourTenandId>'
    )
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)
    
    rg_params = {'location':'eastus'}
    df_params = {'location':'eastus'}
    rg_name = '<Your Resource Group Name>'
    df_name = '<Your Data Factory Name>'
    ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Lägg till följande kod som skapar en **datafabrik** till **Main**-metoden. 

```python
#Create Resource Group
resource_client.resource_groups.create_or_update(rg_name, rg_params)

#Create Data Factory
df_resource = Factory(location='eastus')
df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
print_item(df)
while df.provisioning_state != 'Succeeded':
    df = adf_client.factories.get(rg_name, df_name)
    time.sleep(1)
print_item(adf_client.factories.get(rg_name, df_name))
```

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst

Lägg till följande kod som skapar en **länkad Azure Storage-tjänst** till **Main**-metoden.

Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här snabbstarten behöver du bara skapa en Azure Storage-länkad tjänst som både kopia på källa och mottagarlagring, med namnet "AzureStorageLinkedService" i exemplet.

```python
#Create Storage Linked Service
ls_name = 'storageLinkedService'

#Replace Storage String with your credentials
storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<replace>;AccountKey=<replace>')

ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
print_item(ls)
```
## <a name="create-datasets"></a>Skapa datauppsättningar
I det här avsnittet skapar du två datauppsättningar: en för källan och en för mottagaren.

### <a name="create-a-dataset-for-source-azure-blob"></a>Skapa en datauppsättning för Azure Blob-källan
Lägg till följande kod till Main-metoden som skapar en Azure-blobdatauppsättning. Mer information om de här Azure-blobegenskaperna finns i artikeln [Azure Blob-anslutningsapp](connector-azure-blob-storage.md#dataset-properties). 

Du definierar en datauppsättning som representerar källdata i Azure Blob. Denna Blob-datauppsättning refererar till den Azure Storage-länkade tjänst som du skapar i föregående steg.

```python
#Create Dataset Input
ds_name = 'ds_in'
ds_ls = LinkedServiceReference(ls_name)
blob_path= 'adfv2tutorial/'
blob_filename = 'input.txt'
ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Skapa en datauppsättning för Azure Blob-mottagaren
Lägg till följande kod till Main-metoden som skapar en Azure-blobdatauppsättning. Mer information om de här Azure-blobegenskaperna finns i artikeln [Azure Blob-anslutningsapp](connector-azure-blob-storage.md#dataset-properties). 

Du definierar en datauppsättning som representerar källdata i Azure Blob. Denna Blob-datauppsättning refererar till den Azure Storage-länkade tjänst som du skapar i föregående steg.

```python
#Create Dataset Output
dsOut_name = 'ds_out'
output_blobpath = 'output/'
dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Skapa en pipeline

Lägg till följande kod som skapar och **aktiverar en pipeline** till **Main**-metoden.

```python
#Create 1st activity: Copy Activity
act_name =  'copyBlobtoBlob'
blob_source = BlobSource()
blob_sink = BlobSink()
dsin_ref = DatasetReference(ds_name)
dsOut_ref = DatasetReference(dsOut_name)
copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

#Create Pipeline
p_name =  'copyPipeline'
params_for_pipeline = {}
p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
print_item(p)
```


## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning

Lägg till följande kod i **Main**-metoden som **utlöser en pipelinekörning**.

```python
#Create Pipeline Run
print(adf_client.pipelines.create_run(rg_name, df_name, p_name,
    {
    }
))
```

## <a name="run-the-code"></a>Kör koden
Skapa och starta programmet och kontrollera sedan pipelinekörningen.

Konsolen skriver ut förloppet för skapandet av datafabriken, den länkade tjänsten, datauppsättningar, pipeline och pipelinekörning. Vänta tills du ser information om körningen av kopieringsaktiviteten med storlek för lästa/skrivna data. Använd sedan verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att kontrollera de blob(ar) som kopierats till outputBlobPath från inputBlobPath som du angav i variablerna.


## <a name="clean-up-resources"></a>Rensa resurser
För att programmässigt ta bort datafabriken lägger du till följande rader med kod till programmet: 

```csharp
adf_client.data_factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
