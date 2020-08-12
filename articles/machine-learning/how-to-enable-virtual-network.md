---
title: Nätverks isolering & sekretess
titleSuffix: Azure Machine Learning
description: Använd en isolerad Azure-Virtual Network med Azure Machine Learning för att skydda experiment/utbildning samt jobb för att begränsa/göra poäng.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121212"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>Nätverks isolering under utbildning och härledning med privata slut punkter och virtuella nätverk
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skyddar dina Machine Learning-livscykler genom att isolera Azure Machine Learning utbildning och jobb härlednings jobb i ett Azure-Virtual Network (VNet). Ett __virtuellt nätverk__ fungerar som en säkerhets gränser som isolerar dina Azure-resurser från det offentliga Internet. Du kan också ansluta ett virtuellt Azure-nätverk till ditt lokala nätverk. Genom att ansluta till nätverk kan du på ett säkert sätt träna dina modeller och komma åt dina distribuerade modeller för att få en mer härledning.

Azure Machine Learning arbets ytan kan nås från ett virtuellt nätverk med en __privat slut punkt__. Den privata slut punkten är en uppsättning privata IP-adresser i ditt virtuella nätverk, och åtkomst till din arbets yta är begränsad till det virtuella nätverket. Den privata slut punkten hjälper till att minska risken för data exfiltrering. Mer information om privata slut punkter finns i artikeln [Azure Private Link](/azure/private-link/private-link-overview) .

> [!NOTE]
> Du kan stöta på problem med att komma åt en arbets yta via en privat slut punkt när du använder Mozilla Firefox. Problemet kan vara relaterat till inställningen DNS över HTTPS i webbläsaren. Vi rekommenderar att du använder Microsoft Edge eller Google Chrome för att undvika det här problemet.

Azure Machine Learning använder andra Azure-tjänster för data lagring och beräknings resurser (används för att träna och distribuera modeller). Dessa resurser kan också skapas i ett virtuellt nätverk. Du kan till exempel använda Azure Machine Learning Compute för att träna en modell och sedan distribuera modellen till Azure Kubernetes service (AKS). 

## <a name="prerequisites"></a>Förutsättningar

+ En Azure Machine Learning- [arbetsyta](how-to-manage-workspace.md).

