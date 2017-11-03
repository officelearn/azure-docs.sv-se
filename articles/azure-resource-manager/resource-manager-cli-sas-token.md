---
title: Distribuera Azure-mall med SAS-token och Azure CLI | Microsoft Docs
description: "Använd Azure Resource Manager och Azure CLI för att distribuera resurser till Azure från en mall som är skyddat med SAS-token."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 22387aadd8f53a65efb76a29a9403c46a2c25954
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Distribuera privata Resource Manager-mall med SAS-token och Azure CLI

När mallen finns i ett lagringskonto kan du begränsa åtkomsten till mallen och erbjuder en token för delad åtkomst-signatur (SAS) under distributionen. Det här avsnittet beskriver hur du använder Azure PowerShell med Resource Manager-mallar för att ange en SAS-token under distributionen. 

## <a name="add-private-template-to-storage-account"></a>Lägg till privata mall i storage-konto

Du kan lägga till dina mallar i ett lagringskonto och en länk till dem under distributionen med en SAS-token.

> [!IMPORTANT]
> Blob som innehåller mallen som är tillgänglig för endast kontoägaren genom att följa stegen nedan. Men när du skapar en SAS-token för blobben är blob tillgänglig för alla med den URI. Om en annan användare spärras URI: N, har som användare tillgång till mallen. Använda en SAS-token är ett bra sätt att begränsa åtkomst till dina mallar bör, men du inte känsliga data som lösenord direkt i mallen.
> 
> 

I följande exempel ställer in en behållare för privat lagring-konto och överför en mall:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Ange SAS-token under distributionen
Generera en SAS-token för att distribuera en privat mallen i ett lagringskonto och inkludera den i URI: N för mallen. Ange förfallotiden så att tillräckligt med tid att slutföra distributionen.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Ett exempel på hur du använder en SAS-token med länkade mallar finns [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Nästa steg
* En introduktion till att distribuera mallar finns [distribuera resurser med Resource Manager-mallar och Azure PowerShell](resource-group-template-deploy-cli.md).
* En fullständig exempelskript som distribuerar en mall finns [skript för distribuera Resource Manager-mallar](resource-manager-samples-cli-deploy.md)
* Om du vill definiera parametrar i mallen, se [Webbsidemallar](resource-group-authoring-templates.md#parameters).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).
