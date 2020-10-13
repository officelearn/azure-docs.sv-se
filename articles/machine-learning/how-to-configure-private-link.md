---
title: Konfigurera en privat slut punkt
titleSuffix: Azure Machine Learning
description: Använd Azures privata länk för att få säker åtkomst till din Azure Machine Learning-arbetsyta från ett virtuellt nätverk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828121"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Konfigurera en privat Azure-länk för en Azure Machine Learning-arbetsyta

I det här dokumentet får du lära dig hur du använder en privat Azure-länk med din Azure Machine Learning-arbetsyta. Information om hur du skapar ett virtuellt nätverk för Azure Machine Learning finns i [Översikt över virtuell nätverks isolering och sekretess](how-to-network-security-overview.md)

Med Azures privata länk kan du ansluta till din arbets yta med en privat slut punkt. Den privata slut punkten är en uppsättning privata IP-adresser i det virtuella nätverket. Du kan sedan begränsa åtkomsten till din arbets yta så att den bara sker över de privata IP-adresserna. Privat länk hjälper till att minska risken för data exfiltrering. Mer information om privata slut punkter finns i artikeln [Azure Private Link](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Azures privata länk påverkar inte Azures kontroll plan (hanterings åtgärder) som att ta bort arbets ytan eller hantera beräknings resurser. Till exempel skapa, uppdatera eller ta bort ett beräknings mål. De här åtgärderna utförs via det offentliga Internet som normalt. Data Plans åtgärder som att använda Azure Machine Learning Studio, API: er (inklusive publicerade pipeliner) eller SDK använder den privata slut punkten.
>
> Du kan stöta på problem vid försök att komma åt den privata slut punkten för din arbets yta om du använder Mozilla Firefox. Det här problemet kan vara relaterat till DNS via HTTPS i Mozilla. Vi rekommenderar att du använder Microsoft Edge av Google Chrome som en lösning.

## <a name="prerequisites"></a>Förutsättningar

Om du planerar att använda en privat länk aktive rad arbets yta med en kundhanterad nyckel måste du begära denna funktion med hjälp av ett support ärende. Mer information finns i [Hantera och öka kvoter](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Begränsningar

Det går inte att använda en Azure Machine Learning arbets yta med privat länk i Azure Government regioner eller Azure Kina 21Vianet-regioner.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Skapa en arbets yta som använder en privat slut punkt

Använd någon av följande metoder för att skapa en arbets yta med en privat slut punkt:

> [!TIP]
> Azure Resource Manager-mallen kan skapa ett nytt virtuellt nätverk om det behövs. Alla andra metoder kräver ett befintligt virtuellt nätverk.

# <a name="resource-manager-template"></a>[Resource Manager-mall](#tab/azure-resource-manager)

Azure Resource Manager-mallen på [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) ger ett enkelt sätt att skapa en arbets yta med en privat slut punkt och ett virtuellt nätverk.

Information om hur du använder den här mallen, inklusive privata slut punkter, finns i [använda en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning python SDK tillhandahåller klassen [PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) , som kan användas med [arbets ytan. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) för att skapa en arbets yta med en privat slut punkt. Den här klassen kräver ett befintligt virtuellt nätverk.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure CLI-tillägget för Machine Learning](reference-azure-machine-learning-cli.md) tillhandahåller kommandot [AZ ml arbets yta skapa](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) . Följande parametrar för det här kommandot kan användas för att skapa en arbets yta med ett privat nätverk, men det krävs ett befintligt virtuellt nätverk:

* `--pe-name`: Namnet på den privata slut punkt som skapas.
* `--pe-auto-approval`: Om de privata slut punkts anslutningarna till arbets ytan automatiskt ska godkännas.
* `--pe-resource-group`: Resurs gruppen som den privata slut punkten ska skapas i. Måste vara samma grupp som innehåller det virtuella nätverket.
* `--pe-vnet-name`: Det befintliga virtuella nätverket som den privata slut punkten ska skapas i.
* `--pe-subnet-name`: Namnet på det undernät som den privata slut punkten ska skapas i. Standardvärdet är `default`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

På fliken __nätverk__ i Azure Machine Learning Studio kan du konfigurera en privat slut punkt. Det kräver dock ett befintligt virtuellt nätverk. Mer information finns i [skapa arbets ytor i portalen](how-to-manage-workspace.md).

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Använda en arbets yta över en privat slut punkt

Eftersom kommunikation till arbets ytan endast tillåts från det virtuella nätverket måste alla utvecklings miljöer som använder arbets ytan vara medlemmar i det virtuella nätverket. Till exempel en virtuell dator i det virtuella nätverket.

> [!IMPORTANT]
> För att undvika tillfälligt avbrott i anslutningen rekommenderar Microsoft att tömma DNS-cachen på datorer som ansluter till arbets ytan när du har aktiverat privat länk. 

Information om Azure Virtual Machines finns i Virtual Machines- [dokumentationen](/azure/virtual-machines/).


## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skyddar din Azure Machine Learning-arbetsyta finns i artikeln om [isolering av virtuella nätverk och sekretess översikt](how-to-network-security-overview.md) .

* Om du planerar att använda en anpassad DNS-lösning i ditt virtuella nätverk, se [hur du använder en arbets yta med en anpassad DNS-Server](how-to-custom-dns.md).