+ Allmänt fungerande kunskap om både [Azure Virtual Network-tjänsten](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) och [IP-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Ett befintligt virtuellt nätverk och undernät som ska användas med dina beräknings resurser.

+ För att distribuera resurser till ett virtuellt nätverk eller undernät måste ditt användar konto ha behörighet till följande åtgärder i Azure-rollbaserade åtkomst kontroller (RBAC):

    - "Microsoft. Network/virtualNetworks/Join/Action" på den virtuella nätverks resursen.
    - "Microsoft. Network/virtualNetworks/Subnet/Join/Action" på under näts resursen.

    Mer information om RBAC med nätverk finns i [inbyggda nätverks roller](/azure/role-based-access-control/built-in-roles#networking)

## <a name="secure-your-workspace"></a>Skydda din arbets yta

Din Azure Machine Learning arbets yta kan antingen ha en __offentlig slut punkt__ eller en __privat slut punkt__. En offentlig slut punkt är en uppsättning IP-adresser som är tillgängliga på det offentliga Internet, medan en privat slut punkt är en uppsättning privata IP-adresser i ett virtuellt nätverk. 

Använd en privat slut punkt om du vill begränsa åtkomsten till din arbets yta enbart för att ske över de privata IP-adresserna.

Eftersom kommunikation till arbets ytan endast tillåts från det virtuella nätverket måste alla utvecklings miljöer som använder arbets ytan vara medlemmar i det virtuella nätverket. Till exempel en virtuell dator i det virtuella nätverket.

> [!IMPORTANT]
> Den privata slut punkten påverkar inte Azures kontroll plan (hanterings åtgärder) som att ta bort arbets ytan eller hantera beräknings resurser. Till exempel skapa, uppdatera eller ta bort ett beräknings mål. De här åtgärderna utförs via det offentliga Internet som normalt.
>
> Den privata slut punkten förhindrar åtkomst till arbets ytan utanför det virtuella nätverket.

Vissa kombinationer av resurser med en privat slut punkt kräver en Enterprise Edition-arbetsyta. Använd följande tabell för att ta reda på vilka scenarier som kräver Enterprise Edition:

| Scenario | Stora företag</br>Edition | Grundläggande</br>Edition |
| ----- |:-----:|:-----:| 
| Inget virtuellt nätverk eller en privat slut punkt | ✔ | ✔ |
| Arbets ytan saknar privat slut punkt. Andra resurser (utom Azure Container Registry) i ett virtuellt nätverk | ✔ | ✔ |
| Arbets ytan saknar privat slut punkt. Andra resurser med privat slut punkt | ✔ | |
| Arbets yta med privat slut punkt. Andra resurser (utom Azure Container Registry) i ett virtuellt nätverk | ✔ | ✔ |
| Arbets yta och andra resurser med privat slut punkt | ✔ | |
| Arbets yta med privat slut punkt. Andra resurser utan privat slut punkt eller virtuellt nätverk | ✔ | ✔ |
| Azure Container Registry i ett virtuellt nätverk | ✔ | |
| Kundhanterade nycklar för arbets ytan | ✔ | |

> [!WARNING]
> 
> Azure Machine Learning beräknings instanser stöds inte i en arbets yta där privat slut punkt är aktive rad.
>
> Azure Machine Learning stöder inte användning av en Azure Kubernetes-tjänst som har privat slut punkt aktive rad. I stället kan du använda Azure Kubernetes-tjänsten i ett virtuellt nätverk. Mer information finns i [skydda Azure ml-experimentering och härlednings jobb i en Azure-Virtual Network](how-to-enable-virtual-network.md).

Mer information om privata slut punkter i Azure finns i artikeln [Azure Private Link](/azure/private-link/private-link-overview) .

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Skapa en arbets yta som använder en privat slut punkt

Du kan skapa en ny arbets yta med en privat slut punkt med hjälp av Azure Machine Learning SDK, CLI, en Azure Resource Manager mall eller Azure Portal.

__Krav__

* Nätverks principerna måste vara inaktiverade för det virtuella nätverk som du använder med den privata slut punkten. Mer information finns i [inaktivera nätverks principer för en privat slut punkt](/azure/private-link/disable-private-endpoint-network-policy).

__Begränsningar__

* Du kan inte ansluta till arbets ytan via det offentliga Internet, bara inifrån det virtuella nätverket.

* Om flera arbets ytor skapas med hjälp av privata slut punkter och de använder samma privata DNS-zon, läggs bara den första arbets ytan till i de __virtuella nätverks länkarna__ i den privata DNS-zonen.

    Undvik den här begränsningen genom att manuellt lägga till den virtuella nätverks länken för ytterligare arbets ytor. Mer information finns i [Vad är en virtuell nätverks länk](/azure/dns/private-dns-virtual-network-links).

__Konfiguration__

Information om hur du skapar en arbets yta med ett virtuellt nätverk och en privat slut punkt, tillsammans med andra konfigurations alternativ, finns i följande artiklar:

* [Använd en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning](how-to-create-workspace-template.md).
* [Skapa arbets ytor i portalen](how-to-manage-workspace.md).
* [Skapa arbets ytor med Azure CLI](how-to-manage-workspace-cli.md).
* Om du vill använda python SDK går du till [PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py) och [arbets ytan. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) Reference Documentation ().

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

__Krav__

* Om du vill komma åt data i ett lagrings konto måste lagrings kontot finnas i samma virtuella nätverk som arbets ytan.

* Om dina data lagras i ett virtuellt nätverk måste du använda en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för arbets ytan för att ge Studio åtkomst till dina data.

    > [!IMPORTANT]
    > Även om de flesta Studio fungerar med data som lagras i ett virtuellt nätverk gör integrerade antecknings böcker __inte__det. Integrerade antecknings böcker stöder inte användning av lagring som finns i ett virtuellt nätverk. I stället kan du använda Jupyter-anteckningsböcker från en beräknings instans. Mer information finns i avsnittet [Compute clusters & instances](#compute-instance) .

    Om du inte ger åtkomst till Studio får du ett fel meddelande om att `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` följande åtgärder inte är tillgängliga:

    * Förhandsgranska data i Studio.
    * Visualisera data i designern.
    * Skicka ett AutoML experiment.
    * Starta ett etikettande projekt.

__Begränsningar__

* Azure Machine Learning Studio stöder läsning av data från följande data lager typer i ett virtuellt nätverk:

    * Azure-blobb
    * Azure Data Lake Storage Gen1
    * Azure Data Lake Storage Gen2
    * Azure SQL Database

__Konfiguration__

* __Konfigurera data lager för att använda en hanterad identitet__ för att komma åt dina data. De här stegen lägger till den hanterade identiteten för arbets ytan som en __läsare__ till lagrings tjänsten med hjälp av Azure Resource-baserad åtkomst kontroll (RBAC). Med __läsar__ åtkomst kan arbets ytan Hämta brand Väggs inställningar och se till att data inte lämnar det virtuella nätverket.

    1. I Studio väljer du __data lager__.

    1. Om du vill skapa ett nytt data lager väljer du __+ nytt data lager__. Om du vill uppdatera en befintlig väljer du data lagret och väljer __uppdatera autentiseringsuppgifter__.

    1. I data lager inställningarna väljer du __Ja__ för __Tillåt Azure Machine Learning-tjänst för att få åtkomst till lagringen med hanterad identitet för arbets ytan__.

    > [!NOTE]
    > Det kan ta upp till 10 minuter innan ändringarna börjar gälla.

* För __Azure Blob Storage__måste du även lägga till arbets ytans hanterade identitet som en [BLOB-datakälla](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) så att den kan läsa data från Blob Storage.

* Designern använder det lagrings konto som är kopplat till din arbets yta för att lagra utdata som standard. Du kan dock ange att den ska lagra utdata till alla data lager som du har åtkomst till. Om din miljö använder virtuella nätverk kan du använda dessa kontroller för att säkerställa att dina data är skyddade och tillgängliga. Ange ett nytt standard lagrings utrymme för en pipeline:

    1. I ett pipeline-utkast väljer du **kugg hjuls ikonen Inställningar** nära rubriken för din pipeline.
    1. Välj posten **Välj standard data lager** .
    1. Ange ett nytt data lager.

    Du kan även åsidosätta standard data lagret per modul. Detta ger dig kontroll över lagrings platsen för varje enskild modul.

    1. Välj den modul vars utdata du vill ange.
    1. Expandera avsnittet **utdata-inställningar** .
    1. Välj **Åsidosätt standardinställningar för utdata**.
    1. Välj **Ange inställningar för utdata**.
    1. Ange ett nytt data lager.

* Om du använder __Azure Data Lake Storage Gen2__kan du använda både RBAC-och POSIX-typ åtkomst kontrol listor (ACL: er) för att styra data åtkomsten i ett virtuellt nätverk.

    Om du vill använda RBAC lägger du till arbets ytans hanterade identitet i rollen [BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Mer information finns i [Rollbaserad åtkomstkontroll](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

    För att kunna använda ACL: er kan hanterade arbets ytans identitet tilldelas åtkomst precis som andra säkerhets principer. Mer information finns i [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

* __Azure Data Lake Storage gen1__ stöder endast åtkomst kontrol listor med POSIX-typ. Du kan tilldela arbets ytan hanterad identitets åtkomst till resurser precis som vilken annan säkerhets princip som helst. Mer information finns i [åtkomst kontroll i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md).

* Om du vill komma åt data som lagras i en __Azure SQL Database__ med hjälp av hanterad identitet måste du skapa en SQL-innesluten användare som mappar till den hanterade identiteten Mer information om hur du skapar en användare från en extern provider finns i [skapa inneslutna användare som är mappade till Azure AD-identiteter](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

    När du har skapat en SQL-innesluten användare beviljar du behörigheter till den med hjälp av [kommandot bevilja T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

* Om du __ansluter till Studio från en resurs i ett virtuellt nätverk__ (till exempel en beräknings instans eller virtuell dator) måste du tillåta utgående trafik från det virtuella nätverket till Studio. 

    Om du till exempel använder nätverks säkerhets grupper (NSG) för att begränsa utgående trafik, lägger du till en regel till ett __service tag-__ mål för __AzureFrontDoor. frontend__.

## <a name="use-datastores-and-datasets"></a>Använda data lager och data uppsättningar

> [!NOTE]
> I det här avsnittet beskrivs användningen av data lager och data uppsättningar för SDK-upplevelsen. Mer information om Studio-upplevelsen finns i avsnittet [Machine Learning Studio](#machine-learning-studio).

__Begränsningar__

Som standard utför Azure Machine Learning data giltighet och autentiseringsuppgifter vid försök att komma åt data med hjälp av SDK. Om dina data ligger bakom ett virtuellt nätverk kan Azure Machine Learning inte komma åt data och kontrollerna kan inte utföras. Undvik det här problemet genom att hoppa över validering när du skapar data uppsättningar och data uppsättningar.

* När du använder ett __data lager__:

    Azure Data Lake Store gen1 och Azure Data Lake Store Gen2 hoppa över validering som standard, så ingen ytterligare åtgärd krävs. För följande tjänster kan du dock använda liknande syntax för att hoppa över validering av data lager:

    - Azure Blob Storage
    - Azure-fileshare
    - PostgreSQL
    - Azure SQL Database

    I följande kod exempel skapas ett nytt Azure Blob-datalager och uppsättningar `skip_validation=True` .

    ```python
    blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  
                                                            datastore_name=blob_datastore_name,  
                                                            container_name=container_name,  
                                                            account_name=account_name, 
                                                            account_key=account_key, 
                                                            skip_validation=True ) // Set skip_validation to true
    ```

* När du använder en __data uppsättning__:

    Syntaxen för att hoppa över data uppsättnings verifiering är liknande för följande typer av data uppsättningar:
    - Avgränsad fil
    - JSON 
    - Parquet
    - SQL
    - Fil

    Följande kod skapar en ny JSON-datauppsättning och uppsättningar `validate=False` .

    ```python
    json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 
                                                   validate=False) 
    ```

## <a name="azure-storage-account"></a>Azure-lagringskonto

> [!IMPORTANT]
> Du kan placera både _standard lagrings kontot_ för Azure Machine Learning och _icke-standardlagrings konton_ i ett virtuellt nätverk.

__Krav__

* Lagrings kontot måste finnas i samma virtuella nätverk och undernät som de beräknings instanser eller kluster som används för utbildning eller härledning.

__Konfiguration__

Om du vill skydda det Azure Storage kontot som används av din arbets yta, aktiverar du antingen en __privat slut punkt__ eller en __tjänst slut punkt__ för lagrings kontot i det virtuella nätverket.

* Information om hur du konfigurerar lagrings kontot för att använda en __privat slut punkt__finns i artikeln [använda privata slut punkter](/azure/storage/common/storage-private-endpoints.md) .

* Gör så här om du vill konfigurera lagrings kontot så att det använder en __tjänst slut punkt__:

    1. Om du vill lägga till lagrings kontot i det virtuella nätverket som används av din arbets yta använder du informationen i artikeln __bevilja åtkomst från ett virtuellt nätverk__ i artikeln [Konfigurera Azure Storage brand väggar och virtuella nätverk](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) .
    1. Om du vill tillåta åtkomst från Microsoft-tjänster på det virtuella nätverket (till exempel Azure Machine Learning) använder du informationen i avsnittet __undantag__ i artikeln [Konfigurera Azure Storage brand väggar och virtuella nätverk](/azure/storage/common/storage-network-security#exceptions) .
    1. När du arbetar med Azure Machine Learning SDK måste utvecklings miljön kunna ansluta till Azure Storage-kontot. När lagrings kontot finns i ett virtuellt nätverk måste brand väggen tillåta åtkomst från utvecklings miljöns IP-adress. Om du vill lägga till IP-adressen för utvecklings miljön använder du informationen i artikeln __bevilja åtkomst från ett IP-adressintervall__ i avsnittet [Konfigurera Azure Storage brand väggar och virtuella nätverk](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range) .

## <a name="azure-container-registry"></a>Azure Container Registry

__Krav__

* Din Azure Machine Learning-arbetsyta måste vara Enterprise Edition. Information om hur du uppgraderar finns i [Uppgradera till Enterprise Edition](how-to-manage-workspace.md#upgrade).
* Din Azure Machine Learning arbets ytans region bör vara en [privat länk aktive rad region](https://docs.microsoft.com/azure/private-link/private-link-overview#availability). 
* Din Azure Container Registry måste vara en Premium version. Mer information om hur du uppgraderar finns i [ändra SKU: er](/azure/container-registry/container-registry-skus#changing-skus).
* Ditt Azure Container Registry måste finnas i samma virtuella nätverk och undernät som lagrings kontot och beräknings målen som används för utbildning eller härledning.
* Din Azure Machine Learning-arbetsyta måste innehålla ett [Azure Machine Learning Compute-kluster](how-to-set-up-training-targets.md#amlcompute).

__Begränsningar__

* När ACR ligger bakom ett virtuellt nätverk kan Azure Machine Learning inte använda det för att direkt bygga Docker-avbildningar. I stället används beräknings klustret för att bygga avbildningarna.

__Konfiguration__

1. Använd någon av följande metoder för att hitta namnet på Azure Container Registry för din arbets yta:

    __Azure-portalen__

    I översikts avsnittet på arbets ytan länkar __registervärdet__ till Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry för arbets ytan" border="true":::

    __Azure CLI__

    Om du har [installerat Machine Learning-tillägget för Azure CLI](reference-azure-machine-learning-cli.md)kan du använda `az ml workspace show` kommandot för att visa information om arbets ytan.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Det här kommandot returnerar ett värde som liknar `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Den sista delen av strängen är namnet på Azure Container Registry för arbets ytan.

1. Du begränsar åtkomsten till ditt virtuella nätverk genom att följa stegen i [Konfigurera nätverks åtkomst för registret](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). När du lägger till det virtuella nätverket väljer du det virtuella nätverket och under nätet för dina Azure Machine Learning-resurser.

1. Använd Azure Machine Learning python SDK för att konfigurera ett beräknings kluster för att bygga Docker-avbildningar. Följande kodfragment visar hur du gör detta:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Ditt lagrings konto, beräknings kluster och Azure Container Registry måste finnas i samma undernät i det virtuella nätverket.
    
    Mer information finns i metod referensen [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

1. Använd följande Azure Resource Manager mall för att aktivera arbets ytan för att kommunicera med ACR-instansen:

    > [!WARNING]
    > Med den här mallen kan du använda en privat slut punkt för din arbets yta och ändra den till en företags arbets yta. Du kan inte ångra de här ändringarna.

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

## <a name="key-vault-instance"></a>Key Vault-instans 

__Krav__

__Begränsningar__

__Konfiguration__ 

Om du vill använda Azure Machine Learning experiment funktioner med Azure Key Vault bakom ett virtuellt nätverk använder du artikeln [konfigurera Azure Key Vault brand väggar och virtuella nätverk](/azure/key-vault/general/network-security) .

> [!IMPORTANT]
> När du följer stegen i artikeln använder du samma virtuella nätverk som det som används av experimentering-beräknings resurserna. Du måste också __tillåta att betrodda Microsoft-tjänster kringgår den här brand väggen__.

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Beräknings kluster & instanser 

__Krav__

* Det virtuella nätverket måste finnas i samma prenumeration och region som Azure Machine Learning-arbetsytan.
* Under nätet som anges för beräknings instansen eller klustret måste ha tillräckligt många otilldelade IP-adresser för att rymma antalet virtuella datorer som är riktade. Om under nätet inte har tillräckligt med otilldelade IP-adresser, tilldelas ett beräknings kluster delvis.
* Om du planerar att skydda det virtuella nätverket genom att begränsa trafiken måste vissa portar lämnas öppna för beräknings tjänsten.
* Om du ska lagra flera beräknings instanser eller kluster i ett virtuellt nätverk kan du behöva begära en kvot ökning för en eller flera av dina resurser.
* Om Azure Storage kontona för arbets ytan också är skyddade i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som Azure Machine Learning beräknings instans eller kluster. 
* För att Compute instance Jupyter-funktionen ska fungera kontrollerar du att WebSocket-kommunikation inte är inaktiverat.

__Begränsningar__

* Machine Learning beräknings instans eller kluster allokerar automatiskt ytterligare nätverks resurser __i resurs gruppen som innehåller det virtuella nätverket__. För varje beräknings instans eller kluster allokerar tjänsten följande resurser:

    * En nätverks säkerhets grupp
    * En offentlig IP-adress
    * En belastningsutjämnare
    
    För __beräknings kluster__tas dessa resurser bort (och återskapas) varje gång klustret skalar ned till 0 noder.
    
    För en __beräknings instans__ placeras resurserna på tills instansen tas bort (att stoppa tar inte bort resurserna).

    Dessa resurser begränsas av prenumerationens [resurskvoter](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

__Konfiguration__

* Använd följande steg för att skapa ett Machine Learning-beräkning kluster:

    1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/)och välj sedan din prenumeration och arbets yta.
    1. Välj __Beräkna__ till vänster.
    1. Välj __utbildnings kluster__ från mitten och välj sedan __+__ .
    1. I dialog rutan __nytt utbildnings kluster__ expanderar du avsnittet __Avancerade inställningar__ .
    1. Om du vill konfigurera den här beräknings resursen för att använda ett virtuellt nätverk utför du följande åtgärder i avsnittet __Konfigurera virtuellt nätverk__ :

        1. I list rutan __resurs grupp__ väljer du den resurs grupp som innehåller det virtuella nätverket.
        1. I list rutan __virtuellt nätverk__ väljer du det virtuella nätverk som innehåller under nätet.
        1. I list rutan __undernät__ väljer du det undernät som ska användas.

    ![Inställningarna för virtuella nätverk för Machine Learning-beräkning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

    Du kan också skapa ett Machine Learning-beräkning-kluster med hjälp av Azure Machine Learning SDK. Följande kod skapar ett nytt Machine Learning-beräkning-kluster i `default` under nätet för ett virtuellt nätverk med namnet `mynetwork` :

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

    [!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

* Om du använder antecknings böcker på en Azure-beräknings instans måste du se till att din bärbara dator körs på en beräknings resurs bakom samma virtuella nätverk och undernät som dina data. 

    Konfigurera beräknings instansen så att den är i samma virtuella nätverk under skapandet av **Avancerade inställningar**  >  **Konfigurera virtuellt nätverk**. Det går inte att lägga till en befintlig beräknings instans i ett virtuellt nätverk.

* Om du planerar att skydda det virtuella nätverket genom att begränsa nätverks trafiken till/från det offentliga Internet måste du tillåta inkommande kommunikation från tjänsten Azure Batch.

    Batch-tjänsten lägger till nätverks säkerhets grupper (NSG: er) på nivån nätverks gränssnitt (NIC) som är anslutna till virtuella datorer. De här NSG:erna konfigurerar automatiskt regler för inkommande och utgående trafik för att tillåta följande trafik:

    - Inkommande TCP-trafik på portarna 29876 och 29877 från en __service tag__ i __BatchNodeManagement__.

    - Valfritt Inkommande TCP-trafik på port 22 för att tillåta fjärråtkomst. Använd bara den här porten om du vill ansluta med SSH på den offentliga IP-adressen.

    - Utgående trafik på vilken port som helst till det virtuella nätverket.

    - Utgående trafik på vilken port som helst till Internet.

    - För Compute instance inkommande TCP-trafik på port 44224 från en __service tag__ i __AzureMachineLearning__.

    > [!IMPORTANT]
    > Var försiktig om du ändrar eller lägger till regler för inkommande eller utgående trafik i Batch-konfigurerade NSG:er. Om en NSG blockerar kommunikation till datornoderna, anger beräknings tjänsten status för datornoderna till oanvändbar.
    >
    > Du behöver inte ange NSG: er på under näts nivån, eftersom Azure Batch tjänsten konfigurerar sin egen NSG: er. Men om det undernät som innehåller Azure Machine Learning Compute har tillhör ande NSG: er eller en brand vägg, måste du också tillåta trafiken som anges ovan.

* Använd följande steg om du inte vill använda de utgående standard reglerna och du vill begränsa den utgående åtkomsten för ditt virtuella nätverk:

    1. Neka utgående Internet anslutning med NSG-reglerna.
    1. För en __beräknings instans__ eller ett __beräknings kluster__begränsar du utgående trafik till följande objekt:
        - Azure Storage med hjälp av __tjänst tag gen__ för __Storage. RegionName__. Där `{RegionName}` är namnet på en Azure-region.
        - Azure Container Registry med hjälp av __service tag gen__ för __AzureContainerRegistry. RegionName__. Där `{RegionName}` är namnet på en Azure-region.
        - Azure Machine Learning med hjälp av __service tag gen__ för __AzureMachineLearning__
        - Azure Resource Manager med hjälp av __service tag gen__ för __AzureResourceManager__
        - Azure Active Directory med hjälp av __service tag gen__ för __AzureActiveDirectory__

    > [!NOTE]
    > Om du planerar att använda standard Docker-avbildningar som tillhandahålls av Microsoft och aktiverar hanterade beroenden, måste du också använda följande __service märken__:
    >
    > * __MicrosoftContainerRegistry__
    > * __AzureFrontDoor.FirstParty__
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

* Om du använder [Tvingad tunnel trafik](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) med Azure Machine Learning Compute måste du tillåta kommunikation med det offentliga Internet från det undernät som innehåller beräknings resursen. Den här kommunikationen används för schemaläggning av aktiviteter och åtkomst till Azure Storage.

    Du kan göra detta på två sätt:

    * Använd en [Virtual Network NAT](../virtual-network/nat-overview.md). En NAT-gateway ger utgående Internet anslutning för ett eller flera undernät i det virtuella nätverket. Mer information finns i [utforma virtuella nätverk med NAT-gateway-resurser](../virtual-network/nat-gateway-resource.md).

    * Lägg till [användardefinierade vägar (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) till det undernät som innehåller beräknings resursen. Upprätta en UDR för varje IP-adress som används av tjänsten Azure Batch i den region där dina resurser finns. Dessa UDR gör att batch-tjänsten kan kommunicera med datornoder för schemaläggning av aktiviteter. Lägg också till IP-adressen för den Azure Machine Learning tjänst där resurserna finns, eftersom detta krävs för åtkomst till beräknings instanser. Använd någon av följande metoder för att hämta en lista över IP-adresser för batch-tjänsten och Azure Machine Learning tjänsten:

        * Hämta [Azure IP-intervall och service märken](https://www.microsoft.com/download/details.aspx?id=56519) och Sök efter `BatchNodeManagement.<region>` och `AzureMachineLearning.<region>` , där `<region>` är din Azure-region.

        * Använd [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att hämta informationen. I följande exempel hämtas IP-adress informationen och filtreras bort informationen för regionen USA, östra 2:

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        När du lägger till UDR definierar du vägen för varje relaterat batch-IP-adressprefix och anger __nästa hopp typ__ till __Internet__. 

        Förutom de UDR som du definierar måste utgående trafik till Azure Storage tillåtas via den lokala nätverks enheten. Mer specifikt är URL: erna för den här trafiken i följande format: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` och `<account>.blob.core.windows.net` . 

        Mer information finns i [skapa en Azure Batch pool i ett virtuellt nätverk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

__Krav__

* Azure Kubernetes service-instansen (AKS) och det virtuella Azure-nätverket måste finnas i samma region. Om du skyddar de Azure Storage-konton som används av arbets ytan i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som AKS-instansen.

* Om du vill __planera IP-adresser__ för klustret följer du kraven i artikeln [Konfigurera avancerade nätverk i Azure KUBERNETES service (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) .

* Om du vill begränsa inkommande och utgående kommunikation till AKS-klustret, följer du anvisningarna i artikeln kontroll av utgående [trafik för klusternoder i Azure Kubernetes service](/azure/aks/limit-egress-traffic) artikel för att se till att _utgående_ kommunikation från AKS har kon figurer ATS korrekt. Eventuella krav för _inkommande_ kommunikation visas i avsnittet konfiguration nedan.

__Begränsningar__

* Om du vill använda en Azure Kubernetes-tjänst som har privat länk aktive rad måste du koppla den till din arbets yta. Du kan inte skapa ett Azure Kubernetes service-kluster med en privat länk från Azure Machine Learning (SDK, Portal, CLI osv.).

__Konfiguration__

> [!IMPORTANT]
> Det här avsnittet innehåller flera konfigurationer. Välj den som passar bäst för dina behov.

* __Använda AKS bakom det virtuella nätverket med en offentlig belastningsutjämnare__:

    1. Skapa eller koppla AKS-klustret. Om du __skapar__ ett nytt kluster måste du ange det virtuella nätverk som ska användas för klustret.
    
        Följande exempel visar hur du skapar ett nytt AKS-kluster med python SDK:

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

        Om du har ett befintligt AKS-kluster som redan ligger bakom det virtuella nätverket kan du använda informationen i artikeln [distribuera till Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Kontrol lera att den nätverks säkerhets grupp som styr det virtuella nätverket har en __inkommande__ säkerhets regel aktive rad för poäng slut punkten så att den kan anropas från utanför det virtuella nätverket.

* Så här __använder du AKS bakom det virtuella nätverket med en privat belastningsutjämnare__:

    1. Skapa eller koppla AKS-klustret. Om du __skapar__ ett nytt kluster måste du ange det virtuella nätverk som ska användas för klustret.
    
        Följande exempel visar hur du skapar ett nytt AKS-kluster med python SDK:

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

        Om du har ett befintligt AKS-kluster som redan ligger bakom det virtuella nätverket kan du använda informationen i artikeln [distribuera till Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Använd följande Azure CLI-kommandon för att hitta tjänstens huvud namn eller hanterat identitets-ID för AKS. Ersätt `<aks-cluster-name>` med namnet på klustret. Ersätt `<resource-group-name>` med namnet på den resurs grupp som _innehåller AKS-klustret_:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
        ``` 

        Om det här kommandot returnerar värdet `msi` använder du följande kommando för att identifiera ägar-ID: t för den hanterade identiteten:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
        ```
    1. Använd följande kommando för att hitta ID: t för den resurs grupp som innehåller ditt virtuella nätverk. Ersätt `<resource-group-name>` med namnet på den resurs grupp som _innehåller det virtuella nätverket_:

        ```azurecli-interactive
        az group show -n <resource-group-name> --query id
        ```

    1. Använd följande kommando om du vill lägga till tjänstens huvud namn eller hanterade identitet som en nätverks deltagare. Ersätt `<SP-or-managed-identity>` med det ID som returnerades för tjänstens huvud namn eller hanterad identitet. Ersätt `<resource-group-id>` med det ID som returnerades för resurs gruppen som innehåller det virtuella nätverket:

        ```azurecli-interactive
        az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
        ```

    1. Om du vill uppdatera AKS-klustret till att använda en __privat belastningsutjämnare__använder du python SDK. Följande kodfragment visar hur du uppdaterar ett befintligt AKS-kluster som har lagts till eller kopplats till arbets ytan:
    
        ```python
        import azureml.core
        from azureml.core.compute.aks import AksUpdateConfiguration
        from azureml.core.compute import AksCompute

        # ws = workspace object. Creation not shown in this snippet
        aks_target = AksCompute(ws,"myaks")

        # Change to the name of the subnet that contains AKS
        subnet_name = "default"
        # Update AKS configuration to use an internal load balancer
        update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
        aks_target.update(update_config)
        # Wait for the operation to complete
        aks_target.wait_for_completion(show_output = True)
        ```

* Så här __kopplar du AKS med en privat slut punkt__:

    1. Använd följande Azure CLI-kommando för att hämta __under nätets ID__ för under nätet som AKS-klustret ska använda. Till exempel standard under nätet för det virtuella nätverket:
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        Det här kommandot returnerar en matris med ID: n för under näten i det virtuella nätverket. Följande JSON är ett exempel från ett virtuellt nätverk som bara har ett undernät:

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        Om flera ID: n returneras väljer du det du vill använda.

    1. Om du vill skapa ett AKS-kluster med en privat slut punkt använder du informationen i avsnittet __Avancerat nätverk__ i artikeln [skapa ett privat Azure Kubernetes service-kluster](/azure/aks/private-clusters#advanced-networking) . När du skapar klustret använder du under nätets ID från föregående kommando med `--vnet-subnet-id` parametern.

    1. Om du vill ansluta klustret använder du informationen i artikeln [distribuera till Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    > [!TIP]
    > När du använder den här konfigurationen och begränsar utgående kommunikation från AKS-klustret, behöver du inte tillåta kommunikation på portarna __1194__ eller __9000__. För andra portar som ska tillåtas, se rikt linjerna i artikeln [kontrollen utgående trafik för klusternoder i Azure Kubernetes service](/azure/aks/limit-egress-traffic) .

## <a name="use-azure-container-instances-aci"></a>Använda Azure Container Instances (ACI)

__Krav__

* Azure Container Instances skapas dynamiskt när du distribuerar en modell. Om du vill aktivera Azure Machine Learning att skapa ACI i det virtuella nätverket måste du aktivera __under näts delegering__ för under nätet som används av distributionen.

__Begränsningar__

* Det virtuella nätverket måste finnas i samma resurs grupp som din Azure Machine Learning-arbetsyta.

* Azure Container Registry (ACR) för din arbets yta kan inte också finnas i det virtuella nätverket.

__Konfiguration__

Använd följande steg för att använda ACI i ett virtuellt nätverk på din arbets yta:

1. Om du vill aktivera under näts delegering i det virtuella nätverket använder du informationen i artikeln [Lägg till eller ta bort en under näts delegering](../virtual-network/manage-subnet-delegation.md) . Du kan aktivera delegering när du skapar ett virtuellt nätverk eller lägga till det i ett befintligt nätverk.

    > [!IMPORTANT]
    > När du aktiverar delegering ska `Microsoft.ContainerInstance/containerGroups` du använda som värde för __tjänsten delegera till tjänst__ .

2. Distribuera modellen med [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-) `vnet_name` och Använd `subnet_name` parametrarna och. Ange de här parametrarna som namn på det virtuella nätverket och under nätet där du aktiverade delegering.

## <a name="azure-databricks"></a>Azure Databricks

__Krav__

* Det virtuella nätverket måste finnas i samma prenumeration och region som Azure Machine Learning-arbetsytan.
* Om Azure Storage kontona för arbets ytan också är skyddade i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som Azure Databricks-klustret.
* Förutom de __databricks-privata__ och __databricks-offentliga__ undernät som används av Azure Databricks, krävs även det __standard__ -undernät som skapats för det virtuella nätverket.

__Begränsningar__

__Konfiguration__

För detaljerad information om hur du använder Azure Databricks med ett virtuellt nätverk, se [distribuera Azure Databricks i Azure-Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuell dator eller HDInsight-kluster

__Krav__

* Azure Machine Learning stöder bara virtuella datorer som kör Ubuntu.
* SSH-porten måste vara aktive rad på den virtuella datorn eller HDInsight-klustret.

__Begränsningar__

__Konfiguration__

1. Skapa ett virtuellt dator kluster eller HDInsight-kluster med hjälp av Azure Portal eller Azure CLI och Lägg klustret i ett virtuellt Azure-nätverk. Mer information finns i följande artiklar:

    * [Skapa och hantera virtuella Azure-nätverk för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Utöka HDInsight med ett virtuellt Azure-nätverk](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. En NSG skapas automatiskt för Linux-baserade Azure-Virtual Machines. Den här NSG ger åtkomst till port 22 från vilken källa som helst. Om du vill begränsa åtkomsten till SSH-porten måste du tillåta åtkomst från Azure Machine Learning. Om du vill bevara åtkomsten för Azure ML måste du tillåta åtkomst från en __käll tjänst__ med ett __käll tjänst tag__ i __AzureMachineLearning__. Följande Azure CLI-kommandon ändrar till exempel SSH-regeln för att bara tillåta åtkomst från Azure Machine Learning.

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    Mer information finns i artikeln [skapa nätverks säkerhets grupper](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups) i artikeln Azure Virtual Networks for Linux Virtual Machines.
    
    Behåll standard reglerna för utgående trafik för nätverks säkerhets gruppen. Mer information finns i standard säkerhets regler i [säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

1. Anslut den virtuella datorn eller HDInsight-klustret till din Azure Machine Learning-arbetsyta. Mer information finns i [Konfigurera beräknings mål för modell träning](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Nästa steg

* [Använd Azure Machine Learning arbets yta bakom Azure-brandväggen](how-to-access-azureml-behind-firewall.md).
* [Konfigurera miljöer för utbildning](how-to-set-up-training-targets.md)
* [Konfigurera privata slut punkter](how-to-configure-private-link.md)
* [Välj var du vill distribuera modeller](how-to-deploy-and-where.md)
* [Använd TLS för att skydda en webb tjänst via Azure Machine Learning](how-to-secure-web-service.md)
